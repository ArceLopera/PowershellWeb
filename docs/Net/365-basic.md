The Microsoft 365 admin center is a web-based console that you can use to manage users, groups, licensing, and other tenant-level configuration settings. When you manage users and groups from here, the properties you modify might be part of Azure AD, Exchange Online, or another service. This console provides a unified interface for some common management tasks.

Links to service-specific, web-based consoles enable you to perform more detailed configuration of Microsoft 365 services. You can use these links to manage services such as Azure AD, Exchange Online, Microsoft Teams, and SharePoint Online. With the service-specific consoles, you can perform tasks such as:

- Creating users and group.
- Modifying email addresses.
- Setting organizational defaults for Teams.
- Configuring external sharing settings for SharePoint Online.

You can access the Microsoft 365 admin console at [https://admin.microsoft.com](https://admin.microsoft.com).

The service-specific, web-based consoles are intuitive and easy to use, but they don't provide access to all possible configuration options. There are many useful configuration options that you can review and configure only by using PowerShell cmdlets. For example, in Exchange Online you can use PowerShell to review the permissions assigned to and configured on a calendar in a mailbox. In the web-based console, you can only review mailbox-level permissions.

Using PowerShell to manage Microsoft 365 services also provides many of the same benefits as using PowerShell to manage local resources. You can:

- Use a query for objects matching certain criteria, and then generate reports.
- Use the pipeline to perform complex operations.
- Automate bulk processes.
- Manage multiple services simultaneously.

## Connection

You can manage Microsoft 365 by using the Azure AD PowerShell for Graph (AzureAD) module or the Microsoft Azure Active Directory Module for Windows PowerShell (MSOnline) module. The Azure AD PowerShell for Graph module is the newer module and is generally preferred over the Azure Active Directory Module for Windows PowerShell module. However, some functionality in the Azure Active Directory Module for Windows PowerShell module isn't replicated in the Azure AD PowerShell for Graph module. Depending on your task, you might need to install and use both modules.

### Azure AD PowerShell for Graph

Azure AD PowerShell for Graph is the newest PowerShell module for managing Microsoft 365 and has some features that aren't available in the older Azure Active Directory Module for Windows PowerShell module. As a result, when using PowerShell to manage Microsoft 365, the Azure AD module is preferred.

All cmdlets provided by the Azure AD PowerShell for Graph module have AzureAD in the name of the cmdlet. For example, Get-AzureADUser.

The AzureAD cmdlets use the Azure AD Graph API to access and modify data. The Azure AD Graph API is a REST API that can be accessed directly by web requests. The AzureAD cmdlets simplify this process for you.

You can install the Azure AD PowerShell for Graph module from the PowerShell Gallery by running the following command:

``` pwsh
Install-Module AzureAD
```

After the Azure AD PowerShell for Graph module is installed, you can connect to Microsoft 365 by running the following command:

``` pwsh
Connect-AzureAD
```

When you connect to Microsoft 365, you're prompted for a username and password to sign in. You need to sign in with a user account that has sufficient privileges to perform the actions. You might also be prompted for multifactor authentication.

### Microsoft Graph PowerShell SDK
Microsoft has announced that future management interface development will be focused on the Microsoft Graph API. This is a web-based API that's separate from the Azure AD Graph API used by the Azure AD PowerShell for Graph module. The Azure AD Graph API will cease updates after June 2022. All future development will be in the Microsoft Graph API.

To access the Microsoft Graph API, you can use Invoke-WebRequest, but this process is difficult. To simplify the process of using Microsoft Graph API, you can use the Microsoft Graph PowerShell SDK (Microsoft.Graph).

You can install the Microsoft.Graph module from the PowerShell Gallery by running the following command:

``` pwsh
Install-Module Microsoft.Graph
```

After the Microsoft.Graph module is installed, you can connect to Microsoft 365 by running the following command:

``` pwsh
Connect-MgGraph -Scopes "User.ReadWrite.All"
```

When you connect by using Microsoft.Graph, the scope specifies the permissions required. If your user account hasn't been assigned the necessary permissions already, an administrator needs to grant the permissions. There are many permission scopes available based on the type of object to be managed and the actions allowed.

The Microsoft.Graph module creates cmdlets for the available Microsoft Graph options. This avoids much of the complexity that's typically required when using web-based APIs where you need to understand which URL to use and send data in a specific format. The Microsoft.Graph module provides you with cmdlets and parameters as the user interface.

Cmdlets provided by the Microsoft.Graph module have Mg in the name of the cmdlet. For example, Get-MgUser.

Additional reading: The remainder of this module focuses on using the AzureAD and Azure Active Directory Module for Windows PowerShell modules. For more information about using the Microsoft.Graph module, refer to [Get started with the Microsoft Graph PowerShell SDK](https://learn.microsoft.com/en-us/powershell/microsoftgraph/get-started?view=graph-powershell-1.0).

### Azure Cloud Shell
As an alternative to installing and maintaining the AzureAD and Azure Active Directory Module for Windows PowerShell modules in multiple locations, you can use Azure Cloud Shell. Cloud Shell is a prompt with PowerShell functionality that you can access through a web browser. The Microsoft 365 admin center provides a link to open Cloud Shell.

Many PowerShell modules that are used to manage Microsoft 365 services are automatically installed in the shell. You must have an Azure subscription to use Cloud Shell.

Additional reading: For more information about Cloud Shell, see [Overview of Azure Cloud Shell](az-shell.md).
