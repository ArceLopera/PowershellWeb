Powershell scripts allow you to store a command or series of commands, for easier execution later. To produce a script, it is enough to record the required commands in a text file with the extension .ps1 .
You can also use scripts to accomplish more complex tasks than are practical by using a single command. While it's technically possible to make a single Windows PowerShell command that's long and complex, it's impractical to manage. Placing complex tasks in a script makes editing simpler and easier to understand.

Reporting is one complex and repetitive task that you can do with Windows PowerShell. You can use Windows PowerShell to create text or HTML-based reports. For example, you can create a script that reports available disk space on your servers, or you can create a script for Exchange that scans the message tracking logs to report on mail flow statistics.

Scripts can also use constructs such as ForEach, If, and Switch, which are seldom used in a single command. You can use these constructs to process objects and make decisions in your scripts.

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
In case the script does not execute, it is necessary to change the script execution policy to a more permissive value, using the Set-ExecutionPolicy cmdlet as administrator. See the cmdlet help for more details. An execution policy is a safety feature. Like requiring the path of a script, a policy can stop you from doing unintentional things. You can set the policy on various levels, like the local computer, current user, or particular session. You can also use a Group Policy setting to set execution policies for computers and users.

``` pwsh
. ./Get-DiskInventory.ps1
```

To run a Windows PowerShell script at the Windows PowerShell prompt, you can use the following methods:

+ Enter the full path to the script; for example, C:\Scripts\MyScript.ps1.
+ Enter a relative path to the script; for example, \Scripts\MyScript.ps1.
+ Reference the current directory; for example, .\MyScript.ps1.

## Execution Policy

You can control whether Windows PowerShell scripts can be run on Windows computers. You do this task by setting the execution policy on the computer. The default execution policy on a computer varies depending on the operating system version. To be sure of the current configuration, you can use the Get-ExecutionPolicy cmdlet.

The options for the execution policy are:

+ Restricted. No scripts are allowed to be run.
+ AllSigned. Scripts can be run only if they're digitally signed.
+ RemoteSigned. Scripts that are downloaded can only be run if they're digitally signed.
+ Unrestricted. All scripts can be run, but a confirmation prompt displays when running unsigned scripts that are downloaded.
+ Bypass. All scripts are run without prompts.

Setting the script execution policy provides a safety net that can prevent untrusted scripts from being run accidentally. However, the execution policy can always be overridden.

You can set the execution policy on a computer by using the Set-ExecutionPolicy cmdlet. However, this setting is difficult to manage across many computers. When you configure the execution policy for many computers, you can use the Computer Configuration\Policies\Administrative Templates\Windows Components\Windows PowerShell\Turn on Script Execution Group Policy setting to override the local setting.

You can override the execution policy for an individual Windows PowerShell instance. This setting is useful if company policy requires the execution policy to be set as Restricted, but you still must run scripts occasionally. To override the execution policy, run PowerShell.exe with the -ExecutionPolicy parameter.

``` pwsh
Powershell.exe -ExecutionPolicy ByPass
```

If you've modified a script downloaded from the internet, the script still has the attributes that identify it as a downloaded file. To remove that status from a script, use the Unblock-File cmdlet.

### AppLocker

While the Windows PowerShell script execution policy provides a safety net for inexperienced users, it's not very flexible. When you set an execution policy, you can only check that the script was downloaded and that it's signed.

Another alternative for controlling the use of Windows PowerShell scripts is AppLocker. With AppLocker, you can set various restrictions that limit the running of specific scripts or scripts in specific locations. Also, unlike the AllSigned execution policy, AppLocker can allow scripts that are signed only by specific publishers.

