As you develop more scripts, you'll find that efficient troubleshooting makes your script development much faster. A large part of efficient troubleshooting is understanding error messages. For some difficult problems, you can use breakpoints to stop a script partially through running to query values for variables.

## Try/Catch/Finally

``` pwsh
Try {
   # Do something with a file.
} Catch [System.IO.IOException] {
   Write-Host "Something went wrong"
}  Catch {
   # Catch all. It's not an IOException but something else.
} Finally {
   # Clean up resources.
}

```

## Inspecting errors

 An exception object contains:

1. A message. The message tells you in a few words what went wrong.

2. The stacktrace. The stacktrace tells you which statements ran before the error. Imagine you have a call to function A, followed by B, followed by C. The script stops responding at C. The stacktrace will show that chain of calls.

3. The offending row. The exception object also tells you which row the script was running when the error occurred. This information can help you debug your code.

So how do you inspect an exception object? There's a built-in variable, $_, that has an exception property. To get the error message, for example, you would use $_.exception.message.

``` pwsh
Try {
     # Do something with a file.
   } Catch [System.IO.IOException] {
     Write-Host "Something IO went wrong: $($_.exception.message)"
   }  Catch {
     Write-Host "Something else went wrong: $($_.exception.message)"
   }
```

## Raising errors

In some situations, you might want to cause an error:

* Non-terminating errors. For this type of error, PowerShell just notifies you that something went wrong, by using the Write-Error cmdlet, for example. The script continues to run. That might not be the behavior you want. To raise the severity of the error, you can use a parameter like -ErrorAction to cause an error that can be caught with Try/Catch, like so:
    ``` pwsh
    Try {
        Get-Content './file.txt' -ErrorAction Stop
    } Catch {
        Write-Error "File can't be found"
    }
    ```

By using the -ErrorAction parameter and the value Stop, you can cause an error that Try/Catch can catch.

* Business rules. You might have a situation where the code doesn't actually stop responding, but you want it to for business reasons. Imagine you're sanitizing input and you check whether a parameter is a path. A business requirement might specify only certain paths are allowed, or the path needs to look a   certain way. If the checks fail, it makes sense to throw an error. In a situation like this, you can use a Throw block:
    ``` pwsh
    Try {
        If ($Path -eq './forbidden') 
        {
            Throw "Path not allowed"
        }
        # Carry on.

    } Catch {
    Write-Error "$($_.exception.message)" # Path not allowed.
    }
    ```

    In general, don't use Throw for parameter validation. Use validation attributes instead. If you can't make your code work with these attributes, a Throw might be OK.

## $ErrorActionPreference

Windows PowerShell has a built-in, global variable named $ErrorActionPreference. When a command generates a non-terminating error, the command checks that variable to decide what it should do. The variable can have one of the following four possible values:

+ **Continue** is the default, and it tells the command to display an error message and continue to run.
+ **SilentlyContinue** tells the command to display no error message, but to continue running.
+ **Inquire** tells the command to display a prompt asking the user what to do.
+ **Stop** tells the command to treat the error as terminating and to stop running.

``` pwsh
$ErrorActionPreference = 'Inquire'
```
Be selective about using SilentlyContinue for $ErrorActionPreference. You might think that this makes your script better for users, but it could make troubleshooting difficult.

If you intend to trap an error within your script so that you can manage the error, commands must use the Stop action. You can trap and manage only terminating errors.

## -ErrorAction parameter

All Windows PowerShell commands have the –ErrorAction parameter. This parameter has the alias –EA. The parameter accepts the same values as $ErrorActionPreference, and the parameter overrides the variable for that command. If you expect an error to occur on a command, you use –ErrorAction to set that command’s error action to Stop. Doing this lets you trap and manage the error for that command but leaves all other commands to use the action in $ErrorActionPreference. An example is:

``` pwsh
Get-WmiObject -Class Win32_BIOS -ComputerName LON-SVR1,LON-DC1 -ErrorAction Stop
```

The only time that you'll modify $ErrorActionPreference is when you expect an error outside of a Windows PowerShell command, such as when you're running a method such as the following

``` pwsh
Get-Process –Name Notepad | ForEach-Object { $PSItem.Kill() }
```
In this example, the Kill() method might generate an error. But because it's not a Windows PowerShell command, it doesn't have the –ErrorAction parameter. You would instead set $ErrorActionPreference to Stop before running the method, and then set the variable back to Continue after you run the method.
## Breakpoints

A breakpoint pauses a script and provides an interactive prompt. At the interactive prompt, you can query or modify variable values and then continue the script. You use breakpoints to troubleshoot scripts when they aren't behaving as expected.

At a Windows PowerShell prompt, you can set breakpoints by using the Set-PSBreakPoint cmdlet. Breakpoints can be set based on the line of the script, a specific command being used, or a specific variable being used.

``` pwsh
Set-PSBreakPoint -Script "MyScript.ps1" -Line 23
```

The following example depicts how to set a breakpoint for a specific command:

``` pwsh
Set-PSBreakPoint -Command "Set-ADUser" -Script "MyScript.ps1"
```

When you set a breakpoint based on a command, you can include wildcards. For example, you could use the value *-ADUser to trigger a breakpoint for Get-ADUser, Set-ADUser, New-ADUser, and Remove-ADUser.

To set a breakpoint for a specific variable, do the following:

``` pwsh
Set-PSBreakPoint -Variable "computer" -Script "MyScript.ps1" -Mode ReadWrite
```

You can use the -Mode parameter for variables to identify whether you want to break when the variable value is read, written, or both. Valid values are Read, Write, and ReadWrite.

The default action for Set-PSBreakPoint is break, which provides the interactive prompt. However, you can use the -Action parameter to specify code that runs instead. This allows you to perform complex operations such as evaluating variable values and only breaking if the value is outside a specific range.

Breakpoints are stored in memory rather than as part of the script. Breakpoints aren't shared between multiple Windows PowerShell prompts and are removed when the prompt is closed.

Microsoft Visual Studio Code allows you to set and use breakpoints with more advanced options and you can configure conditional breakpoints that are triggered when variables are outside of a range or match a specific value.

Information about variable contents is also easier to find in Visual Studio Code. After a breakpoint is triggered and you're in the debugger, there's a variables section that displays the contents of variable so that you don't need to interrogate them.