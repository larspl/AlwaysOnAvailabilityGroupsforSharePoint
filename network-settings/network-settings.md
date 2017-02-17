 
##SharePoint 2016 Network Firewall Settings
 
#Service  TCP Port  UDP Port  Protocols 
* Distributed Cache  TCP:22233, 22236  UDP:no  Req: ICMP (ping) 
* People Picker  TCP:53, 88, 135, 137 - 139, 389, 445, 636, 749, 750, 3268, 3269
* Sandbox Service 32846 
* Search Crawler  Web Application Ports Used TCP:80, 443 
* Search Index  TCP:16500 - 16519
* Service Applications  TCP:32843, 32844
* SQL Server  TCP:1433, 5022 (AlwaysOn)  UDP:1434 
* SMTP TCP:25
* WCF Services TCP:808 
* User Profile Service  TCP:53, 88, 389, 5725, 1025 - 5000, 49152 - 65536  UPD:53, 88, 389, 464  


## Plan security hardening for SharePoint 2013
https://technet.microsoft.com/en-us/library/cc262849.aspx

## Configure SQL Server security for SharePoint 2013 environments
http://technet.microsoft.com/en-us/library/ff607733.aspx#proc1

## Blocking the standard SQL Server ports
http://technet.microsoft.com/en-us/library/cc262849.aspx#BlockingSQL

## Service application communication
http://technet.microsoft.com/en-us/library/cc262849.aspx#ServiceApp

## User Profile service hardening requirements
http://technet.microsoft.com/en-us/library/cc262849.aspx#UserProfile

## Set-SPServiceHostConfig
http://technet.microsoft.com/en-us/library/ff607922.aspx

## Get-SPServiceHostConfig
http://technet.microsoft.com/en-us/library/ff607794.aspx

## TCP/IP Communications (Windows Server AppFabric Caching)
http://msdn.microsoft.com/en-us/library/ee790914(v=azure.10).aspx

