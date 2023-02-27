PowerShell provides a variety of cmdlets for working with files and folders. In PowerShell, an "item" is a generic term used to refer to a file, folder, registry key, or other object that can be manipulated by cmdlets in the PowerShell environment. The term "item" is used in cmdlet names such as Get-Item, Set-Item, and Remove-Item, which can be used to manipulate different types of items in PowerShell.

## Items
Here are some examples of how to use these cmdlets:

|Cmdlet |	Description | Alias|
| :--- | :--- | :--|
|[Get-ChildItem](#get-childitem)	| Gets the items and child items in one or more specified locations|gci, dir, ls|
|[Set-Location](#set-location)	|Sets the working location to a specified location|cd, sl, chdir|
|[Copy-Item](#copy-item)	|Copies an item from one location to another location|cp, copy|
|[Move-Item](#move-item)	|Moves an item from one location to another location|mv, move|
|[Remove-Item](#remove-item)	|Deletes the specified items|ri, rmdir, rd, del, erase|
|[New-Item](#new-item)	|Creates a new item at the specified location|ni|
|[Rename-Item](#rename-item)	|Renames an item at the specified location|rni|

Note that some of these cmdlets have additional parameters that can be used to control their behavior. For example, you can use the -Recurse parameter with Remove-Item to delete a directory and all its contents. You can also use wildcards with many of these cmdlets to perform operations on multiple files or directories at once.

### Get-ChildItem 

This cmdlet lists the contents of a directory. By default, it displays both files and directories.
Example:

``` pwsh
Get-ChildItem C:\Users
```

### Set-Location 

This cmdlet changes the current working directory.
Example:

``` pwsh
Set-Location C:\Users\UserName\Desktop
```

### Copy-Item 

This cmdlet copies a file or directory to a new location.
Example:

``` pwsh
Copy-Item C:\Users\UserName\Desktop\MyFile.txt C:\Temp\
```

### Move-Item

This cmdlet moves a file or directory to a new location.
Example:

``` pwsh
Move-Item C:\Users\UserName\Desktop\MyFile.txt C:\Temp\
``` 

### Rename-Item

This cmdlet renames a file or directory.
Example:

``` pwsh
Rename-Item C:\Temp\MyFile.txt MyNewFile.txt
```

### New-Item

This cmdlet creates a new file or directory.
Example:

``` pwsh
New-Item -ItemType File -Path C:\Temp\NewFile.txt
```

### Remove-Item

This cmdlet deletes a file or directory.
Example:

``` pwsh
Remove-Item C:\Temp\MyFile.txt
```

## Properties

In PowerShell, cmdlets whose names end with "Property" are used to retrieve or manipulate the properties of objects. Properties are attributes or characteristics of an object, such as its name, size, or creation date.

The most common cmdlet that ends with "Property" is the Get-ItemProperty cmdlet, which is used to retrieve the properties of a file, folder, or registry key. For example, you can use the Get-ItemProperty cmdlet to retrieve the version number of a file or the registry key value for a particular setting.

Other examples of cmdlets that end with "Property" include the Get-ServiceProperty cmdlet, which is used to retrieve the properties of a Windows service, and the Get-ADUserProperty cmdlet, which is used to retrieve the properties of an Active Directory user.

In general, cmdlets that end with "Property" are useful for retrieving specific information about an object, which can be used in scripts or further manipulated using other PowerShell cmdlets.

|Cmdlet |	Description |
| :--- | :--- |
|New-ItemProperty	|Creates a new property for an item at the specified location|
|Get-ItemProperty	|Gets the properties of the specified items|
|Set-ItemProperty	|Sets the value of a property for the specified item|
|Remove-ItemProperty	|Removes a property from an item at the specified location|


## Paths

In PowerShell, cmdlets whose names end with "Path" are used to work with file and directory paths. These cmdlets are commonly used to manipulate or retrieve information about file and directory paths, such as their locations, names, or extensions.

One of the most commonly used cmdlets that ends with "Path" is the Split-Path cmdlet, which is used to split a file or directory path into its individual components, such as the directory, filename, and extension. For example, the command "Split-Path C:\Temp\test.txt -Leaf" would return "test.txt", while "Split-Path C:\Temp\test.txt -Parent" would return "C:\Temp".

Another example is the Join-Path cmdlet, which is used to combine two or more path strings into a single path. For example, the command "Join-Path C:\Temp test.txt" would return "C:\Temp\test.txt".

Other examples of cmdlets that end with "Path" include the Resolve-Path cmdlet, which is used to resolve a path to its full, absolute form, and the Test-Path cmdlet, which is used to test whether a file or directory path exists.

In general, cmdlets that end with "Path" are useful for manipulating file and directory paths, which can be useful for scripting or automating tasks that involve working with files and directories.


|Cmdlet |	Description |
| :--- | :--- |
|Test-Path	|Determines whether all elements of a path exist|
|Join-Path	|Joins two or more parts of a path into a single path|
|Split-Path	|Returns specific parts of a path|
|Convert-Path	|Converts a path from a relative path to a full path or from a full path to a relative path|