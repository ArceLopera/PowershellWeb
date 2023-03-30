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