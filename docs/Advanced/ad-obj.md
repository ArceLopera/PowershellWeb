## Active Directory object cmdlets
You'll sometimes need to manage Active Directory objects that don't have their own management cmdlets, such as contacts. You might also want to manage multiple object types in a single operation, such as moving users and computers from one OU to another OU. The Active Directory module provides cmdlets that allow you to create, delete, and modify these objects and their properties. Because these cmdlets can manage all objects, they repeat some functionality of the cmdlets for managing users, computers, groups, and OUs.

*-ADObject cmdlets sometimes perform faster than cmdlets that are specific to object type. This is because those cmdlets add the cost of filtering the set of applicable objects to their operations. Cmdlets for changing generic Active Directory objects have the text “Object” in the noun part of the name.

|Cmdlet|	Description|
| --- | --- |
|New-ADObject|	Creates a new Active Directory object|
|Set-ADObject|	Modifies properties of an Active Directory object|
|Get-ADObject|	Displays properties of an Active Directory object|
|Remove-ADObject|	Deletes an Active Directory object|
|Rename-ADObject|	Renames an Active Directory object|
|Restore-ADObject|	Restores a deleted Active Directory object from the Active Directory Recycle Bin|
|Move-ADObject|	Moves an Active Directory object from one container to another container|
|Sync-ADObject|	Syncs an Active Directory object between two domain controllers|

## Creating a new Active Directory object
You can use the New‑ADObject cmdlet to create objects. When using New-ADObject, you must specify the name and the object type.

|Parameter|	Description|
| --- | --- |
|‑Name|	Defines the name of an object|
|‑Type|	Defines the LDAP type of an object|
|‑OtherAttributes|	Defines properties of an object that isn't accessible from other parameters|
|‑Path|	Defines the container in which an object is created|

The following command creates a new contact object:

``` pwsh
New-ADObject -Name "AnaBowmancontact" -Type contact
```

The Active Directory module provides cmdlets that allow you to create, delete, and modify those objects that don't have their own management cmdlets.