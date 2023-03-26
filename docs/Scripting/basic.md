Powershell scripts allow you to store a command or series of commands, for easier execution later. To produce a script, it is enough to record the required commands in a text file with the extension .ps1 .
For example, consider the following command, which prints a list of fixed disks attached to the system, along with their free space, total size, and free space percentage:
``` pwsh
Get-CimInstance Win32_logicaldisk `
 -filter "drivetype=3" |
 Sort-Object -Property DeviceID |
 format-table -Property DeviceID,
 @{n='FreeSpace(MB)';e={$_.FreeSpace / 1MB -as [int]}},
 @{n='Size(GB)';e={$_.Size / 1GB -as [int]}},
 @{n='%Free';e={$_.FreeSpace / $_.Size * 100 -as [int]}}
```
At the end of the first line the backtick ` is used to indicate that the command has not finished yet. In the rest of the lines, this job is done by vertical bars and commas. This way it is easier to read.
By copying these lines of code and saving them to a text file called Get-DiskInventory.ps1, a script will be created. 

## Parameterization

Scripts can be parameterized, that is, parameters can be created that can be specified at the time the script is executed. For example, in the script of the previous example it is possible to return parameters the name of the computer and the type of unit.
The parameterized script would look like this:

``` pwsh
param(
  $DriveType = 3
)

Get-CimInstance Win32_logicaldisk `
  -filter "drivetype=$DriveType" |
  Sort-Object-Property DeviceID |
  format-table -Property DeviceID,
  @{n='FreeSpace(MB)';e={$_.FreeSpace / 1MB -as [int]}},
  @{n='Size(GB)';e={$_.Size / 1GB -as [int]}},
  @{n='%Free';e={$_.FreeSpace / $_.Size * 100 -as [int]}}
```
Optionally, each parameter can be specified with a default value, as seen in the example above.

## Run the script

The script can be executed by typing its name in the console. If the script is saved in a different directory than the current one, the full path to the file must be written to execute it.
In case the script does not execute, it is necessary to change the script execution policy to a more permissive value, using the Set-ExecutionPolicy cmdlet as administrator. See the cmdlet help for more details.

``` pwsh
. ./Get-DiskInventory.ps1
```

Be sure to include the dot (.) at the beginning of the command. This tells PowerShell to run the script or file that's being called.