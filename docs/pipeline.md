
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

When data is passed using ByValue, a parameter can accept complete objects from the pipeline when those objects are of the type that the parameter accepts. A single command can have more than one parameter accepting pipeline input ByValue, but each parameter must accept a different kind of object.

For example, Get-Service can accept pipeline input ByValue on both its –InputObject and –Name parameters. Each of those parameters accepts a different kind of object. –InputObject accepts objects of the type ServiceController, and –Name accepts objects of the type String. Consider the following example:

``` pwsh
'BITS','WinRM' | Get-Service
```
Here, two string objects are piped into Get-Service. They attach to the –Name parameter because that parameter accepts that kind of object, ByValue, from the pipeline.

#### Generic object types
Windows PowerShell recognizes two generic kinds of object, Object and PSObject. Parameters that accept these kinds of objects can accept any kind of object. When you perform ByValue pipeline parameter binding, Windows PowerShell first looks for the most specific object type possible. If the pipeline contains a String, and a parameter can accept String, that parameter will receive the objects.

If there's no match for a specific data type, Windows PowerShell will try to match generic data types. That behavior is why commands like Sort-Object and Select-Object work. Each of those commands has a parameter named –InputObject that accepts objects of the type PSObject from the pipeline ByValue. This is why you can pipe any type of object to those commands. Their –InputObject parameter will receive any object from the pipeline because it accepts objects of any kind.

### By parameter name (ByPropertyName)

If Windows PowerShell is unable to bind pipeline input by using the ByValue technique, it tries to use the ByPropertyName technique. When Windows PowerShell uses the ByPropertyName technique, it attempts to match a property of the object passed to a parameter of the command to which the object was passed. This match occurs in a simple manner. If the input object has a Name property, it will be matched with the parameter Name because they're spelled the same. However, it will only pass the property if the parameter is programmed to accept a value by property name. This means that you can pass output from one command to another when they don't logically go together.

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

#### Renaming properties
Most often, a property name from an output object doesn't match the name of an input parameter exactly. You can change the name of the property by using Select-Object and create a calculated property. For example, to view the processes running on all computers in your Windows Server Active Directory, try running the following command:

``` pwsh
Get-ADComputer -Filter * | Get-Process
```

However, this command doesn't work. No parameter for Get-Process matches a property name for the output of Get-ADComputer. View the output of Get-ADComputer | Get-Member and Get-Help Get-Process and you'll see that what you want is to match the Name property of Get-ADComputer with the -ComputerName parameter of Get-Process. You can do that by using Select-Object and changing the property name for the Get-ADComputer command’s Name property to ComputerName, and then passing the results to Get-Process. The following command will work:

``` pwsh
Get-ADComputer -Filter * | Select-Object @{n='ComputerName';e={$PSItem.Name}} | Get-Process
```

Remember: Windows PowerShell will always try ByValue first and will use ByPropertyName only if ByValue fails.

## Parenthetical Commands

Another option for passing the results of one command to the parameters of another is by using parenthetical commands. A parenthetical command is a command that is enclosed in parentheses. Just as in math, parentheses tell Windows PowerShell to execute the enclosed command first. The parenthetical command runs, and the results of the command are inserted in its place.

You can use parenthetical commands to pass values to parameters that do not accept pipeline input. This means you can have a pipeline that includes data inputs from multiple sources. Consider the following command:

``` pwsh
Get-ADGroup "London Users" | Add-ADGroupMember -Members (Get-ADUser -Filter {City -eq 'London'})
```

In this example, output of Get-ADGroup passes to Add-ADGroupMember, telling it which group to modify. However, that is only part of the information needed. We also need to tell Add-ADGroupMember what users to add to the group. The -Members parameter does not accept piped input, and even if it did, we have already piped data to the command. Therefore, we need to use a parenthetical command to provide a list of users that we want added to the group.

Parenthetical commands do not rely on pipeline parameter binding. They work with any parameter if the parenthetical command produces the kind of object that the parameter expects.

### Expand Property Values

You can use parenthetical commands to provide parameter input without using the pipeline. In some cases, however, you might have to manipulate the objects produced by a parenthetical command so that the command’s output is of the type that the parameter requires.

For example, you might want to list all the processes that are running on every computer in the domain. In this example, imagine that you have a very small lab domain that contains just a few computers. You can get a list of every computer in the domain by running the following command:

``` pwsh
Get-ADComputer –Filter *
```

However, this command produces objects of the type ADComputer. You couldn't use those objects directly in a parenthetical command such as in the following command:

``` pwsh
Get-Process –ComputerName (Get-ADComputer –Filter *)
```

The –ComputerName parameter expects objects of the type String. However, the parenthetical command doesn't produce String type objects. The –ComputerName parameter only wants a computer name. However, the command provides it an object that contains a name, an operating system version, and several other properties.

You could try the following command:

``` pwsh
Get-Process –ComputerName (Get-ADComputer –Filter * | Select-Object –Property Name)
```
This command selects only the Name property. This property is still a member of a whole ADComputer object. It's the Name property of an object. Although the Name property contains a string, it isn't itself a string. The –ComputerName parameter expects a string, not an object with a property. Therefore, that command doesn't work either.

The following command achieves the goal of passing the computer name as a string to the -ComputerName parameter:

``` pwsh
Get-Process –ComputerName (Get-ADComputer –Filter * | Select-Object –ExpandProperty Name)
```

The –ExpandProperty parameter accepts one, and only one, property name. When you use that parameter, only the contents of the specified property are produced by Select-Object. Some people refer to this feature as extracting the property contents. The official description of the feature is expanding the property contents.

In the preceding command, the result of the parenthetical command is a collection of strings that are passed as individual strings, not an array, and that is what the –ComputerName parameter expects. The command will work correctly; however, it might produce an error if one or more of the computers can't be reached on the network.

Expanding property values also works when piping output. Consider the following example:

``` pwsh
Get-ADUser Ty -Properties MemberOf | Get-ADGroup
```

This command returns an error because Windows PowerShell can't match the MemberOf property to any property of Get-ADGroup.

However, if you expand the value of the MemberOf property, as in the following example, Windows PowerShell can match the resulting output to a value that Get-ADGroup understands as valid input:

``` pwsh
Get-ADUser Ty -Properties MemberOf | Select-Object -ExpandProperty MemberOf | Get-ADGroup
```