Both Windows PowerShell and PowerShell Core support cmdlets that you can use to manage all aspects related to network settings on Windows devices. Settings that you can configure with PowerShell include TCP/IP, Domain Name System (DNS), firewall, and routing table configurations. In addition to the cmdlets for managing network features and components, the Test-NetConnection cmdlet is also available. This cmdlet offers the same functionality as traditional command-line interface tools such as **ping.exe** and **tracert.exe**, which are used to identify and diagnose network connectivity and configuration settings.

## Manage IP Addresses

PowerShell includes the NETTCPIP module, which consists of TCP/IP-specific cmdlets used to manage network settings for Windows servers and devices. You can use the NETTCPIP cmdlets to add, remove, change, and validate IP address settings.

IP address management cmdlets use the noun "NetIPAddress" in their names. You can also find them by using the Get-Command command with the -Module NetTCPIP parameter.

|Cmdlet|	Description|
| --- | --- |
|New-NetIPAddress|	Creates a new IP address|
|Get-NetIPAddress|	Displays properties of an IP address|
|Set-NetIPAddress|	Modifies properties of an IP address|
|Remove-NetIPAddress|	Deletes an IP address|

## Creating new IP address settings
The New-NetIPAddress cmdlet requires an IPv4 or IPv6 address and either the alias or index of a network interface. As a best practice, you should also set the default gateway and subnet mask at the same time.

|Parameter|	Description|
| --- | --- |
|-IPAddress|	Defines the IPv4 or IPv6 address to create|
|-InterfaceIndex|	Defines the network interface, by index, for the IP address|
|-InterfaceAlias|	Defines the network interface, by name, for the IP address|
|-DefaultGateway|	Defines the IPv4 or IPv6 address of the default gateway host|
|-PrefixLength|	Defines the subnet mask for the IP address|

The following command creates a new IP address on the Ethernet interface:

``` pwsh
New-NetIPAddress -IPAddress 192.168.1.10 -InterfaceAlias "Ethernet" -PrefixLength 24 -DefaultGateway 192.168.1.1
```
The New-NetIPAddress cmdlet also accepts the –AddressFamily parameter, which defines either the IPv4 or IPv6 IP address family. If you don't use this parameter, the address family property is detected automatically.


PowerShell includes the NetTCPIP module, which consists of TCP/IP-specific cmdlets used to manage network settings for Windows servers and devices. You can use these cmdlets to add, remove, change, and validate IP address settings.