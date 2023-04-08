## WMI and CMI basics

A Windows computer contains thousands of items of management information. The WMI (Windows Management Instrumentation) and CIM (Common Information Model) frameworks seek to facilitate and organize these elements. 

WMI, which is the Microsoft implementation of the Web-Based Enterprise Management (WBEM) standard, is an older technology that's based on preliminary standards and proprietary technology. CIM is a newer technology that's based on open, cross-platform standards.

Both technologies provide a way to connect to a common information repository (also known as the WMI repository). This repository contains management information that you can query and manipulate. There are two parallel sets of cmdlets that you can use to perform tasks by using WMI or CIM.

## WMI cmdlets
WMI cmdlets use the same repository as CIM cmdlets. The only difference is how the WMI cmdlets connect to a remote computer.

WMI cmdlets don't support session-based connections. These commands support only ad-hoc remote connections over DCOM. Whether used by WMI or CIM commands, DCOM might be difficult to use on some networks. DCOM uses the remote procedure call (RPC) protocol, which connects by using randomly selected port numbers. Special firewall exceptions are required for DCOM to work correctly.

The Windows Management Instrumentation service provides the connectivity for WMI. WMI cmdlets don't require any version of the Windows Management Framework on a remote computer. They also don't require Windows PowerShell remoting to be enabled. If the remote computer has the Windows Firewall feature enabled, you need to ensure that the remote administration exceptions are enabled on the remote computer. If the remote computer has a different local firewall enabled, equivalent exceptions must be created and enabled.

