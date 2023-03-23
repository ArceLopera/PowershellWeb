## Requesting Info 

The Read-Host cmdlet displays a request message, and then reads a response from the user. For example:

``` pwsh
Read-Host "Enter a computer name"
```
```
Enter a computer name: SERVER
SERVER
```
The cmdlet adds a colon (:) after the request message, and places the user's response in the pipeline. In most cases, the user's response is captured in a variable, as in the following example:
``` pwsh
$computer = Read-Host "Enter a computer name"
Enter a computer name: SERVER
```

## Writing Info

To write data to the console:

+ Write-Host: Allows you to write data directly to the console, skipping the pipeline. For this reason, its output cannot be captured or redirected to logs. It is recommended not to use it.
+ Write-Output: Allows data to be written to the pipeline. If there is no redirection to another command, the data will be output to the screen. This is the recommended way to print data to the console.
+ Other ways:
    Powershell has other cmdlets to produce console output. None of them use the pipeline (they function as Write-Host), but their output can be suppressed at will, through the use of environment variables. The commands are the following:

|Command| Description|
| --- | --- |
|Write-Warning| Prints the message in orange, preceded by the word WARNING. Its printing depends on the value of the $WarningPerference variable, which defaults to Continue.|
|Write-Verbose| Prints the message in blue, preceded by the word VERBOSE. Its printing depends on the value of the $VerbosePreference variable, which is set to SilentlyContinue by default.|
|Write-Debug| Prints the message in blue, preceded by the word DEBUG. Its printing depends on the value of the $DebugPreference variable, which defaults to SilentlyContinue.|
|Write-error| Generates an error message. Its printing depends on the value of the $ErrorActionPreference variable, which defaults to Continue.|

If the variable corresponding to the command has the value Continue, the message is printed. If the value is SilentlyContinue, the printing of the message is suppressed.