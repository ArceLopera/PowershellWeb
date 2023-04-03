When you create many scripts, you'll have snippets of code that you want to reuse. You'll also have snippets of code that you want to reuse within the same script. Rather than having the same code display multiple times in a script, you can create a function that displays once in the script, but is called multiple times. If you need to use the same code across multiple scripts, then you can put the function into a module that can be shared by multiple scripts.

When you call a function, you can pass data to it. You use the Param() block for a function in the same way as you do for a script. After the declaration for the function, insert the Param() block and the definitions for any variables that are expected to be passed to the function.

``` pwsh
Function Get-SecurityEvent {
   Param (
      [string]$ComputerName
   ) #end Param
   Get-EventLog -LogName Security -ComputerName -$ComputerName -Newest 10
}

```

To call the function within a script, use the following syntax:

```
Get-SecurityEvent -ComputerName LON-DC1
```

### Variable Scopes

|Scope|	Description|
| --- | --- |
|Global|	The global scope is for the Windows PowerShell prompt. Variables set at the Windows PowerShell prompt can be reviewed in all the scripts started at that Windows PowerShell prompt. Variables created at a Windows PowerShell prompt don't exist in other Windows PowerShell prompts or in instances of the Windows PowerShell Integrated Scripting Environment (ISE).|
|Script|	The script scope is for a single script. Variables set within a script can be reviewed by all the functions within that script. If you set a variable value in the script scope that already exists in the global scope, a new variable is created in the script scope. There are then two variables of the same name in two separate scopes. At this point, when you review the value of the variable in the script, the value of the variable in the script scope is returned.|
|Function|	The function scope is for a single function. Variables set within a function aren't shared with other functions or the script. If you set a variable value in the function scope that already exists in the global or script scope, a new variable is created in the function scope. Then, there are two variables of the same name in two separate scopes.|

In addition to reviewing a variable in a higher-level scope, you can also modify that variable by specifically referencing the scope of the variable when you modify it. To modify a script scope variable from a function, use the following syntax:

``` pwsh
$script:var = "Modified from function"
```

It's a best practice to avoid modifying variables between scopes, because doing so can cause confusion. Instead, set the script scope variable equal to the output of the function. If the data in the function is in a variable, you can use Return() to pass it back to the script.

The following is an example of using Return() at the end of a function to pass a variable value back to the script scope:

``` pwsh
Return($users)
```

Using Return() in a function adds the specified data to the pipeline of data being returned, but doesn't replace existing data in the pipeline. As part of script development, you need to verify exactly which data is being returned by a function.

