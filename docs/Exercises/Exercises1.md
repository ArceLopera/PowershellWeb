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