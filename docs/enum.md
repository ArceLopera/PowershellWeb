Enumeration is the process of performing a task on each object, one at a time, in a collection. Frequently, PowerShell doesn't require you to explicitly enumerate objects. For example, if you need to stop every running Notepad process on your computer, you can run either of these two commands:

``` pwsh
Get-Process –Name Notepad | Stop-Process

Stop-Process –Name Notepad

```

The ForEach-Object command performs enumeration. It has two common aliases: ForEach and %. Like Where-Object, ForEach-Object has a basic syntax and an advanced syntax.

## Basic Syntax

In the basic syntax, you can run a single method or access a single property of the objects that were piped into the command.

``` pwsh
Get-ChildItem –Path C:\Encrypted\  File | ForEach Object  MemberName Encrypt
```

With this syntax, you don't include the parentheses after the member name if the member is a method. Because this basic syntax is meant to be short, you'll frequently notice it without the -MemberName parameter name, and you might notice it with an alias instead of the full command name. For example, both of the following commands perform the same action:

``` pwsh
Get-ChildItem –Path C:\Encrypted\ -File | ForEach Encrypt

Get-ChildItem –Path C:\Encrypted\ -File | % Encrypt
```
You might not run into many scenarios that require enumeration. Each new operating system and version of PowerShell introduces new PowerShell commands. Newer operating systems typically introduce new commands which perform actions that previously required enumeration.

### Limitations of the basic syntax
The basic syntax can access only a single property or method. It can't perform logical comparisons that use -and or -or; it can't make decisions; and it can't run any other commands or code. For example, the following command doesn't run, and it produces an error:

``` pwsh
Get-Service | ForEach -MemberName Stop -and -MemberName Close
```

## Advanced Syntax

The advanced syntax for enumeration provides more flexibility and functionality than the basic syntax. Instead of letting you access a single object member, you can run a whole script. That script can include one command, or it can include many commands in sequence.

``` pwsh
Get-ChildItem –Path C:\ToEncrypt\ -File | ForEach-Object –Process { $PSItem.Encrypt() }
```

The ForEach-Object command can accept any number of objects from the pipeline. It has the -Process parameter that accepts a script block. This script block runs one time for each object that was piped in. Every time that the script block runs, the built-in variable $PSItem (or $_) can be used to refer to the current object. In the preceding example command, the Encrypt() method of each file object runs.

When used with the advanced syntax, method names are always followed by opening and closing parentheses, even when the method doesn't have any input arguments. For methods that do need input arguments, provide them as a comma-separated list inside the parentheses. Don't include a space or other characters between the method name and the opening parenthesis.

## Advanced techniques
In some situations, you might need to repeat a particular task a specified number of times. You can use ForEach-Object for that purpose when you pass it an input that uses the range operator. The range operator is two periods (..) with no space between them. For example, run the following command:

``` pwsh
1..100 | ForEach-Object { Get-Random }
```

In the preceding command, the range operator produces integer objects from 1 through 100. Those 100 objects are piped to ForEach-Object, forcing the script block to run 100 times. However, because neither $_ nor $_PSItem display in the script block, the actual integers aren't used. Instead, the Get-Random command runs 100 times. The integer objects are used only to set the number of times the script block runs.