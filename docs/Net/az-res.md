Azure virtual machines (VMs) provide a fully configurable and flexible computing environment. You can create and manage these VMs by using the Azure portal, the Windows PowerShell with Az module, or the Cloud Shell environment.

Besides managing Azure VMs, one of the most common usages of the PowerShell Az module is to manage Azure storage accounts and Azure subscriptions.

## Create an Azure VM

To create a new Azure virtual machine (VM) with PowerShell commands, you can use the locally installed Windows PowerShell with Az module, or you can use the Cloud Shell environment that's available in Azure portal. If you choose to use your locally installed PowerShell, it's recommended that you use Windows PowerShell 7.1. You should also install the Az module, so you can have Azure-related commands available. Also, when using locally installed PowerShell, you first need to use the Connect-AzAccount command to authenticate and connect to your Azure tenant. When you run this command in your PowerShell environment, you'll be prompted to authenticate. You need to use credentials from your Azure tenant, with privileges that allow you to create the resources needed for Azure VMs.

### Create a resource group
An Azure resource group is a logical container into which Azure resources are deployed and managed. You must create a resource group before you create a VM. In the following example, a resource group named myResourceGroup is created in the West Europe region:

``` pwsh
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "westeurope"
```
The resource group is later used when creating or modifying a VM or the resources attached to a VM.

### Create an Azure VM
The New-AzVM cmdlet creates a VM in Azure. This cmdlet uses a VM object as input. Use the New-AzVMConfig cmdlet to create a virtual machine object. When you're creating a VM, several options are available, such as operating system image, network configuration, and administrative credentials. You can use other cmdlets to configure the VM, such as Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface, and Set-AzVMOSDisk.

