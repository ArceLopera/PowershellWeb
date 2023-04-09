The Web server role includes Internet Information Services (IIS), which is commonly used to manage websites and web-based applications. IIS supports PowerShell cmdlets to allow you to configure and manage application pools, websites, web applications, and virtual directories.

IIS management cmdlets are available in the IISAdministration module for PowerShell and have the prefix “IIS” in the noun part of their names. Sites use the noun “IISSite”.

To manage web-based applications, you can use the WebAdministration module for PowerShell, which includes cmdlets for managing web applications. These cmdlets use the noun "WebApplication". Cmdlets for managing application pools use the noun “WebAppPool”.

The WebAdministration module has mostly been replaced by updated features included in the IISAdministration module. For any IIS-related management tasks, it's recommended to use the IISAdministration module.

|Cmdlet|	Description|
| --- | --- |
|New-IISSite|	Creates a new IIS website|
|Get-IISSite|	Gets properties and configuration information about an IIS website|
|Start-IISSite|	Starts an existing IIS website on the IIS server|
|Stop-IISSite|	Stops an IIS website|
|New-WebApplication|	Creates a new web application|
|Remove-WebApplication|	Deletes a web application|
|New-WebAppPool|	Creates a new web application pool|
|Restart-WebAppPool|	Restarts a web application pool|


The IISAdministration module for PowerShell includes IIS management cmdlets, while the WebAdministration module for PowerShell includes cmdlets for managing web applications.