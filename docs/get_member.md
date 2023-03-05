The Get-Member cmdlet in PowerShell is an essential tool for exploring objects and their properties, methods, and events. It retrieves the members (properties, methods, and events) of an object and displays their definitions and syntax, which can help users understand the object and how to interact with it.

## Syntax

The syntax for using Get-Member is as follows:

``` pwsh
Get-Member [-InputObject] <PSObject> [-MemberType {Property | Method | Event | NoteProperty | AliasProperty | ScriptProperty | PropertySet | All}] [-Force] [-Static]
```

Here's an explanation of the parameters:

- InputObject: specifies the object for which to retrieve the members. It can be piped to Get-Member or specified as an argument.

- MemberType: specifies the type of members to retrieve. It can be one of the following: Property (instance properties), Method (instance methods), Event (instance events), NoteProperty (extended properties), AliasProperty (aliases), ScriptProperty (script properties), PropertySet (property sets), or All (all members).

- Force: forces Get-Member to retrieve hidden or non-public members.

- Static: retrieves static members of a class.

## Examples


1. View the properties of an object:

    ``` pwsh
    Get-Process | Get-Member -MemberType Property
    ```

    This command retrieves the list of properties associated with the Get-Process cmdlet.

2. View the methods of an object:

    ``` pwsh
    Get-ChildItem | Get-Member -MemberType Method
    ```

    This command retrieves the list of methods associated with the Get-ChildItem cmdlet.

3. Filter the results by name or type:

    ``` pwsh
    Get-Process | Get-Member -Name "<Name>"
    ```

    This command retrieves only the properties with the name "<Name>" from the Get-Process cmdlet.

4. View the static properties of a .NET class:

    ``` pwsh
    [System.Math] | Get-Member -Static -MemberType Property
    ```
    This command retrieves the static properties of the .NET class System.Math.