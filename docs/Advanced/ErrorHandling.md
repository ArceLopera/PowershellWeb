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