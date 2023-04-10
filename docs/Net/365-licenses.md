After you create user accounts, you need to assign a license to the user account. The license determines which Microsoft 365 services the user has access to. The licenses in the tenant vary depending on which licenses you've purchased.

Some of the common enterprise licenses are:

Microsoft Office 365 E3
Office 365 E5
Microsoft 365 E3
Microsoft 365 E5
Each license includes multiple services that can be enabled or disabled. For example, an Office 365 E3 license includes access to Exchange Online, Microsoft Teams, SharePoint Online, and others. All services in a license are enabled by default.

When you configure licensing by using Windows PowerShell, you need to refer to the license and service plans by either a string ID or a globally unique identifier (GUID). For example, the Microsoft 365 E3 license has a string ID of SPE_E3 and a GUID of 05e9a617-0261-4cee-bb44-138d3ef5d965. The AzureAD cmdlets for licensing use the GUID, and the Msol cmdlets use the string ID.

Additional reading: It's possible to query the string ID or GUID from your tenant, but you can also find a list of licenses and service plans on [Product names and service plan identifiers for licensing](https://learn.microsoft.com/en-us/azure/active-directory/enterprise-users/licensing-service-plan-reference).

Some organizations use group-based licensing, which assigns licenses to user accounts automatically, based on group membership. If a license has been assigned by group-based licensing, you can't modify that license assignment by using PowerShell.

## Reviewing licenses by using AzureAD cmdlets
You can use the Get-AzureADSubscribedSku cmdlet to review the licenses available in your Microsoft 365 tenant. The following example retrieves licenses and displays information about them. The SkuId property is the GUID for the license:

``` pwsh
Get-AzureADSubscribedSku | Select-Object -Property Sku*,ConsumedUnits -ExpandProperty PrepaidUnits
```

The service plans for a license are stored in the ServicePlans property. The following example places all licenses in a variable and then displays the service plans for the first item in the array. The provisioning status for the service plan indicates whether it's enabled or disabled for that user:

``` pwsh
$sku = Get-AzureADSubscribedSku
$sku[0].ServicePlans
```

## Managing licenses by using Azure AD cmdlets
You can use the Set-AzureADUserLicense cmdlet to assign a license to a user. Licenses to be added are contained in an AssignedLicenses object that you create. For each license that you want to add, you create an AssignedLicense object and add it to the AddLicenses property of the AssignedLicenses object. After the AssignedLicenses object is configured, you apply it to the user account. The following example creates a license object for Microsoft 365 E3, and then assigns it to a user:

``` pwsh
$License = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicense
$License.SkuId = '05e9a617-0261-4cee-bb44-138d3ef5d965'
$LicensesToAssign = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicenses
$LicensesToAssign.AddLicenses = $License
Set-AzureADUserLicense -ObjectId AbbieP@adatum.com -AssignedLicenses $LicensesToAssign
```

If you want to disable service plans for a user, you need to add the GUID for the service plans to the DisabledPlans property of the license object. The following example depicts how to disable the YAMMER_ENTERPRISE and SWAY service plans in a license:

``` pwsh
$License.DisabledPlans = '7547a3fe-08ee-4ccb-b430-5077c5041653'
$License.DisabledPlans.Add('a23b959c-7ce8-4e57-9140-b90eb88a9e97')
```

To remove a license, you add a license to the RemoveLicenses property of an AssignedLicenses object. The following example removes a Microsoft 365 E3 license from a user account:

``` pwsh
$License = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicense
$License.SkuId = '05e9a617-0261-4cee-bb44-138d3ef5d965'
$LicensesToAssign = New-Object -TypeName Microsoft.Open.AzureAD.Model.AssignedLicenses
$LicensesToAssign.RemoveLicenses = $License
Set-AzureADUserLicense -ObjectId AbbieP@adatum.com -AssignedLicenses $LicensesToAssign
```

You can't add multiple licenses to a user that has conflicting components. For example, you can't assign a Microsoft 365 E5 license to a user that already has a Microsoft 365 E3 license. However, you can create an AssignedLicenses object that removes the Microsoft 365 E3 license and adds the Microsoft 365 E5 license at the same time.     