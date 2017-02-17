##SQl Server Setup AlWaysOn prep

## Security 
SQL Server Account : Role: sysadmin  
Permissions: Perform Volume Maintenance Tasks , Lock Pages in Memory 


Each SQL Node from AOAG need 3 nic's

# Network Adapter Configs

Primary “Intranet” adapter: 
SQL01 IP Address: 10.1.0.21
SQL02 IP Address: 10.1.0.22    

#Posh
 
$adapter = Get-NetAdapter "Ethernet 2" 
$adapter | Set-NetIPInterface -Dhcp Disabled 
$adapter | New-NetIPAddress -AddressFamily IPv4 -IPAddress 10.1.0.21 -PrefixLength 16 -Type Unicast -DefaultGateway 10.1.0.1 
Set-DnsClientServerAddress -InterfaceAlias $adapter.Name -ServerAddresses 10.2.0.10 
Rename-NetAdapter -Name "Ethernet 2" -NewName "Intranet" 


Secondary “Heartbeat” adapter:
SQL01 IP Address: 192.168.10.21 
SQL02 IP Address: 192.168.10.22   

#Posh
$adapter = Get-NetAdapter "Ethernet" 
$adapter | Set-NetIPAddress -Dhcp Disabled 
$adapter | New-NetIPAddress -AddressFamily IPv4 -IPAddress 192.168.10.21 -PrefixLength 24 -Type Unicast 
Rename-NetAdapter -Name "Ethernet" -NewName "Heartbeat" 

*Disable the DNS registration on the Heartbeat adapter for both servers 
  
Set-DnsClient -RegisterThisConnectionAddresss $false -InterfaceAlias "Heartbeat" 


#SQLServer Setup


[OPTIONS] 
ACTION="Install" 
FEATURES=SQLENGINE,REPLICATION,IS 
INSTANCENAME="MSSQLSERVER" 
SQLSVCACCOUNT=SQLKONF\s-sql 
SQLSVCPASSWORD="<Password>" 
SQLSYSADMINACCOUNTS="SQLKONF\SetupAdmin" 
IAcceptSQLServerLicenseTerms="True" 
QUIET="True" 
UpdateEnabled="False" 
ERRORREPORTING="False" 
INSTANCEDIR="M:\\Program Files\\Microsoft SQL Server\\" 
AGTSVCACCOUNT=SQLKONF\s-sql 
AGTSVCPASSWORD="<Password>" 
AGTSVCSTARTUPTYPE=Automatic 
SQLSVCSTARTUPTYPE=Automatic 
SQLTEMPDBDIR=T:\Data\ 
SQLTEMPDBLOGDIR=T:\Data\ 
SQLUSERDBDIR=M:\Data\ 
SQLUSERDBLOGDIR=L:\Logs\ 
ISSVCACCOUNT=SQLKONF\s-sql 
ISSVCPASSWORD="<Password>" 
ISSVCStartupType=Automatic 
TCPENABLED=1 
BROWSERSVCSTARTUPTYPE=Automatic 


# run sql Setup BIN

@ECHO OFF 
set CDRoot=D: 
@ECHO ON 
%CDRoot%\Setup.exe /ConfigurationFile=sqlconfig.ini /Q 

#AddOns
msiexec /i SharedManagementObjects.msi /passive /norestart 
msiexec /i SQLSysClrTypes.msi /passive /norestart 
msiexec /i PowerShellTools.msi /passive /norestart 

##AOG Setup

#Posh

Import-Module SQLPS -DisableNameChecking 
Enable-SqlAlwaysOn -Path SQLSERVER:\SQL\SQL01\DEFAULT -Force 
Enable-SqlAlwaysOn -Path SQLSERVER:\SQL\SQL02\DEFAULT -Force 

 
#BackUp Dir 
New-Item Z:\Backup -ItemType Directory 
New-SmbShare -Name Backup -Path Z:\Backup -FullAccess "Everyone" 
$acl = Get-Acl Z:\Backup 
$acl.SetAccessRuleProtection($true,$false) 
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("SPKONF\s-sql", "Modify", "ContainerInherit,ObjectInherit", "None", "Allow") 
$acl.AddAccessRule($rule) 
Set-Acl Z:\Backup $acl 

# 4 testing, AOtempDB

Invoke-SqlCmd "CREATE DATABASE AOTemp;" -ServerInstance SQL01 
 
New-SqlHADREndpoint -Path SQLSERVER:SQL\SQL01\DEFAULT -Name SPHADR -Port 5022 -Encryption Required 
New-SqlHADREndpoint -Path SQLSERVER:\SQL\SQL02\DEFAULT -Name SPHADR -Port 5022 -Encryption Required 
Set-SqlHADREndpoint -Path SQLSERVER:\SQL\SQL01\DEFAULT\Endpoints\SPHADR -State Started 
Set-SqlHADREndpoint -Path SQLSERVER:\SQL\SQL02\DEFAULT\Endpoints\SPHADR -State Online 
Backup-SqlDatabase -Database AOTemp -BackupFile Z:\Backup\AOTemp.bak -ServerInstance SQL01 -NoRecovery 
Backup-SqlDatabase -Database AOTemp -BackupFile Z:\Backup\AOTemp_Log.bak -ServerInstance SQL01 -BackupAction Log - NoRecovery 

