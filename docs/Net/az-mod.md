Azure PowerShell is a module that you add to Windows PowerShell or PowerShell Core to let you connect to your Azure subscription and manage resources. It provides a set of cmdlets that you can use to manage and administer Azure resources directly from the PowerShell command line. Azure PowerShell makes it easier to interact with Azure, but also provides powerful features for automation.

PowerShell provides powerful features for automation that you can use to manage your Azure resources; for example, in the context of a continuous integration and continuous delivery (CI/CD) pipeline.

The Az PowerShell module is the replacement for AzureRM and is the recommended version to use for interacting with Azure. To keep up with the latest Azure features in PowerShell, you should migrate to the Az PowerShell module.

## Benefits of the Az PowerShell module
The Az PowerShell module features the following benefits:

**Security and stability:**

+ Token cache encryption
+ Prevention of man-in-the-middle attack type
+ Support for authentication with Active Directory Federation Services (AD FS) in Windows Server 2019
+ Username and password authentication in PowerShell 7
+ Support for features such as continuous access evaluation

**Support for all Azure services:**

+ All generally available Azure services have a corresponding supported PowerShell module
+ Multiple bug fixes and API version upgrades since AzureRM

**New capabilities:**

+ Support in Cloud Shell and cross-platform
+ Ability to get and use access tokens to access Azure resources
+ Cmdlets for advanced Representational State Transfer (REST) operations with Azure resources


The Az PowerShell module is based on the .NET Standard library and works with PowerShell 7 and newer on all platforms including Windows, macOS, and Linux. It's also compatible with Windows PowerShell 5.1.

## Installation

The Azure Az PowerShell module is a rollup module. Installing it downloads the available Az PowerShell modules and makes their cmdlets available for use. The Azure Az PowerShell module works with PowerShell 7.x and newer versions on all platforms. Azure PowerShell has no additional requirements when you run it on PowerShell 7.x and newer versions.

To check your PowerShell version, run the following command from within a PowerShell session:

``` pwsh
$PSVersionTable.PSVersion
```

Before installing the Azure Az PowerShell module, you should set your PowerShell script execution policy to RemoteSigned. You can do this by running the following command:

``` pwsh
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

You can install the Azure Az PowerShell module by using one of the following methods:

1. The Install-Module cmdlet
2. Azure PowerShell MSI
3. Az PowerShell Docker container

The Azure Az PowerShell module is preinstalled in Azure Cloud Shell. You can use it directly from the browser, without installing anything locally on your machine.

### The Install-Module cmdlet
Using the Install-Module cmdlet is the preferred installation method for the Azure Az PowerShell module. You should install this module for the current user only. This is the recommended installation scope. This method works the same on Windows, macOS, and Linux platforms. To install the Az module, run the following command from a local PowerShell session:

``` pwsh
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
```

Although PowerShell 7.x is the recommended version of PowerShell, and Install-Module is the recommended installation option, you can also install the Az module within PowerShell 5.1 environment on Windows. If you're on Windows 10 version 1607 or higher, you already have PowerShell 5.1 installed. You should also make sure that you have .NET Framework 4.7.2 or newer installed and the latest version of PowerShellGet. To install the latest version of the PowerShellGet module within PowerShell 5.1, run the following command:

``` pwsh
Install-Module -Name PowerShellGet -Force
```

You can then install the Az module by using the same command you use in PowerShell 7.1.

### Azure PowerShell MSI
In some environments, it isn't possible to connect to the PowerShell Gallery. In such situations, you can install the Az PowerShell module offline, by downloading the Azure PowerShell MSI package. Keep in mind that the MSI installer only works for PowerShell 5.1 on Windows.

To update any PowerShell module, you should use the same method used to install the module. For example, if you originally used Install-Module, then you should use Update-Module to get the latest version. If you originally used the MSI package, then you should download and install the new MSI package.

### Az PowerShell Docker container
It's also possible to run Azure PowerShell inside a Docker image. Microsoft provides Docker images with Azure PowerShell preinstalled. The released images require Docker 17.05 or newer. The latest container image contains the latest version of PowerShell and the latest Azure PowerShell modules supported with the Az module.

To download the image and start an interactive PowerShell session, you should run the following commands:

``` pwsh
docker pull mcr.microsoft.com/azure-powershell
docker run -it mcr.microsoft.com/azure-powershell pwsh
```

## Start

To start working in the Azure PowerShell environment, you should first sign in with your Azure credentials. This step is different from working in pure PowerShell. Your Azure credentials are the same credentials you use to sign in to the Azure portal or other Azure-based resources.

To sign in to Azure from Azure PowerShell, run the following command:

``` pwsh
Connect-AzAccount
```

After running this command, you'll be prompted to sign in with your Azure credentials. After you successfully authenticate to Azure, you can start using commands from the Az module to manage your Azure resources.

## Azure AD

The Azure Active Directory Module for Windows PowerShell provides cmdlets that you can use for Azure Active Directory (Azure AD) administrative tasks. These tasks include user management, domain management, and configuring single sign-on. This topic includes information about how to install these cmdlets for use with your directory.

You mostly need the Azure Active Directory Module for Windows PowerShell when you manage users, groups, and services such as Microsoft 365. However, Microsoft is replacing the Azure Active Directory Module for Windows PowerShell with Azure Active Directory PowerShell for Graph. The Azure Active Directory Module for Windows PowerShell cmdlets include Msol in their names, while the Azure Active Directory PowerShell for Graph cmdlets use AzureAD in their names.

### Azure Active Directory Module for Windows PowerShell
The following Windows operating systems support the Azure Active Directory Module for Windows PowerShell, with the default version of Microsoft .NET Framework and Windows PowerShell:

- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

The easiest way to install the module is from the PowerShell Gallery. You can install the module with the Install-Module cmdlet by running the following command:

``` pwsh
Install-Module MSOnline
```

### Azure Active Directory PowerShell for Graph
Currently, the Azure Active Directory PowerShell for Graph module doesn't completely replace the functionality of the Azure Active Directory Module for Windows PowerShell module for user, group, and license administration. In some cases, you need to use both versions. You can safely install both versions on the same computer.

The Azure AD PowerShell for Graph module has two versions: a Public Preview version and a General Availability version. It isn't recommended to use the Public Preview version for production scenarios.

### Connecting to Azure AD

If you want to connect to the Azure AD service with the Azure Active Directory Module for Windows PowerShell, run the following command:

``` pwsh
Connect-MsolService
```

If you use the Azure AD PowerShell for Graph module, and want to connect to Azure AD, run the following command:

``` pwsh
Connect-AzureAD
```

After running either of the previous commands, you'll be prompted for your Azure AD credentials. You should use the credentials that you use to sign in to Microsoft 365 or your Azure services. After you authenticate, you'll be able to use the cmdlets available for Azure AD management.

Additional reading: For more information about the Azure Active Directory PowerShell for Graph cmdlets, refer to [AzureAD](https://learn.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true).

Additional reading: For more information about the Azure Active Directory Module for Windows PowerShell cmdlets, refer to [MSOnline](https://learn.microsoft.com/en-us/powershell/module/msonline/?view=azureadps-1.0&preserve-view=true).