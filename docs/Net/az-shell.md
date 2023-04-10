Instead of using the locally installed PowerShell module for managing Azure resources, you could also use the Azure Cloud Shell environment. This option lets you use PowerShell or Bash environments and commands to manage Azure resources. Azure Cloud Shell is available in the Azure portal and also in the Microsoft 365 admin portal.

Azure Cloud Shell is an interactive, browser-accessible shell for managing Azure resources. It provides the flexibility of choosing the shell experience that best suits the way you work. Linux users can opt for a Bash experience, while Windows users can opt for PowerShell.

Cloud Shell enables access to a browser-based, command-line experience built with Azure management tasks in mind. You can use Cloud Shell to work untethered from a local machine in a way only the cloud can provide.

The main characteristics of Azure Cloud Shell are that it:

Is a temporary environment that requires a new or existing Azure file share to be mounted.
Offers an integrated graphical text editor based on the open-source Monaco Editor.
Authenticates automatically for instant access to your resources.
Runs on a temporary host provided on a per-session, per-user basis.
Times out after 20 minutes without interactive activity.
Requires a resource group, storage account, and Azure file share.
Uses the same Azure file share for both Bash and PowerShell.
Is assigned to one machine per user account.
Persists $HOME using a 5-GB image held in your file share.
Has permissions that are set as a regular Linux user in Bash.
You can access the Cloud Shell in three ways:

Direct link. Open a browser and refer to [https://shell.azure.com](https://shell.azure.com).

Azure portal. Select the Cloud Shell icon on the Azure portal.

Code snippets. On [Microsoft Docs](https://learn.microsoft.com/en-us/) and [Microsoft Learn](https://learn.microsoft.com/en-us/training/), select the Try It option that displays with the Azure CLI and Azure PowerShell code snippets:

``` pwsh
az account show
Get-AzSubscription
```
The Try It option opens the Cloud Shell directly alongside the documentation using Bash (for Azure CLI snippets) or PowerShell (for Azure PowerShell snippets).

To run the command:

1. Use Copy in the code snippet.
2. Use Ctrl+Shift+V (Windows/Linux) or Cmd+Shift+V (macOS) to paste the command.
3. Select Enter.

Cloud Shell provisions machines on a per-request basis, and as a result, machine state won't persist across sessions. Cloud Shell is built for interactive sessions, and therefore, shells automatically terminate after 20 minutes of shell inactivity.


## Secure automatic authentication
Cloud Shell securely and automatically authenticates account access for the Azure CLI and Azure PowerShell. This helps you gain quick and more secure access to your resources.

## $HOME persistence across sessions
To persist files across sessions, Cloud Shell moves through attaching an Azure file share on the first launch. When this completes, Cloud Shell will automatically attach your storage (mounted as $HOME\clouddrive) for all future sessions. Additionally, your $HOME directory is persisted as an .img in your Azure file share. Files outside of $HOME and machine state aren't persisted across sessions. Depending on the scenario, you should use recommended best practices when storing secrets such as SSH keys. Services such as Azure Key Vault have tutorials for setup.

## Azure drive (Azure:)
PowerShell in Cloud Shell provides the Azure drive (Azure:). You can switch to the Azure drive with cd Azure: and back to your home directory with cd ~. The Azure drive enables easier discovery and navigation of Azure resources such as compute, network, and storage, similar to file system navigation. You can continue to use familiar Azure PowerShell cmdlets to manage these resources, regardless of the drive you're in. Any changes to the Azure resources, whether they're made directly in the Azure portal or by using Azure PowerShell cmdlets, are reflected in the Azure drive. You can run dir -Force to refresh your resources.

## Manage Exchange Online
PowerShell in Cloud Shell contains a private build of the Exchange Online module. Run Connect-EXOPSSession to get your Exchange cmdlets. By using these cmdlets, you can manage your Exchange Online instance running in the Microsoft 365 environment.

## Deep integration with open-source tooling
Cloud Shell includes preconfigured authentication for various open-source tools. The following table lists the various tool categories and interfaces you can use.

|Category|	Names|
| --- | --- |
|Linux tools|	bash, zsh, sh, tmux, and dig|
|Azure tools|	Azure CLI and Azure classic CLI, AzCopy, Azure Functions CLI, Service Fabric CLI, Batch Shipyard, and blobxfer|
|Text editors|	code (Cloud Shell editor), vim, nano, and emacs|
|Source control|	git|
|Build tools|	make, maven, npm, and pip|
|Containers|	Docker Machine, Kubectl, Helm, and DC/OS CLI|
|Databases|	MySQL client, PostgreSql client, sqlcmd Utility, and mssql-scripter|
|Other|	iPython Client, Cloud Foundry CLI, Terraform, Ansible, Chef InSpec, Puppet Bolt, HashiCorp Packer, and Office 365 CLI|

## Configure the Cloud Shell
Access the Azure Portal.
Select the Cloud Shell icon on the banner.
On the Welcome to Azure Cloud Shell page, notice your selections for Bash or PowerShell. Select PowerShell.
The Azure Cloud Shell requires an Azure file share to persist files. If you have time, select Learn more to obtain information about the Cloud Shell storage and the associated pricing.
Select your Subscription, and then select Create Storage.

### Experiment with Azure PowerShell
Wait for your storage to be created and your account to be initialized.
At the PowerShell prompt, enter Get-AzSubscription to review your subscriptions.
Enter Get-AzResourceGroup to review resource group information.

### Experiment with the Bash shell
Use the drop-down list to switch to the Bash shell and confirm your choice.
At the Bash shell prompt, enter az account list to review your subscriptions. Also, try tab completion.
Enter az resource list to review resource information.

### Experiment with the Cloud Editor
To use the Cloud Editor, enter code .. You can also select the curly braces icon.
Select a file from the navigation pane; for example, .profile.
On the editor banner, notice the selections for Settings, such as Text Size, Font, and Upload/Download files.
Notice the ellipses (...) for Save, Close Editor, and Open File.
After experimenting, you can close the Cloud Editor.
Close Azure Cloud Shell.