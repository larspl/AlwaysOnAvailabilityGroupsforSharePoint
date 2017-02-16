 
##SharePoint 2016 Network Firewall Settings
 
#Service  TCP Port  UDP Port  Protocols 
* Distributed Cache  TCP:22233, 22236  UDP:no  Req: ICMP (ping) 
* People Picker  TCP:53, 88, 135, 137 - 139, 389, 445, 636, 749, 750, 3268, 3269
* Sandbox Service 32846 
* Search Crawler  Web Application Ports Used (TCP:80, 443) 
* Search Index  TCP:16500 - 16519
* Service Applications  TCP:32843, 32844
* SQL Server  TCP:1433  UDP:1434
* SMTP TCP:25
* WCF Services TCP:808 
* User Profile Service  TCP:53, 88, 389, 5725, 1025 - 5000, 49152 - 65536  UPD:53, 88, 389, 464  


## Plan security hardening for SharePoint 2013
https://technet.microsoft.com/en-us/library/cc262849.aspx

 