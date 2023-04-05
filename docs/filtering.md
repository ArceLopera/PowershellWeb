

Command output can be filtered in several ways:

+ Using wildcards in the parameters that support it. For example:
    ``` pwsh
    get-service -name s*
    ```
    ...displays a list of services whose name starts with S.

+ If the parameter does not support wildcards, the Where-Object (abbreviated Where) cmdlet can be used in the pipeline. For example:

    ``` pwsh
    Get-Service | where -filter { $_.Status -like "Run*" }
    ```
    ...displays a list of services whose status starts with "Run"

Some of the comparison operators that can be used are:

|Operator| Meaning|
| --- | --- |
|-eq| Equal|
|-ne |not equal|
|-gt| Greater than|
|-ge| Greater than or equal|
|-lt| Less Than|
|-le| Less than or equal|
|-like| Matches the expression with wildcards|
|-notlike| Does not match the expression with wildcards|

For example:
``` pwsh
Get-Service | where -filter { $_.Status -eq "Running" }
```

String comparisons are normally not case sensitive. If it is required to do so, a c is placed before the operators (-ceq, -cne, -cgt, -cge...).

You can also use the connectives -and and -or.

PowerShell also contains the -like operator and its case-sensitive companion, -clike. The -like operator resembles -eq but supports the use of the question mark (?) and asterisk (*) wildcard characters in string comparisons.

Other, more advanced operators exist that are beyond the scope of this course. These operators include:

The -in and -contains operators, which test whether an object exists in a collection.
The -as operator, which tests whether an object is of a specified type.
The -match and -cmatch operators, which compare a string to a regular expression.
PowerShell also contains many operators that reverse the logic of the comparison, such as -notlike and -notin.

For more information, see the help topic about_comparison_operators.

## Advanced Filtering

The advanced syntax of Where-Object uses a filter script. A filter script is a script block that contains the comparison and that you pass by using the -FilterScript parameter. Within that script block, you can use the built-in $PSItem variable (or $_, which is also valid in versions of Windows PowerShell older than 3.0) to reference whatever object was piped into the command. Your filter script runs one time for each object that's piped into the command. When the filter script returns True, that object is passed down the pipeline as output. When the filter script returns False, that object is removed from the pipeline.

The following two commands are functionally identical. The first uses the basic syntax, and the second uses the advanced syntax to do the same thing:

``` pwsh
## basic syntax
Get-Service | Where Status –eq Running
## advanced syntax
Get-Service | Where-Object –FilterScript { $PSItem.Status –eq 'Running' }

```

The -FilterScript parameter is positional, and most users omit it. Most users also use the Where alias or the ? alias, which is even shorter. Experienced Windows PowerShell users also use the $_ variable instead of $PSItem, because only $_ is allowed in Windows PowerShell 1.0 and Windows PowerShell 2.0. The following commands perform the same task as the previous two commands:

``` pwsh
Get-Service | Where {$PSItem.Status –eq 'Running'}

Get-Service | ? {$_.Status –eq 'Running'}
```

### Combining multiple criteria
The advanced syntax allows you to combine multiple criteria by using the -and and -or Boolean, or logical, operators. Here's an example:

``` pwsh
Get-EventLog –LogName Security –Newest 100 |
Where { $PSItem.EventID –eq 4672 –and $PSItem.EntryType –eq 'SuccessAudit' }
```

### Accessing properties without limitations
Although the basic filtering syntax can access only the direct properties of the object being evaluated, the advanced syntax doesn't have that limitation. For example, to display a list of all the services that have names longer than eight characters, use this command:

``` pwsh
Get-Service | Where {$PSItem.Name.Length –gt 8}
```

### Example
It is required to draw a list that includes the 10 processes that are consuming the most virtual memory, not including Powershell. At the end, the total virtual memory that these 10 processes are consuming should be presented. The listing should only include the Name and VM columns.

+ The first step (filtering Powershell processes) can be done like this:
``` pwsh
Get Process | where -filter { $_.Name -notlike "Powershell*" }
```

+ Then it is organized by the VM column, in descending order, and the columns that you want to show are specified:
``` pwsh
Get Process | where -filter { $_.Name -notlike "Powershell*" } | sort VM -desc | select name,vm
```

+ Finally, the Measure-Object cmdlet is used to find the total virtual memory usage:
``` pwsh
Get Process | where -filter { $_.Name -notlike "Powershell*" } | sort VM | select name,vm -first 10 | Measure-Object -Property vm -sum
```

### Other Example 

``` pwsh
Get-Process |
Select-Object Name,
              ID,
              @{n='VirtualMemory(MB)';e={'{0:N2}' –f ($PSItem.VM / 1MB) -as [Double] }},
              @{n='PagedMemory(MB)';e={'{0:N2}' –f ($PSItem.PM / 1MB) -as [Double] }}
```

This example uses the Windows PowerShell -f formatting operator. When used with a string, the -f formatting operator instructs Windows PowerShell to replace one or more placeholders in the string with the specified values that follow the operator.

In this example, the string that precedes the -f operator instructs Windows PowerShell what data to display. The string '{0:N2}' signifies displaying the first data item as a number with two decimal places. The original mathematical expression comes after the operator. It's in parentheses to make sure that it runs as a single unit.

Best Practice is to always *filter left*, any filtering should occur as far to the left, or as close to the beginning of the command line, as possible. This will have a significant effect on performance.