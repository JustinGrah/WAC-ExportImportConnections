# WAC-ExportImportConnections
Simple RESTful PowerShell script which will sync your saved connections to a new Windows Admin Center instance.



## Documentation

### Prerequisites 
**Note** If you dont know what the Script does, you should probably not use it.

1. PowerShell
1. Two Running Windows Admin Center installtions
1. Internet connectivity between both instances of Windows Admin Center


### When shall I use this?
This Script's intention is to aid sysadmins / users in Syncing their Windows Admin Center connections from one Windows Admin Center instance to the other.
To keep an eye or remember where which connection came from, the script offers you to add custom tags to synced Windows Admin Center connections.

### Do I need to plan anything?
This script is not intrusive to anything. The only caveat is that you need to create a User-Bound GPO that runs on user login to sync up the WAC connections.
That can be:
* A scheduled task that runs automatically
* A GPO executed at logon
* Simple instruction to run the Script

