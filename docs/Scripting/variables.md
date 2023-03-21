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

2. **Automatic variables**: Automatic variables store the state of PowerShell. These variables are created by PowerShell, and PowerShell changes their values as required to maintain their accuracy. Users can't change the value of these variables. For example, the $PSHOME variable stores the path to the PowerShell installation directory.

For more information, a list, and a description of the automatic variables, see [about_Automatic_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_automatic_variables?view=powershell-7.3).

3. **Preference variables**: Preference variables store user preferences for PowerShell. These variables are created by PowerShell and are populated with default values. Users can change the values of these variables. For example, the $MaximumHistoryCount variable determines the maximum number of entries in the session history.

For more information, a list, and a description of the preference variables, see [about_Preference_Variables](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7.3).

