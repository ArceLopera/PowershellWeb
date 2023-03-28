
Adding the line [CmdletBinding()] after the help comments makes the script advanced, which allows you to return required parameters, set their data type, add aliases for parameters, etc.
The final version of the example script is as follows:
``` pwsh
<#
.SYNOPSIS
Get-DiskInventory gets logical disk information for one or more computers.
.DESCRIPTION
Get-DiskInventory uses CIM to query the instances Win32_LogicalDisk from one or more computers. Display for each disk the drive letter, free space, total space, and percentage of free space.
.PARAMETER DriveType
The type of unit to query. View the Win32_LogicalDisk documentation for more information. 3 indicates a fixed disk, and is the default.
.EXAMPLE
Get-DiskInventory -DriveType 3
#>
[CmdletBinding()]
param(
  [Parameter(Mandatory=$True)]
  [Alias('Type')]
  [ValidateSet(2,3)]
  [int]$DriveType = 3
)

Get-CimInstance Win32_logicaldisk `
-filter "drivetype=$DriveType" |
 Sort-Object-Property DeviceID |
 Select-Object-Property DeviceID,
 @{n='FreeSpace(MB)';e={$_.FreeSpace / 1MB -as [int]}},
 @{n='Size(GB)';e={$_.Size / 1GB -as [int]}},
 @{n='%Free';e={$_.FreeSpace / $_.Size * 100 -as [int]}}

```

The changes from the previous example are as follows:

+ The directive to return the script advanced is included
+ The parameter is now required, and the alias Type is created for it.
+ The parameter is declared as an integer, and the only valid values ​​it receives are 2 and 3.