Powershell commands present their output in tables or lists, depending on the command being used and the attributes it is asked to display.
The Select-Object command (abbreviated as Select) can be used to determine the attributes that are displayed. However, the display is limited to the attributes related to the invoked command. For example:

``` pwsh
Get Process | select name,vm
```

...displays a list of processes, made up only of the name of each process and the amount of virtual memory it uses.

The formatting commands allow, in addition to printing the standard attributes, to display attributes calculated from the basic attributes. The display can be done in table, list or wide format mode.

## Table Format

The Format-Table cmdlet (abbreviated as ft) allows you to display the output of a command in table format. The parameters received by this command are the following:

|Parameters| Meaning|
| --- | --- |
|-Property| Allows you to specify the attributes (native or calculated) that you want to display. The wildcard * can be used to specify all of them.|
|-Autosize| Allows the command to accommodate the width of the columns in the best possible way. Normally this width is fixed, but this parameter adjusts the width of the columns to the longest value of each attribute.|
|-Groupby| This parameter specifies one of the fields. Each time there is a change in the value of this field, a new set of headers is printed. It is recommended to use Sort-Object before doing a Format-Table with Groupby, to avoid unnecessary repetition of headers.|
|-Wrap| When a field's value is excessively long, Powershell wraps it, indicating this with an ellipsis (...). The Wrap parameter causes long values to span one or more additional lines, depending on the length of the value.|

**Examples:**
``` pwsh
get-process | ft-Property *
```
... tries to print all the attributes of the processes. Powershell reviews all lines of the output, and does its best to accommodate all possible fields. Due to this hotfix, the command takes a relatively long time to execute.

``` pwsh
get-process | ft -Property ID,Name,Responding -AutoSize
```

...gets a list of processes with ID, Name, and Responding attributes. The width of the columns is optimized.

``` pwsh
get-process | ft -Property * -autosize
```
... tries to print all the attributes of the processes. However, this command runs faster, since the width of the columns is optimized.

``` pwsh
Get-Service | sortStatus | ft Name,Status,DisplayName -groupby Status
```
... prints a list of services divided into two sections: One of stopped services (Stopped) and another of running services (Running).

``` pwsh
Get-Service | ft Name,Status,DisplayName -autosize -wrap
```
...prints a list of services with the attributes Name, Status and DisplayName. The DisplayName attribute (which is the longest) will span multiple lines if necessary.

## List Format

The Format-List cmdlet (abbreviated fl) allows you to display attributes as a series of name-value pairs, for example:

``` pwsh
Get-Service -name bits | fl -Property *

Name                : bits
RequiredServices    : {RpcSs}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Background Intelligent Transfer Service
DependentServices   : {}
MachineName         : .
ServiceName         : bits
ServicesDependedOn  : {RpcSs}
ServiceHandle       :
Status              : Stopped
ServiceType         : Win32ShareProcess
StartType           : Manual
Site                :
Container           :
```

Note that the output of the Get-Service command was filtered here, to get only the information for the BITS service (otherwise, Format-List would have returned a similar set of lines for each service). Here Format-List was asked to display all attributes of the service (the -Property parameter with wildcard).

## Wide Format

The wide format allows you to display two or more columns of a particular object property, much like the Linux ls command. The Format-Wide (abbreviated fw) cmdlet is used for this purpose.

Examples:
``` pwsh
get-process | format-wide
```
... displays two columns of process names (Format-wide defaults to the Name property).

``` pwsh
get-process | format-wide name -col 4
```
...shows 4 columns of process names.

## Change Of Names Of Attributes

An example of renaming an attribute is the following:

``` pwsh
get service | ft @{name='Service';expression={$_.Name}},Status,DisplayName
```

In this case, a new column is defined, using the expression between the @{ } symbols.

+ Name indicates the name that the column will have. It can be abbreviated as n.
+ Expression defines the content of the column. It can be abbreviated as e.
+ $_ is a special variable that holds the object that is currently being processed. For this example, $_.Name means: "Of the current object, take the Name property."

The above command could be abbreviated like this:
``` pwsh
get service | ft @{n='Service';e={$_.Name}},Status,DisplayName
```
The following command:
``` pwsh
get-process | ft Name,@{n='VM (MB)';e={$_.VM / 1MB}}
```
...displays a process table with two columns: Name (native property) and VM (MB), which shows the amount of virtual memory used by the process, in megabytes.

The command:
``` pwsh
get-process | ft Name,@{n='VM (MB)';e={$_.VM / 1MB -as [int]}} -AutoSize
```
...displays a table similar to the one in the previous example, but rounds the virtual memory to an integer value.

## Using The Format Commands
The formatting command to be used (ft, fl or fw) must be the last one in the pipeline before printing to the screen. The output of a format command can only be redirected to a TEXT file. If you try to convert the output to another format (CSV, HTML, XML) the results will be inconsistent.