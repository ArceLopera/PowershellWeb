Powershell allows you to store values ​​in variables. All variable names begin with the $ sign, and are made up of letters, numbers, and the underscore (_).
The assignment operator is the equal sign (=). 

Some examples:

|Command| Description|
| --- | --- |
|$server = "localhost"| The variable will contain a text string|
|$number = 5| The variable will contain an integer|
|$services = Get-Service| The variable will contain a list of services|

If you want to query the type of data a variable contains, you can do so with the Get-Member cmdlet. Get-Member also displays the variable's properties and methods. For example, variables of the System.String type have the ToUpper() method, which converts the content of the variable to uppercase.

``` pwsh
$server.ToUpper()
```
```
LOCALHOST
```

Variables can be used to replace any parameter in a cmdlet call. You can store all types of values in PowerShell variables. For example, store the results of commands, and store elements that are used in commands and expressions, such as names, paths, settings, and values.

To get a list of all the variables in your PowerShell session, type Get-Variable. The variable names are displayed without the preceding dollar ($) sign that is used to reference variables.

## Variables in Powershell

There are several different types of variables in PowerShell.

1. **User-created variables**: User-created variables are created and maintained by the user. By default, the variables that you create at the PowerShell command line exist only while the PowerShell window is open. When the PowerShell windows is closed, the variables are deleted. To save a variable, add it to your PowerShell profile. You can also create variables in scripts with global, script, or local scope.

2. **Automatic variables**: Automatic variables store the state of PowerShell. These variables are created by PowerShell, and PowerShell changes their values as required to maintain their accuracy. Users can't change the value of these variables. For example, the $PSHOME variable stores the path to the PowerShell installation directory.For more information, a list, and a description of the automatic variables, see [about_Automatic_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.3).

3. **Preference variables**: Preference variables store user preferences for PowerShell. These variables are created by PowerShell and are populated with default values. Users can change the values of these variables. For example, the $MaximumHistoryCount variable determines the maximum number of entries in the session history. For more information, a list, and a description of the preference variables, see [about_Preference_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7.3).

## Working With Variables

To create a new variable, use an assignment statement to assign a value to the variable. You don't have to declare the variable before using it. The default value of all variables is $null

``` pwsh
$MyVariable = 1, 2, 3

$Path = "C:\Windows\System32"
```
Variables are useful for storing the results of commands.

For example:

``` pwsh
$Processes = Get-Process

$Today = (Get-Date).DateTime
```
To change the value of a variable, assign a new value to the variable.

### The Variable Cmdlets

|Cmdlet Name|	Description|
| --- | --- |
|Clear-Variable|	Deletes the value of a variable.|
|Get-Variable|	Gets the variables in the current console.|
|New-Variable|	Creates a new variable.|
|Remove-Variable|	Deletes a variable and its value.|
|Set-Variable|	Changes the value of a variable.|

### Variable Types

You can store any type of object in a variable, including integers, strings, arrays, and hash tables. And, objects that represent processes, services, event logs, and computers.

PowerShell variables are loosely typed, which means that they aren't limited to a particular type of object. A single variable can even contain a collection, or array, of different types of objects at the same time.

The data type of a variable is determined by the .NET types of the values of the variable. To view a variable's object type, use Get-Member.

``` pwsh
$a = 12                         # System.Int32
$a = "Word"                     # System.String
$a = 12, "Word"                 # array of System.Int32, System.String
$a = Get-ChildItem C:\Windows   # FileInfo and DirectoryInfo types
```

#### Casting

To use cast notation, enter a type name, enclosed in brackets, before the variable name (on the left side of the assignment statement). The following example creates a $number variable that can contain only integers, a $words variable that can contain only strings, and a $dates variable that can contain only DateTime objects.

``` pwsh
[int]$number = 8
$number = "12345"  # The string is converted to an integer.
$number = "Hello"  #Casting Error
```

Casting is useful when asking user input:

``` pwsh
# If no casting the number will be concatenated instead of multiplied
PS> [int]$num = read-host "Enter any number:"
Enter any number: 1024
PS> $num = $num * 10
PS> $num
10240

```

## Using Variables in Commands and Expressions

To use a variable in a command or expression, type the variable name, preceded by the dollar ($) sign.

If the variable name and dollar sign aren't enclosed in quotation marks, or if they're enclosed in double quotation (") marks, the value of the variable is used in the command or expression.

If the variable name and dollar sign are enclosed in single quotation (') marks, the variable name is used in the expression.

For more information about using quotation marks in PowerShell, see [about_Quoting_Rules](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_quoting_rules?view=powershell-7.3).

Single quotes are used to assign to a variable the exact text that is placed between them, for example:
``` pwsh
PS> $var = 'Hello'
PS> $var = 'The content of the variable is $var'
PS> $var
The content of the variable is $var
```
The single quotes prevent the $ sign from being interpreted as the start of a variable name. To get Powershell to interpret the $ sign as the start of a variable name, the assignment is done with double quotes:
``` pwsh
PS> $var = 'Hello'
PS> $var="Variable contains $var"
PS> $var
Variable contains Hello
```

The backtick \` causes Powershell to ignore the meaning of the following special character, for example:
``` pwsh
PS> $var = 'Hello'
PS> $var="Variable `$var contains $var"
PS> $var
The variable $var contains Hello
```
It can also be used to give special meaning to certain characters (equivalent to \ in C and Java, for example \n, \t...). An example of use is the following:
``` pwsh
PS> $computername = 'localhost'
PS> $phrase = "`$computername`ncontains`n$computername"
PS> $phrase
$computername
contains
localhost
```
As you can see, `n stands for the carriage return character.

To create or display a variable name that includes spaces or special characters, enclose the variable name with the curly braces ({}) characters. The curly braces direct PowerShell to interpret the variable name's characters as literals.

``` pwsh
${save-items} = "a", "b", "c"
${save-items}
```

To reference a variable name that includes braces, enclose the variable name in braces, and use the backtick character to escape the braces. For example, to create a variable named this{value}is type:

``` pwsh
${this`{value`}is} = "This variable name uses braces and backticks."
${this`{value`}is}
```

## List Variables
It is possible to create a list type variable, separating the values ​​of the list with commas:
``` pwsh
PS> $computers = 'server', 'localhost', 'server_2'
PS> $computers
server
localhost
server_2
```
List elements can be accessed by index number. Lists are numbered from zero:
``` pwsh
PS> $computers[0]
server
PS> $computers.count
3
```
Starting with Powershell version 3, if you pass a list as a parameter to a cmdlet, Powershell iterates over the items in the list.

It is also possible to iterate over a list using the Foreach-Object cmdlet. These two commands do the same thing:

``` pwsh
$computers = $computers.tolower()
$computers = $computers | ForEach-Object {$_.tolower()}
```