In Microsoft 365, roles are used to assign administrative permissions to user accounts. The Global Administrator role gives users permission to manage all aspects of Microsoft 365. Other roles allow users to manage only a subset of Microsoft 365 features. The following table lists some of the Microsoft 365 roles that can be assigned to users.

|Role|	Description|
| --- | --- |
|Global Reader|	Can review settings for all aspects of Microsoft 365.|
|Exchange Administrator|	Can review and manage settings for Exchange Online.|
|Helpdesk admin|	Can reset passwords for non-administrative accounts, force non-administrative users to sign out, manage service requests, and monitor service health.|
|Service support admin|	Can open and manage service requests, review and share message center posts, and monitor service health.|
|SharePoint Administrator|	Can review and manage settings for SharePoint Online.|
|Teams Administrator|	Can review and manage settings for Microsoft Teams.|
|User Administrator|	Can review and manage user accounts.|

Role names can vary depending on whether you review them in the web-based admin consoles, AzureAD cmdlets, or Msol cmdlets.

## Managing roles with AzureAD cmdlets
AzureAD cmdlets require you to identify whether a role is already in use before you can assign it to a user. If no users have been assigned to a role, then it exists only as a template, and you need to enable the role before you can add users to it. You can use the Get-AzureADDirectoryRole cmdlet to review the roles that are enabled. Use the Get-AzureADDirectoryRoleTemplate cmdlet to review the roles that aren't yet enabled.

The following example depicts how to enable the User Administrator role. When you enable the role, you need to refer to the object ID of the template:

``` pwsh
$roleTemplate = Get-AzureADDirectoryRoleTemplate | Where {$_.displayName -eq 'User Administrator'}
Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
```

After you enable the role, you can add a role member to assign administrative permissions by using the Add-AzureADDirectoryRoleMember cmdlet. The following example depicts how to add an account to the User Administrator role. The -ObjectId parameter refers to the ObjectID of the role. The -RefObjectId parameter refers to the ObjectID of the user account:

``` pwsh
$user = Get-AzureADUser -ObjectID AbbieP@adatum.com
$role = Get-AzureADDirectoryRole | Where {$_.displayName -eq 'User Administrator'}
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $user.ObjectID
```

To list existing members of a role, use Get-AzureADDirectoryRoleMember. To remove a member from a role, use Remove-AzureADDirectoryRoleMember.

