The Compare-Object cmdlet (which has the alias diff) allows you to compare two objects and display the differences between them. In this sense, it allows you to compare a snapshot of processes (or services, or anything else) from a system with a more current snapshot.

For example, you can generate a snapshot of the system's processes:
``` pwsh
get-process | export-clixml processes.xml
```
... and later, compare it with the processes that are running at that time:
``` pwsh
diff -Ref (Import-Clixml .\processes.xml) -Diff (get-process) -Property name
```

The previous command is interpreted as follows:

- Parentheses are interpreted the same as in algebra: the commands that are inside parentheses are executed first, and their results are passed to the outermost command.
- The -Ref parameter (-ReferenceObject in full) indicates the object that will be used as the basis for the comparison (in this case, the snapshot that was previously saved).
- The -Diff parameter (-DifferenceObject in full) indicates the object that will be compared against the base (in this case, the current list of processes).
- The -Property parameter indicates the property that is being compared, in this case, the names of the processes.

The output of the command is similar to this:

```
name                                                           SideIndicator
----                                                           -------------
WindowsInternal.ComposableShell.Experiences.TextInput.InputApp =>           
YourPhone                                                      =>           
LockApp                                                        <=           
Microsoft.Photos                                               <=           
notepad                                                        <=           
```


The names of the processes appear, with an arrow pointing to the left or right:
• If the arrow points to the right, it indicates a name that is in the new snapshot but not in the original.
• If the arrow points to the left, it indicates a name that is in the original snapshot but not in the new one.