There are different tools designed to explore the different classes of WMI. One of them is [WMI Explorer](https://github.com/vinaypamnani/wmie2/releases)

### Get-WmiObject

You can use the ``Get-WmiObject`` cmdlet to explore WMI namespaces. For example, if you wanted to query the classes related to disks, you could use the following command:
``` pwsh
Get-WmiObject -Namespace root\CIMv2 -list | where name -like '*disk*'
```

The following command displays a complete list of classes within the ``root\CIMv2`` namespace:
``` pwsh
Get-WmiObject -Namespace root\CIMv2 -list
```

To interrogate a specific class, use the ``-class`` parameter:
``` pwsh
Get-WmiObject -Namespace root\CIMv2 -class win32_desktop
```

The above command can be abbreviated, since the default namespace is ``root\CIMv2``, and the ``-class`` parameter are both positional:
``` pwsh
Get-WmiObject win32_desktop
```
## CIM Cmdlets

You can use CIM cmdlets to access the repository on local or remote computers. Multiple protocols for connectivity are supported and the protocol varies, depending on how the connection is created. Component Object Model (COM) is a protocol for local communication between software components. Distributed COM (DCOM) is an older proprietary Microsoft protocol for connectivity which uses dynamic ports. Web Services for Management (WS-MAN) is a web-based protocol defined by the DMTF for connectivity. A WS-MAN listener is configured automatically on port 5985. Also, a secure listener is created on port 5986 if a certificate is available.

CIM cmdlets can create connections in three ways:

+ Local connections. When you don't specify a remote computer, the CIM cmdlets use a local COM session to access and communicate with the repository.
+ Ad-hoc connections. When you specify a remote computer by using the -ComputerName parameter with a CIM cmdlet, the remote connection uses WS-MAN.
+ CIM sessions. You can precreate a CIM session with specific options for connectivity to remote computers. When you create a CIM session, you can specify whether to use WS-MAN or DCOM.

To use the CIM cmdlets on a remote computer, the remote computer doesn't need to have PowerShell installed. The remote computer needs to support WS-MAN to connect to it with CIM cmdlets. You can use CIM cmdlets with Linux computers.

In Windows, WS-MAN connectivity is provided by the Windows Remote Management (WinRM) service. This service also provides support for Windows PowerShell remoting, but PowerShell remoting is not required for the CIM cmdlets.

### Get-CimInstance

The ``Get-CimInstance`` command was introduced in Powershell version 3, and works much like ``Get-WmiObject``, except for the following:

- The ``-ClassName`` parameter is used instead of ``-Class``.
- There is no ``-list`` parameter to list all classes in a namespace.

The ``Get-CimClass`` cmdlet must be used with the ``-Namespace`` parameter.
Examples:
List the instances of the ``Win32_LogicalDisk`` class:

``` pwsh
Get-CimInstance -ClassName win32_logicaldisk
```

Get the list of classes from ``root\CIMv2``, filtering out those that have to do with disks:

``` pwsh
Get-CimClass -Namespace root\CIMv2 | where cimclassname -Like '*disk*'
```

## CIM or WMI?
In general, you should use CIM cmdlets instead of the older WMI cmdlets. Microsoft considers the WMI commands within Windows PowerShell to be deprecated, although the underlying WMI repository is still a current technology. You should rely primarily on CIM commands and use WMI commands only when CIM commands aren't practical.

CIM cmdlets also provide easier network connectivity. Because CIM cmdlets use WS-MAN for remote connectivity, the firewall rules for known ports are easier to create than the randomly generated ports used by DCOM. If remote computers don't support using WS-MAN, you can specify the use of DCOM for connectivity by using a CIM session.

### Using cmdlets instead of classes
The repository is not well-documented, so discovering the classes that you need to perform a specific task might be difficult and impractical. If there is a PowerShell cmdlet for managing a specific item such as networking, then it's typically easier to use that cmdlet instead of the equivalent class methods exposed through CIM cmdlets. These purpose-built cmdlets often use CIM or WMI but hide their complexity from you. This approach gives you the advantages of Windows PowerShell cmdlets, such as discoverability and built-in documentation, while also giving you the existing functionality of the repository.

## Repositories WMI and CIM

The repository that Common Information Model (CIM) and Windows Management Instrumentation (WMI) use is organized into namespaces. A namespace is a folder that groups related items for organizational purposes.

Namespaces contain classes. A class represents a manageable software or hardware component. For example, the Windows operating system provides classes for processors, disk drives, services, user accounts, and so on. Each computer on the network might have slightly different namespaces and classes. For example, a domain controller might have a class named ActiveDirectory that doesn't exist on other computers.

To find the top-level namespaces, run the following command:

``` pwsh
Get-CimInstance -Namespace root -ClassName __Namespace
```

In the previous command, there are two underscores (_) in the class name of __Namespace.

Some of the namespaces returned might include:

+ subscription
+ DEFAULT
+ CIMV2
+ msdtc
+ Cli
+ Intel_ME
+ SECURITY
+ HyperVCluster
+ SecurityCenter2
+ RSOP
+ Intel
+ PEH
+ StandardCimv2
+ WMI
+ directory
+ Policy
+ virtualization
+ Interop
+ Hardware
+ ServiceModel
+ SecurityCenter
+ Microsoft
+ Appv
+ dcim

When you work with the repository, you typically work with instances. An instance is an actual occurrence of a class. For example, if your computer has two processor sockets, you'll have two instances of the class that represents processors. If your computer doesn't have an attached tape drive, you'll have zero instances of the tape drive class.

Instances are objects, similar to the objects that you've already used in Windows PowerShell. Instances have properties, and some instances have methods. Properties describe the attributes of an instance. For example, a network adapter instance might have properties that describe its speed, power state, and so on. Methods tell an instance to do something. For example, the instance that represents the operating system might have a method to restart the operating system.


- The ``root\CIMv2`` namespace contains all information about the Windows operating system and the underlying hardware.
- On client computers, the namespace ``root\SecurityCenter2`` (``root\SecurityCenter`` in earlier versions of the operating system) contains information about firewall, antivirus, and antispyware software installed on the computer.

Within each namespace, WMI includes a series of classes. Each class represents a type of management component that WMI knows how to query. For example, the ``Win32_LogicalDisk`` class in the ``root\CIMv2`` namespace contains information about logical disks, while the ``AntiSpywareProduct`` class in the ``root\SecurityCenterv2`` namespace contains information about antispyware products. .

If manageable components exist for a certain class, they will appear as instances of that class. Class names in the ``root\CIMv2`` namespace usually start with ``Win32_`` (even on 64-bit machines) or ``CIM_`` . Typically, the properties of such instances are read-only (that is, you cannot make changes to the parameter values).

## Listing classes
Most of the time, when you use Common Information Model (CIM) and Windows Management Instrumentation (WMI) you're trying to accomplish a specific task. To identify how to accomplish that task, you typically do an internet search to identify if anyone has provided sample code that accomplishes a similar task. Then you can modify that code for your purposes and identify the CIM or WMI classes that they're using. When you don't find useful sample code, you might want to browse the available classes to check if anything is suitable.

To explore the classes available to you by using CIM and WMI, you can list the classes available in a namespace. For example, to list all the classes in the root\CIMv2 namespace, run either of these commands:

``` pwsh
Get-WmiObject -Namespace root\CIMv2 -List
#or
Get-CimClass -Namespace root\CIMv2
```

In the root\CIMv2 namespace, you'll notice some class names that start with Win32_ and others that start with CIM_. This namespace is the only one that uses these prefixes. Classes that start with CIM_ are typically abstract classes. Classes that start with Win32_ are typically more specific versions of the abstract classes, and they contain information that's specific to the Windows operating system.

Many administrators feel that the repository is difficult to work with. Finding the class that you need to perform a particular task is a guessing game. You have to guess what the class might be named and then review the class list to figure out whether you're correct. Then you must query the class to determine whether it contains the information that you need. Because many classes outside the root\CIMv2 namespace are not well-documented, this is your best approach.

No central directory of repository classes exists. The repository doesn't include a search system. You can use Windows PowerShell to perform a basic keyword search of repository class names. For example, to find all the classes in the root\CIMv2 namespace having network in the class name, use the following command:

``` pwsh
Get-CimClass *network* | Sort CimClassName
```

However, this technique does not provide the ability to search class descriptions because that information isn't stored in the repository. An internet search engine provides more viable alternative to search for possible class names.

There's one specific WMI class object that can cause problems for system administrators. This is the Win32_Product class. You can use this class to query installed software, but be aware that returning the results takes a long time and has negative performance implications. When you query this class, the provider performs a Windows Installer (MSI) reconfiguration on every MSI package on the system as the query is being performed. Microsoft recommends using the Win32reg_AddRemovePrograms class as an alternative, but this class is only available on systems with the Microsoft Endpoint Configuration Manager client installed.

## Query instances

Once you've identified the class you want to query, you can use Windows PowerShell to retrieve the specific instances of that class. For example, if you want to retrieve all instances of the Win32_LogicalDisk class from the root\CIMv2 namespace, run either of the following commands:

```pwsh
Get-WmiObject -Class Win32_LogicalDisk
# or
Get-CimInstance -ClassName Win32_LogicalDisk
```

The output from these commands is formatted differently, but they contain the same information.

When using Get-CimInstance, you can use tab completion for the class name. This isn't possible with Get-WmiObject.

Both the -Class parameter of Get-WmiObject and the -ClassName parameter of Get-CimInstance are positional. The names of positional parameters don't need to be specified.

### Filtering instances
By default, both commands retrieve all available instances of the specified class. You can specify filter criteria to retrieve a smaller set of instances. The filter languages used by these commands don't use Windows PowerShell comparison operators. Instead, they use traditional programming operators, as listed in the following table.

|Comparison|	WMI and CIM operator|	Windows PowerShell operator|
| --- | --- | --- |
|Equal to|	=|	-eq|
|Not equal to|	<>|	-ne|
|Greater than|	>|	-gt|
|Less than|	<|	-lt|
|Less than or equal to|	<=|	-le|
|Greater than or equal to|	>=|	-ge|
|Wildcard string match|	LIKE (with % as the wildcard)|	-like (with ***** as the wildcard)|
|Require two or more conditions to be true|	AND|	-and|
|Require one of two or more conditions to be true|	OR|	-or|

For example, to retrieve only the instances of Win32_LogicalDisk for which the DriveType property is 3, run either of the following commands:

``` pwsh
Get-WmiObject -Class Win32_LogicalDisk -Filter "DriveType=3"
#or
Get-CimInstance -ClassName Win32_LogicalDisk -Filter "DriveType=3"
```
Many class properties use integers to represent different kinds of things. For example, in the Win32_LogicalDisk class, a DriveType property of 3 represents a local fixed disk. A value of 5 represents an optical disk, such as a DVD drive. You have to examine the class documentation to learn what each value represents.

### Querying by using WQL

Both WMI and CIM accept query statements written in [WMI Query Language (WQL)](https://learn.microsoft.com/en-us/windows/win32/wmisdk/wql-sql-for-wmi). WQL is a subset of Structured Query Language (SQL) that is specific to querying WMI. Their format is fairly intuitive so it's relatively straightforward to author them. For example, the following queries are equivalent to the previously described commands that retrieve the specific instance of the Win32_LogicalDisk class:

``` pwsh
Get-WmiObject -Query "SELECT * FROM Win32_LogicalDisk WHERE DriveType = 3"
#or
Get-CimInstance -Query "SELECT * FROM Win32_LogicalDisk WHERE DriveType = 3"
```

## Discover Methods

A method is an action that you can perform on an object. Repository objects that you query by using Common Information Model (CIM) or Windows Management Instrumentation (WMI) have methods that reconfigure the manageable components that the objects represent. For example, the Win32_Service class instances represent background services. The class has a Change method that reconfigures many of a service’s settings, including the sign-in password, name, and start mode.

When you query instances of a class, you can use the Get-Member cmdlet to discover the methods available for that type of object. The following examples depict how to use Get-Member to review the properties and methods for instances of Win32_Service:

``` pwsh
Get-WmiObject -Class Win32_Service | Get-Member -MemberType Method
# or
Get-CimInstance -ClassName Win32_Service | Get-Member -MemberType Method
```

You can also use Get-CimClass to review the methods available for a specific class:

``` pwsh
Get-CimClass -Class Win32_Service | Select-Object -ExpandProperty CimClassMethods
```

The methods available to managing object instances vary, depending on the type of object. The output of the commands doesn't explain how to use the methods, so unless you already know how to use them, you will need to find the relevant documentation by relying on other sources of information, such as results of an internet search.

If any method documentation exists, the documentation webpage for the repository class will include it. Remember that repository classes aren't typically well-documented, especially the classes that aren't in the root\CIMv2 namespace.

An internet search engine provides the fastest way to find the documentation for a class. When you enter the class name into a search engine, one of the first few search results is typically the class documentation page. Generally, the documentation page includes a section named Methods that lists the class methods. Select any method name to display the instructions for using that method.

The documentation allows you to determine the arguments that each method requires. For example, the Win32Shutdown method of the Win32_OperatingSystem class accepts a single argument. This argument is an integer, and it tells the method to either shut down, restart, or sign out.

To use a method on an object, you invoke it. You can accomplish this by using direct invocation, which includes a reference to the object that supports the method, followed by the method name. Alternatively, you can use the Invoke-WmiMethod or Invoke-CimMethod cmdlets. If you use the cmdlets, the cmdlet you use needs to match the type of object you're working with.

### Direct invocation
Direct invocation is typically used when you've loaded Windows Management Instrumentation (WMI) objects into a variable. Then, you can invoke methods available for that object type by specifying the variable name, a dot (.), and then the method. This is similar to how you display property values for an object contained in a variable. The following example queries the spooler service as a WMI object and then invokes the StopService method.

``` pwsh
$WmiSpoolerService = Get-WmiObject -Class Win32_Service -Filter "Name='Spooler'"
$WmiSpoolerService.StopService()
```

The StopService method in the previous example doesn't require any parameters to be passed to it, so there's no value within the parentheses. If you invoke a method that requires parameters, their values are placed within the parentheses. The following example sets the value of the start mode parameter to Manual:

``` pwsh
$WmiSpoolerService.ChangeStartMode("Manual")
```

To identify the parameters required for a method, you should review the documentation for its class. Be aware that parameters for WMI method parameters need to be passed in a specific order. To identify the order, you can use the GetMethodParameters() method. The following example queries the parameters for the Change method:

``` pwsh
$WmiSpoolerService.GetMethodParameters("Change")
```

If the method requires multiple parameters and you don't want to change some of them, you can pass a $null value for the parameters you don't want to change. Additionally, you don't need to specify parameters that are positioned after the one you want to change. In the following example, the Change method has 11 parameters, but only the second parameter (display name) is being configured.

``` pwsh
$WimSpoolerService.Change($null,"Printer Service")
```

Because $null is treated as a parameter value to skip when calling a method, you can't set a value to $null by invoking a method on a WMI object.

Objects retrieved by Get-CimInstance are static and don't have methods. Therefore, you can't use them by relying on direct invocation.

### Using the Invoke-WmiMethod Cmdlet
The Invoke-WmiMethod cmdlet is another way to invoke a method for a WMI object with different syntax. The -Name parameter is used to specify the method to invoke and the -Argument parameter is used to specify the parameter values that are passed to the method. If required, multiple parameters are passed as a comma-separated list or array. The parameter values need to be in a specific order just as they were for direct invocation.

You can use the Invoke-WmiMethod cmdlet by itself, or you can use the pipeline to send it a WMI object. Here are two examples that work the same way:

``` pwsh
Get-WmiObject -Class Win32_OperatingSystem | Invoke-WmiMethod -Name Win32Shutdown -Argument 0
# or
Invoke-WmiMethod -Class Win32_OperatingSystem -Name Win32Shutdown -Argument 0
```

The Get-WmiObject and Invoke-Method cmdlets both have the -ComputerName parameter that lets you run the cmdlet on a remote computer.

### Using the Invoke-CimMethod cmdlet
The Invoke-CimMethod cmdlets provide similar functionality to the Invoke-WmiMethod cmdlet, but argument list for Invoke-CimMethod is a dictionary object. Such an object consists of one or more key-value pairs. The key for each pair is the parameter name, and the value for each pair is the corresponding parameter value. In the following example, Path is the name of the parameter and Notepad.exe is the value:

``` pwsh
Invoke-CimMethod -ComputerName LON-DC1 -ClassName Win32_Process -MethodName Create -Arguments @{CommandLine='Notepad.exe'}
```
To include multiple parameters in the dictionary, use a semicolon to separate each key-value pair. Because the parameters are named, the key-value pairs can be in any order.

To invoke a method on a specific instance of an object, you retrieve the object first by using the Get-CimInstance cmdlet. You can pipe the object directly to the Invoke-CimMethod cmdlet or store it in a variable first. If the object provides all of the necessary information, then you don't need to specify any arguments. The following example retrieves any running instances of Notepad.exe and terminates them:

``` pwsh
Get-CimInstance -ClassName Win32_Process -Filter "Name='notepad.exe'" | Invoke-CimMethod -MethodName Terminate
```

If you use the -ComputerName or -CIMSession parameters with the Get-CimInstance cmdlet and pipe the resulting object to the Invoke-CimMethod cmdlet, the method is invoked on whatever computer or session the object came from. For example, to terminate a process on a remote computer, you can run the following command:

``` pwsh
Get-CimInstance -ClassName Win32_Process -Filter "Name='notepad.exe'" -Computername LON-DC1 | Invoke-CimMethod -MethodName Terminate
```