Windows PowerShell provides cmdlets that you can use to create, modify, and delete Active Directory Domain Services (AD DS) Organizational Units (OUs). Like the cmdlets for users, groups, and computers, you can use these cmdlets for individual operations or as part of a script to perform bulk operations. OU management cmdlets have the text “organizationalunit” in the name.

|Cmdlet|	Description|
| --- | --- |
|New-ADOrganizationalUnit|	Creates an OU|
|Set-ADOrganizationalUnit|	Modifies properties of an OU|
|Get-ADOrganizationalUnit|	Displays properties of an OU|
|Remove-ADOrganizationalUnit|	Deletes an OU|

## Creating new OUs
You can use the New‑ADOrganizationalUnit cmdlet to create a new OU to represent departments or physical locations within your organization.

|Parameter|	Description|
| --- | --- |
|‑Name|	Defines the name of a new OU|
|‑Path|	Defines the location of a new OU|
|‑ProtectedFromAccidentalDeletion|	Prevents anyone from accidentally deleting an OU; the default value is $true|

The following example is a command to create a new OU:

``` pwsh
New-ADOrganizationalUnit -Name Sales -Path "ou=marketing,dc=adatum,dc=com" -ProtectedFromAccidentalDeletion $true
```

Windows PowerShell provides cmdlets that you can use to create, modify, and delete Active Directory Domain Services (AD DS) Organizational Units (OUs). These too can be used for individual operations or as part of a script to perform bulk operations.