Additional reading: For more information about the parameters that you can use with the New-AzVM command, refer to [New-AzVM](https://learn.microsoft.com/en-us/powershell/module/az.compute/new-azvm?view=azps-9.6.0&viewFallbackFrom=azps-6.2.0).

Before you run the New-AzVM command, you need to specify the credentials that you'll use to sign in to the newly created Azure VM. The credentials that you specify during this process will be assigned with local administrative privileges on the VM you're creating. It's easiest to store these credentials in a variable, before creating a new Azure VM. To do this, run this command:

``` pwsh
$cred = Get-Credential
```

When you run this command, you'll be prompted to provide the username and password for the Azure VM. These credentials will be stored in the $cred variable.

After you store administrative credentials, you need to define parameters for the new VM. You don't need to provide all the parameters that New-AzVM supports. Most of them are optional, and if you don't provide them, their default values will be selected. You can also change most of these parameters later.

You can choose to provide VM parameters directly with the New-AzVM command, or you can define these parameters in a variable, and then use this variable with the New-AzVM command.

The following code depicts an example of defining VM parameters:

``` pwsh
$vmParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'TestVM'
  Location = 'westeurope'
  ImageName = 'Win2016Datacenter'
  PublicIpAddressName = 'TestPublicIp'
  Credential = $cred
  OpenPorts = 3389
}
```

When you define VM parameters as the previous example depicts, you can then use the following command to create a new Azure VM, based on these parameters:

``` pwsh
New-AzVM @vmParams
```

Alternatively, you can also choose to provide VM parameters directly with New-AzVM as in the following example:

``` pwsh
New-AzVm `
    -ResourceGroupName "myResourceGroup"
    -Name "myVM"
    -Location "EastUS"
    -VirtualNetworkName "myVnet"
    -SubnetName "mySubnet"
    -SecurityGroupName "myNetworkSecurityGroup"
    -PublicIpAddressName "myPublicIpAddress"
    -Credential $cred
```

### Connect to the Azure VM
After a new Azure VM is created, you need to connect to it to verify the deployment. After the deployment has completed, create a remote desktop connection with the VM.

Run the following commands to return the public IP address of the VM. Take note of this IP address so you can connect to it with your browser to test web connectivity in a future step.

``` pwsh
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup"  | Select IpAddress
```

To create a remote desktop session with the VM, use the following command on your local machine. Replace the IP address with the publicIPAddress of your VM. When prompted, enter the credentials you used when creating the VM.

``` pwsh
mstsc /v:<publicIpAddress>
```

When you run this command, you'll be prompted for credentials to connect to the VM. In the Windows Security window, select More choices, and then select Use a different account. Enter the username and password you created for the VM, and then select OK. After you connect to your Azure VM through Remote Desktop Protocol (RDP), you'll be able to manage it the same way as any other computer.

## Modifying VM sizes
The VM size determines the amount of compute resources such as CPU, GPU, and memory that are made available to the VM. You should create VMs using a VM size that's appropriate for the workload. If a workload increases, you can also resize existing VMs.

To review a list of VM sizes available in a particular region, use the Get-AzVMSize command. For example:
``` pwsh
Get-AzVMSize -Location "EastUS"
```

After a VM has been deployed, you can resize it to increase or decrease resource allocation. Before resizing a VM, check if the size you want is available on the current VM cluster. The Get-AzVMSize command returns a list of sizes:

``` pwsh
Get-AzVMSize -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

If your preferred size is available, you can resize the VM from a powered-on state; however, it's rebooted during the operation. The following example depicts how to change VM size to the Standard_DS3_v2 size profile:

``` pwsh
$vm = Get-AzVM -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## Management tasks
During the lifecycle of a VM, you might want to run management tasks such as starting, stopping, or deleting a VM. Additionally, you might want to create scripts to automate repetitive or complex tasks. You can use Azure PowerShell to perform many common management tasks by using the command line or scripts.

To stop and deallocate a VM with Stop-AzVM, you can run the following command:

``` pwsh
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Force
```

To start a VM, you can run the following command:

``` pwsh
Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

If you want to delete everything inside of a resource group, including VMs, you can run the following command:

``` pwsh
Remove-AzResourceGroup -Name "myResourceGroupVM" -Force
```

## Adding disks to Azure VMs
When you create an Azure VM, two disks are automatically attached to the VM:

+ Operating system disk. These disks can be sized up to 4 terabytes and host the VM's operating system.
+ Temporary disk. These disks use a solid-state drive that's located on the same Azure host as the VM. 

Temporary disks are highly performant and might be used for operations such as temporary data processing.
You can add additional data disks for installing applications and storing data. You should use data disks in any situation that requires durable and responsive data storage. The size of the VM determines how many data disks can be attached to it.

To add a data disk to an Azure VM after you create it, you need to define disk configuration by using the New-AzDiskConfig command. You then need to use the New-AzDisk and Add-AzVMDataDisk commands to add a new disk to the VM, as the following example depicts:

``` pwsh
$diskConfig = New-AzDiskConfig -Location "EastUS" -CreateOption Empty -DiskSizeGB 128
$dataDisk = New-AzDisk -ResourceGroupName "myResourceGroupDisk" -DiskName "myDataDisk" -Disk $diskConfig

$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
$vm = Add-AzVMDataDisk -VM $vm -Name "myDataDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1

Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## Storage

You can use Azure PowerShell to manage Azure-related storage. Before you start managing your storage, you should first create a storage account, if you don't have one. Usually, storage accounts are created automatically when you create other Azure resources such as Azure virtual machines (VMs).

You can create a standard, general-purpose storage account with locally redundant storage (LRS) replication by using New-AzStorageAccount. Next, get the storage account context that defines the storage account you want to use. When acting on a storage account, reference the context, instead of repeatedly passing in the credentials. Use the following example to create a storage account called mystorageaccount with LRS and blob encryption, which is enabled by default.

``` pwsh
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```

Blobs are always uploaded into a container. You can organize groups of blobs the way you organize your files on your computer in folders.

Set the container name, and then create the container by using New-AzStorageContainer. Set the blob permissions to allow public access of the files. The container name in the following example is quickstartblobs.

``` pwsh
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

You can use the Set-AzStorageAccount cmdlet to modify an Azure Storage account. You can use this cmdlet to modify the account type, update a customer domain, or set tags on a Storage account.

For example, to set the storage account type you should use the following command:

``` pwsh
Set-AzStorageAccount -ResourceGroupName "MyResourceGroup" -AccountName "mystorageaccount" -Type "Standard_RAGRS"
```

To set custom domain for existing storage account, you can use the following command:

``` pwsh
Set-AzStorageAccount -ResourceGroupName "MyResourceGroup" -AccountName "mystorageaccount" -CustomDomainName "www.contoso.com" -UseSubDomain $True
```

Additional reading: To learn more about the available cmdlets for managing Azure storage, refer to [Az.Storage](https://learn.microsoft.com/en-us/powershell/module/az.storage/?view=azps-9.6.0&viewFallbackFrom=azps-6.2.0).

## Azure Subscriptions

Most Azure users will only ever have a single subscription. However, if you're part of more than one organization or your organization has divided up access to certain resources across groupings, you might have multiple subscriptions within Azure.

In Azure PowerShell, accessing the resources for a subscription requires changing the subscription associated with your current Azure session. You can do this by modifying the active session context, which is the information about which tenant, subscription, and user the cmdlets should be run against. To change subscriptions, you need to first retrieve an Azure PowerShell Context object with Get-AzSubscription, and then change the current context with Set-AzContext.

The Get-AzSubscription cmdlet gets the subscription ID, subscription name, and home tenant for subscriptions that the current account can access.

To get all Azure subscriptions active on all tenants, run the following command:

``` pwsh
Get-AzSubscription

Name                               Id                      TenantId                        State
----                               --                      --------                        -----
Subscription1                      yyyy-yyyy-yyyy-yyyy     aaaa-aaaa-aaaa-aaaa             Enabled
Subscription2                      xxxx-xxxx-xxxx-xxxx     aaaa-aaaa-aaaa-aaaa             Enabled
Subscription3                      zzzz-zzzz-zzzz-zzzz     bbbb-bbbb-bbbb-bbbb             Enabled
```

To focus on subscriptions assigned to a specific tenant, run the following command:


``` pwsh
Get-AzSubscription -TenantId "aaaa-aaaa-aaaa-aaaa"

Name                               Id                      TenantId                        State
----                               --                      --------                        -----
Subscription1                      yyyy-yyyy-yyyy-yyyy     aaaa-aaaa-aaaa-aaaa             Enabled
Subscription2                      xxxx-xxxx-xxxx-xxxx     aaaa-aaaa-aaaa-aaaa             Enabled
```
The Set-AzContext cmdlet sets authentication information for cmdlets that you run in the current session. The context includes tenant, subscription, and environment information.

To set the subscription context, run the following command:

``` pwsh
Set-AzContext -Subscription "xxxx-xxxx-xxxx-xxxx"

Name    Account             SubscriptionName    Environment         TenantId
----    -------             ----------------    -----------         --------
Work    test@outlook.com    Subscription1       AzureCloud          xxxxxxxx-x...
```

The next example depicts how to get a subscription in the currently active tenant and set it as the active session:

``` pwsh
$context = Get-AzSubscription -SubscriptionId ...
Set-AzContext $context
```