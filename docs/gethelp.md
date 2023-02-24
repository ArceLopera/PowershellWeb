The Get-Help cmdlet is a powerful tool in PowerShell that provides information about commands, cmdlets, and modules. It has several parameters that allow you to customize the help output to suit your needs. Here are some of the most common parameters for the Get-Help cmdlet with examples:

## 1. -Name 

This parameter specifies the name of the command or cmdlet that you want help with. For example:
    ``` pwsh
    Get-Help -Name Get-ChildItem
    ```
    This command will display help for the Get-ChildItem cmdlet, which is used to list the contents of a directory.

## 2. -Category

This parameter specifies the category of help that you want to display. For example:
    ``` pwsh
    Get-Help -Category Navigation
    ```
    This command will display all cmdlets related to navigation.

## 3. -Detailed 

This parameter displays detailed help information, including examples and parameter descriptions. For example:
    ``` pwsh
    Get-Help -Name Get-ChildItem -Detailed
    ```
    This command will display detailed help information for the Get-ChildItem cmdlet.

## 4. -Examples

This parameter displays examples of how to use the command or cmdlet. For example:
    ```
    Get-Help -Name Get-ChildItem -Examples
    ```
    This command will display examples of how to use the Get-ChildItem cmdlet.

## 5. -Full 

This parameter displays the full help information, including syntax and parameter descriptions. For example:
    ```
    Get-Help -Name Get-ChildItem -Full
    ```
    This command will display the full help information for the Get-ChildItem cmdlet.

## 6. -Parameter

This parameter displays help information for a specific parameter of a command or cmdlet. For example:
    ```
    Get-Help -Name Get-ChildItem -Parameter Path
    ```
    This command will display help information for the Path parameter of the Get-ChildItem cmdlet.

## 7. -Online 
    
This parameter opens the online version of the help file for the command or cmdlet. For example:

    ```
    Get-Help -Name Get-ChildItem -Online
    ```
    This command will open the online version of the help file for the Get-ChildItem cmdlet.

In summary, the Get-Help cmdlet is a versatile tool that can provide a lot of information about commands, cmdlets, and modules in PowerShell. By using the appropriate parameters, you can customize the help output to suit your needs and become more proficient in PowerShell.


## Exercises on Get-Help usage

1. Verify if there are cmdlets that allow converting the output of another cmdlet to HTML format.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    help *html*
    ```
    </details>

2. Verify which cmdlets allow directing output to a printer or a file.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Out-Printer
    Out-File
    ```
    </details>

3. Verify how many cmdlets are used to manage processes.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    help *Process*
    ```
    </details>

4. Which cmdlet can be used to write an entry to an event log?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Write-EventLog
    ```
    </details>

5. Which cmdlets can be used to manage aliases?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    help *alias*
    ```
    </details>

6. Is there a way to keep a transcript of a PowerShell session and save it to a file?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Start-Transcript -Path "C:\transcripts\transcript0.txt" -NoClobber
    ```
    </details>

7. How can you get the 100 most recent records from the SECURITY event log on a system?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-EventLog -LogName SECURITY -Newest 100
    ```
    </details>

8. Is there a way to obtain the list of services that are running on a remote computer?

    <details>
    <summary>Click me for proposed solution</summary>
    Answer: Yes, you can use the Get-Service cmdlet with the -ComputerName parameter followed by the name of the remote computer to obtain the list of services running on that computer. Example: 
    ``` pwsh
    Get-Service -ComputerName RemoteComputerName
    ```
    </details>

9. Is there a way to obtain the list of services that are running on a remote computer?

    <details>
    <summary>Click me for proposed solution</summary>
    Answer: Yes, you can use the Get-Process cmdlet with the -ComputerName parameter followed by the name of the remote computer to obtain the list of processes running on that computer. Example: 
    ``` pwsh
    Get-Process -ComputerName RemoteComputerName
    ```
    </details>

10. Review the help of the Out-File cmdlet. What is the line size used by this cmdlet by default? Is there a parameter that allows you to change this size?

    <details>
    <summary>Click me for proposed solution</summary>
    Answer: The line size used by default by the Out-File cmdlet is 80 characters. You can change this size using the -Width parameter followed by the desired width. Example: 
    ``` pwsh
    Get-Process | Out-File -FilePath C:\Processes.txt -Width 120
    ```
    </details>

11. By default, Out-File overwrites the output file if it already exists. Is there a parameter that prevents the overwriting of an existing file?

    <details>
    <summary>Click me for proposed solution</summary>
    Answer: Yes, the -NoClobber parameter can be used to prevent the overwriting of an existing file. If this parameter is used and the output file already exists, Out-File will not overwrite the file and will instead display an error message. 
    Example: 
    ``` pwsh
    Get-Process | Out-File -FilePath C:\Processes.txt -NoClobber
    ```
    </details>