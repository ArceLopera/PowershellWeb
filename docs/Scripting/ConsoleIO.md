To enhance the usability of your scripts, you must learn how to accept user input. This skill allows you to create scripts that can be used for multiple purposes. In addition, accepting user input allows you to create scripts that are easier for others to use. 


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

You can combine a Write-Host command with Read-Host to display text and avoid a colon being appended, as the following example depicts:

``` pwsh
Write-Host "How many days? " -NoNewline
$answer = Read-Host
```

Input from Read-Host is limited to 1022 characters.

You can mask the input users enter at the prompt by using the -MaskInput or -AsSecureString parameters. Both parameters cause the characters entered by the user to display as asterisks (*). When -MaskInput is used, the response is collected as a String object. When -AsSecureString is used, the response is collected as a SecureString object. A SecureString object is required for scenarios such as setting passwords, where data shouldn't be stored as clear text in memory.

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

## Out-GridView

Out-GridView is primarily used to review data. However, you can also use Out-GridView to create a simple menu selection interface. When the user makes one or more selections in the window presented by Out-GridView, the data for those objects is either passed further through the pipeline or placed into a variable.

``` pwsh
$selection = $users | Out-GridView -PassThru
```

In the previous example, an array of user accounts is piped to Out-GridView. Out-GridView displays the user accounts on screen, and the user can select one or more rows in the Out-GridView window. When the user selects OK, the selected rows are stored in the $selection variable. You can then perform further processing on the users’ accounts.

To retain more control over the amount of data that users can select, you can use the -OutputMode parameter instead of the -PassThru parameter. The following table depicts the values that can be defined for the -OutputMode parameter.

### -OutputMode parameter

|Value|	Description|
| --- | --- |
|None|	This is the default value that doesn't pass any objects further down the pipeline.|
|Single|	This value allows users to select zero rows or one row in the Out-GridView window.|
|Multiple|	This value allows users to select zero rows, one row, or multiple rows in the Out-GridView window. This value is equivalent to using the -PassThru parameter.|  

Because users aren't forced to select a row in the Out-GridView window, you must ensure that your script properly handles the scenario where a row isn't selected.