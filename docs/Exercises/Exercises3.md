1. Show a table that includes only the names of the processes, their IDs, and whether they are responding to Windows (the Responding property shows that). Make the table take up a minimum of horizontal space, but don't allow the information to be truncated.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ps | ft -Property name, id, responding -Wrap
    ```
    </details>

2. Display a table that includes the names of the processes and their IDs. Also include columns for physical and virtual memory usage; Express those values ​​in megabytes (MB).

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ps | ft -Property name, id, @{n='vm [MB]';e={$_.vm/1MB -as [INT]}}, @{n='pm [MB]';e={$_.pm/1MB -as [INT]}}
    ```
    </details>

3. Use the cmdlet Get-EventLog to display a list of available event logs (check the help to find the parameter that will allow you to get that information). Format the output as a table that includes the log display name and the retention period. Column headers must be LogName and Per-Retention.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    get-eventlog -list  | ft -property @{n='NombreLog';e={$_.log}}, @{n='Per-Retencion';e={$_.MinimumRetentionDays}}
    ```
    </details>

4. Display a list of services, so that the services that are started and those that are stopped are grouped together. Those who are initiated must appear first.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    gsv | sort -property status -descending | ft -groupby status
    ```
    </details>

5. Display a four-column list of all directories that are at the root of drive C:

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ls 'C:/' | format-wide name -col 4
    ```
    </details>

6. Create a formatted list of all .exe files in the C:\Windows directory. The name, version information, and size of the file should be displayed. The size property is called length in Powershell, but for clarity, the column should be called Size in your listing.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ls 'C:\Windows/*.exe' | ft name, @{n='Size';e={$_.Length}}, versionInfo -Wrap
    ```
    </details>

7. Import the NetAdapter module (using the Import-Module NetAdapter command). Using the Get-NetAdapter cmdlet, display a list of non-virtual adapters (adapters whose Virtual property is false. The boolean false is represented by Powershell as $False).

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-NetAdapter | ? {$_.Virtual -eq $False} | fl
    ```
    </details>

8. Import the DnsClient module. Using the Get-DnsClientCache cmdlet, list the A and AAAA records that are in the cache. Tip: If the cache is empty, visit some websites to populate it.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-DnsClientCache -type 'A', 'AAAA' | fl
    ```
    </details>

9. Display a list of all .exe files in the C:\Windows\System32 directory that are larger than 5 MB.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ls 'C:\Windows\System32/*.exe' | ? {$_.length -gt 5MB} | fl
    ```
    </details>

10. Display a list of patches that are security updates.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-HotFix | ? {$_.description -eq "Security Update"} | fl
    ```
    </details>

11. Display a list of patches that have been installed by the Administrator user, which are updates. If you don't have any, look for user-installed patches System.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-HotFix | ? {$_.Description -like "*Update*" -and $_.InstalledBy -like "*System*"} | fl
    ```
    </details>

12. Generate a list of all running processes with the name Conhost or Svchost.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ps | ? {$_.Name -eq 'conhost' -or $_.Name -eq 'svchost'} | fl
    ```
    </details>