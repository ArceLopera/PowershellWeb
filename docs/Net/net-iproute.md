IP routing forwards data packets based on the destination IP address. This routing is based on routing tables, and while entries are made automatically, you might need to add, remove, or modify routing table entries manually. The NETTCPIP PowerShell module also includes cmdlets used to manage the routing table for Windows servers and devices.

The cmdlets for managing routing table entries have the noun “NetRoute” in the names.

|Cmdlet|	Description|
| --- | --- |
|New-NetRoute|	Creates an entry in the IP routing table|
|Get-NetRoute|	Retrieves an entry from the IP routing table|
|Set-NetRoute|	Modifies properties of an entry in the IP routing table|
|Remove-NetRoute|	Deletes an entry from the IP routing table|
|Find-NetRoute|	Identifies the best local IP address and route to reach a remote address|

## Creating an IP routing table entry
You can use the New-NetRoute cmdlet to create routing table entries on a Windows computer. The New-NetRoute cmdlet requires you to identify the network interface and destination prefix.

|Parameter|	Description|
| --- | --- |
|‑DestinationPrefix|	Defines the destination prefix of an IP route|
|‑InterfaceAlias|	Defines the network interface, by alias, for an IP route|
|‑InterfaceIndex|	Defines the network interface, by index, for an IP route|
|‑NextHop|	Defines the next hop for an IP route|
|‑RouteMetric|	Defines the route metric for an IP route|

The following command creates an IP routing table entry:

``` pwsh
New-NetRoute -DestinationPrefix 0.0.0.0/24 -InterfaceAlias "Ethernet" -DefaultGateway 192.168.1.1
```

The NetTCPIP PowerShell module also includes cmdlets used to manage the routing table for Windows servers and devices. They have the noun “NetRoute” in their names.