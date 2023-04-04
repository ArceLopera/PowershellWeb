
The pipeline in PowerShell is a feature that allows you to take the output of one command and pass it as input to another command. This makes it possible to create more complex commands by chaining simple commands together. The pipeline operator, represented by the "|" character, is used to connect commands together.

When using the Windows PowerShell pipeline, you can pass data through the pipeline and perform operations on it. This capability lets you perform many bulk operations such as:

+ Querying a list of objects.
+ Filtering the objects.
+ Modifying the objects.
+ Displaying the data.

Here are some examples of how to use the pipeline in PowerShell:

**Example 1: Get-ChildItem and Select-Object**

The Get-ChildItem command is used to list the files and folders in a specified directory. The Select-Object command is used to select specific properties of the objects returned by Get-ChildItem. By using the pipeline, you can pass the output of Get-ChildItem to Select-Object to filter the results.

``` pwsh 
Get-ChildItem | Select-Object Name, Length, LastWriteTime
```

This command lists the files and folders in the current directory and then selects the Name, Length, and LastWriteTime properties of each item.

**Example 2: Get-Process and Sort-Object**

The Get-Process command is used to list the running processes on a system. The Sort-Object command is used to sort the results based on a specific property. By using the pipeline, you can pass the output of Get-Process to Sort-Object to sort the results by CPU usage.

``` pwsh
Get-Process | Sort-Object CPU -Descending
```

This command lists the running processes and then sorts them in descending order based on their CPU usage.

**Example 3: Get-Service and Where-Object**

The Get-Service command is used to list the services on a system. The Where-Object command is used to filter the results based on a specific condition. By using the pipeline, you can pass the output of Get-Service to Where-Object to filter the results to only show services that are currently running.

```
Get-Service | Where-Object Status -eq "Running"
```

This command lists all of the services on the system and then filters the results to only show services that have a Status property equal to "Running".

In each of these examples, the pipeline is used to pass the output of one command to another command. This allows you to create more powerful commands by combining simple commands together.

## Order & Selection

The pipeline can be used to select properties of objects that are not displayed by default when using a cmdlet. It can also be used to filter results (using search criteria).

To sort, the Sort-Object cmdlet (abbreviated sort) is used, and to select properties, Select-Object (abbreviated select) is used.

For example, the process list is normally displayed in alphabetical order by process name. To sort it by Process ID, the command is:

``` pwsh
get-process | sort id
```

To sort it by virtual memory usage in descending order:

``` pwsh
get-process | sort vm -desc
```

Note that in this last example, it is sorting by a field that is not normally displayed on the screen. To change the fields that are displayed, select is used:

``` pwsh
get-process | select -property id,name,vm | sort vm -desc
```

This example shows a table with the identifier, name, and virtual memory usage of the machine's processes, ordered in descending order by virtual memory usage.

Remember that to view the properties of an object (the fields that can be displayed), the Get-Member cmdlet (abbreviated gm) is used:

``` pwsh
Get-Process | gm
```
**Best Practice**: Always review the property names in the output of Get-Member before you use those property names in another command. By doing this, you can help to ensure that you use the actual property names and not ones created for display purposes.
## Passing parameters

In a pipeline of the type:

Command_A | Command_B

...parameters can be passed in two ways:

### By value (ByValue) 
In this case, PowerShell analyzes the output type of Command_A and determines which parameter of Command_B can receive this output.

**Example: Analyzing the command**

``` pwsh
Get-Process | Stop-Process
```

In this case, Get-Process produces Process type objects. Examining the help for Stop-Process, the following parameter is found:

```
-InputObject <Process[]>
    Specifies the process objects to stop. Enter a variable that contains the
    objects, or type a command or expression that gets the objects.
    Required?                    true
    Position?                    0
    Default value                None
    Accept pipeline input?       True (ByValue)
    Accept wildcard characters?  false
```

As you can see, this parameter can receive values from the pipeline, using the ByValue method. For this reason, the command works.

If you try to connect two commands to the pipeline that do not have compatible output and parameter types, an error occurs. It can also happen that data is passed through the wrong parameter. For example, suppose the file computers.txt contains the names of several computers, and you want to display the list of services on each of these computers. You could use the command:

``` pwsh
Get-Content computers.txt | get-service
```

This command produces an error because the required parameter (ComputerName) does not accept input through the pipeline using the ByValue method.

The command can then be executed using parentheses:

``` pwsh
get-service -computername (get-content computers.txt)
```

### By parameter name (ByPropertyName)

In this method, you must specify the parameter names. For example, consider a file called alias.txt with the following content:

```
Name,Value
np,notepad
sel,Select-Object
go,Invoke-Command
```

The idea is to use the contents of this file to input it to the new-alias command and create the aliases listed in the file.

Note that the first line of the file corresponds to the column headers. If this file is imported with Import-Csv, the following is obtained:

``` pwsh
PS C:\Users\Usuario\powershell> Import-Csv .\alias.txt
```

```
Name Value
---- -----         
np notepad
sel Select-Object
go Invoke-Command
```

If you use Get-Member to analyze this output, you get:

```
TypeName: System.Management.Automation.PSCustomObject

Name        MemberType   Definition                    
----        ----------   ----------                    
Equals      Method       bool Equals(System.Object obj)
GetHashCode Method       int GetHashCode()             
GetType     Method       type GetType()                
ToString    Method       string ToString()             
Name        NoteProperty string Name=np                
Value       NoteProperty string Value=notepad
```
It can be seen that the last two properties are of type String. Let's now analyze the parameters of the New-Alias command:

```
New-Alias [-Name] <String> [-Value] <String> [-Confirm] [-Description
<String>] [-Force] [-Option {None | ReadOnly | Constant | Private | AllScope |
Unspecified}] [-PassThru] [-Scope <String>] [-WhatIf] [<CommonParameters>]
```
The Name and Value parameters receive String inputs. And if you review the complete help, you can verify that both parameters receive values through the pipeline using the ByPropertyName mode:

```
-Name <String>
        Specifies the new alias. You can use any alphanumeric characters in an
        alias, but the first character cannot be a number.

        Required?                    true
        Position?                    0
        Default value                None
        Accept pipeline input?       True (ByPropertyName)
        Accept wildcard characters?  false

-Value <String>
        Specifies the name of the cmdlet or command element that is being aliased.

        Required?                    true
        Position?                    1
        Default value                None
        Accept pipeline input?       True (ByPropertyName)
        Accept wildcard characters?  false
```

Therefore, information can be passed from one command to another in the following way:

``` pwsh
import-csv alias.txt | new-alias
```

