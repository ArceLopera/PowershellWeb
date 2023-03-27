Scripts can be documented in such a way that they provide help similar to that provided by Powershell. The documented script would look like this:

``` pwsh
<#
.SYNOPSIS
Get-DiskInventory gets logical disk information for one or more computers.
.DESCRIPTION
Get-DiskInventory uses CIM to query the instances Win32_LogicalDisk from one or more computers. Display for each disk the drive letter, free space, total space, and percentage
of free space.
.PARAMETER DriveType
The type of unit to query. View the Win32_LogicalDisk documentation for more information. 3 indicates a fixed disk, and is the default.
.EXAMPLE
Get-DiskInventory -DriveType 3
#>

param(
  $DriveType = 3
)

Get-CimInstance -Class Win32_logicaldisk `
 -filter "drivetype=$DriveType" |
 Sort-Object-Property DeviceID |
 Select-Object-Property DeviceID,
 @{n='FreeSpace(MB)';e={$_.FreeSpace / 1MB -as [int]}},
 @{n='Size(GB)';e={$_.Size / 1GB -as [int]}},
 @{n='%Free';e={$_.FreeSpace / $_.Size * 100 -as [int]}}

```

Comments can be specified with a # symbol before each comment line, or with the notation <# #> if there are several consecutive lines of comments.
A slight change was also made to the script: instead of using the Format-Table cmdlet, Select-Object is used, so as not to produce a formatted table as output. Using Select-Object the final output of the script is an object, so the end user can chain the script with other commands using the pipeline.