Modules are groups of related PowerShell capabilities that are bundled together into a single unit. For the purposes of this class, you can think of them as containers hosting multiple cmdlets. Modules help with organizing cmdlets into distributable units. Microsoft and other software companies provide modules as part of the management tools for their applications and services.

To use a module's cmdlets, the module must be loaded into the current PowerShell session. This typically takes place automatically but, depending on your configuration, might require that you load modules explicitly by running the Import-Module cmdlet.

## Autoloading

In Windows PowerShell version 3.0 and newer, modules load automatically if you run a cmdlet that is part of that module. This works if the module that contains the cmdlet is in a folder under the module load paths. By default, these folders include %systemdir%\WindowsPowerShell\v1.0\Modules and %userprofiles%\Documents\WindowsPowerShell\Modules. The list of folders is stored in the $env:PSModulePath environment variable. When you explicitly import a module by name, PowerShell checks the locations referenced by that environment variable.

For PowerShell 7, the PSModulePath includes the following locations:

C:\Users\<user>\Documents\PowerShell\Modules C:\Program Files\PowerShell\Modules C:\Program Files\PowerShell\7\Modules C:\Program Files\WindowsPowerShell\Modules C:\WINDOWS\System32\WindowsPowerShell\v1.0\Modules

When using Windows PowerShell, the path %systemdir%\WindowsPowerShell\v1.0\Modules is commonly referred to by using the combination of the $PSHome environment variable (which points to %systemdir%\WindowsPowerShell\v1.0) and the Modules path (that is, by using the $PSHome\Modules notation). For PowerShell 7.0, the $PSHome environment variable refers to C:\Program Files\PowerShell\7

## Using Modules To Discover Cmdlets

When you use the Get-Module command, it displays a partial list of cmdlets that the module you reference contains. However, you can use the module in another way to find its cmdlets.

For example, if you've discovered the module NetAdapter, you would expect that it should contain cmdlets you can use to manage network adapters. You can find all applicable commands in that module by running the Get-Command –Module NetAdapter command. The –Module parameter restricts the results to just those commands in the designated module.

## PowerShell Gallery

The [PowerShell Gallery](https://www.powershellgallery.com/) is a central repository for Windows PowerShell–related content, including scripts and modules. The PowerShell Gallery uses the Windows PowerShell module, PowerShellGet. This module is part of Windows PowerShell 5.0 and newer.

PowerShellGet contains cmdlets for finding and installing modules, scripts, and commands from the online gallery. For example, the Find-Command cmdlet searches for commands, functions, and aliases. It works similar to the Get-Command cmdlet, including support for wildcards.

You can pass the results of the Find-Command cmdlet to the Install-Module cmdlet, which the PowerShellGet module also contains. Install-Module will install the module that contains the cmdlet that you discovered.

The following table lists the two cmdlets used most often to find content in the PowerShell Gallery.

Table 1: Cmdlets used to find content in the PowerShell Gallery

|Cmdlet|	Description|
| --- | --- |
|Find-Module|	Use this cmdlet to search for Windows PowerShell modules in the PowerShell Gallery. The simplest usage conducts searches based on the module name, but you can also search based on the command name, version, DscResource, and RoleCapability.|
|Find-Script|	Use this cmdlet to search for Windows PowerShell scripts in the PowerShell Gallery. The simplest usage conducts searches based on the script name, but you can also search based on the version.|

## Create Modules

You can create modules to store functions and share those functions among scripts. After you put your functions into modules, they're discoverable just as cmdlets are. Also, like the modules included with Windows, the modules you create load automatically when a function is required.

As a best practice, you should name your functions in modules with a naming structure similar to the cmdlet naming convention. For example, you would use the verb-noun format.
Functions in modules can include comment-based help that's discoverable by using Get-Help. To support this, you need to include the help information in each function.

In many cases, you already have your functions in a Windows PowerShell script file. To convert a script file containing only functions to a module, rename it with the .psm1 file extension. No structural changes in the file are required.

Windows PowerShell uses the $PSModulePath environmental variable to define the paths from which modules are loaded. In Windows PowerShell 5.0, the following paths are listed:

+ C:\Users\UserID\Documents\WindowsPowerShell\Modules
+ C:\Program Files\WindowsPowerShell\Modules
+ C:\Windows\System32\WindowsPowerShell\1.0\Modules

Windows PowerShell 7 includes the following other paths:

+ C:\Program Files\PowerShell\Modules
+ C:\Program Files\PowerShell\7\Modules

If you store modules in C:\Users\UserID\Document\WindowsPowerShell\Modules, they're only available to a single user.

Modules aren't placed directly in the Modules directory. Instead, you must create a subfolder with the same name as the file and place the file in that folder. For example, if you have a module named AdatumFunctions.psm1, you'd place it in C:\Program Files\WindowsPowerShell\Modules\AdatumFunctions.

## Dot Sourcing

Dot sourcing is a method for importing another script into the current scope. If you have a script file that contains functions, you can use dot sourcing to load the functions into memory at a Windows PowerShell prompt. Normally, when you run the script file with functions, the functions are removed from memory when the script completes. When you use dot sourcing, the functions remain in memory, and you can use them at the Windows PowerShell prompt. You can also use dot sourcing within a script to import content from another script.

Dot sourcing can load from a local file or over the network by using a Universal Naming Convention (UNC) path. The syntax for using dot sourcing is:


``` pwsh
. C:\scripts\functions.ps1
```

At one time, dot sourcing was the only method available to maintain a centralized repository of functions that could be reused across multiple scripts. However, modules are a more standardized and preferred method for maintaining functions used across scripts.