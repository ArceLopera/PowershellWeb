PowerShell providers are adapters that connect Windows PowerShell to data stores. They offer an easier-to-understand and consistent interface for working with data stores. 

A PowerShell provider, or just provider, is an adapter that makes some data stores resemble hard drives within Windows PowerShell. Because most administrators are already familiar with managing hard drives using command-line commands, PowerShell providers help those administrators manage other forms of data storage using the same familiar commands.

A provider presents data as a hierarchical store. For example, items such as folders can have sub-items that appear as subfolders. Items can also have properties, and providers let you manipulate both items and their properties by using a specific set of commands.

Managing a technology by using a provider is more difficult than managing it by using technology-specific commands. Individual commands perform specific actions, and the command name describes what the command does. For example, in Internet Information Services (IIS), the Get-WebSite command retrieves IIS sites. When you use the IIS provider, you run the Get-ChildItem IIS:\Sites command instead.

The advantage of a PowerShell provider is that it's dynamic, which makes it suitable for technologies that are subject to frequent changes. For example, when managing IIS, its provider can accommodate newly introduced Microsoft and third-party IIS add-ins. Even though using a provider to manage dynamic and extensible technologies tends to be more complex, it offers a more consistent approach due to its extensibility.

Some common providers include:

+ [**Registry.**](st-drives.md#manage-the-registry) Provides access to the registry keys and values.
+ **Alias.** Provides access to aliases for Windows PowerShell cmdlets.
+ **Environment.** Provides access to Windows environment variables and their values.
+ **FileSystem.** Provides access to the files and folders in the file system.
+ **Function.** Provides access to Windows PowerShell functions loaded into memory.
+ **Variable.** Provides access to Windows PowerShell variables and their values loaded into memory.

## Built-in Providers

The generic commands that you use to work with providers offer a superset of every feature that a provider might support. For example, the Get-ChildItem command includes the –UseTransaction parameter. However, the only built-in provider that supports the Transactions capability is the Registry provider. If you try to use the -UseTransaction parameter in any other provider, you'll receive an error message. You'll also receive an error message whenever you use a common parameter that the provider doesn't support.

Running the Get-PSProvider cmdlet lists the capabilities of each provider that loads into Windows PowerShell. The capabilities of each provider will be different because each provider connects to a different underlying technology.

Some important capabilities include:

+ **ShouldProcess** for providers that can support the –WhatIf and –Confirm parameters.
+ **Filter** for providers that support filtering.
+ **Include** for providers that can include items in the data store based on the name. Supports using wildcards.
+ **Exclude** for providers that can exclude items in the data store based on the name. Supports using wildcards.
+ **ExpandWildcards** for providers that support wildcards in their paths.
+ **Credentials** for providers that support alternative credentials.
+ **Transactions** for providers that support transacted operations.

You should always review the capabilities of a provider before you work with it. This helps you avoid unexpected errors when you try to use unsupported capabilities.

## Access Provider Help

ou can display a list of available providers by using the Get-PSProvider cmdlet. Be aware that providers can be added into Windows PowerShell when you load modules, and they don't display until loaded. For example, when you run the Import-Module ActiveDirectory command to load the ActiveDirectory module or use module autoloading when running an Active Directory cmdlet, a PowerShell provider for Active Directory is included.

Some providers include help files that you can review. Help files use the naming format about_ProviderName_Provider. For example, the help file for the FileSystem provider is about_FileSystem_Provider. You can review this help file's contents by running the following command:

``` pwsh
Get-Help about_FileSystem_Provider
```

Cmdlets that work with providers use the nouns Item and ItemProperty. To list cmdlets that work with providers, run the following cmdlets:

``` pwsh
Get-Command *-Item,*-ItemProperty
```

Examples for every scenario might not be in commands’ help, because the commands are designed to work with any provider. The intent of provider help is to supplement command help with more specific descriptions and examples.
