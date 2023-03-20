1. Which class can be used to query the IP address of a network adapter? Does that class have a method to release a DHCP address lease?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    Get-WmiObject -List | where name -like '*networkadapter*

    ## Method to release a DHCP address
    
    gwmi Win32_NetworkAdapter | gm | ? {$_.membertype -like "method"}

    gwmi Win32_NetworkAdapterConfiguration | gm | ? {$_.membertype -like "method"}

    ```
    </details>

2. Display a list of patches using WMI (Microsoft refers to patches as quick-fix engineering). Is the listing different from the one produced by the ``Get-Hotfix`` cmdlet?

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    
    gwmi Win32_QuickFixEngineering

    ## No difference with get-hotfix cmdlet
    diff -ReferenceObject (gwmi Win32_QuickFixEngineering) -DifferenceObject (Get-Hotfix)


    ```
    </details>

3. Using WMI, display a list of services, including their current status, their startup mode, and the accounts they use to login.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    gwmi win32_service | fl status, startmode, startname 
    ```
    </details>

4. Using CIM cmdlets, list all classes in the ``SecurityCenter2`` namespace, which have product as part of the name.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    get-cimclass -names root\SecurityCenter2 | ? cimclassname -like '*product*'
    ```
    </details>

5. Using CIM cmdlets, and the results of the previous exercise, display the names of the antispyware applications installed on the system. You can also check if there are antivirus products installed on the system.

    <details>
    <summary>Click me for proposed solution</summary>
    ``` pwsh
    ## Antivirus
    get-ciminstance -names root\SecurityCenter2 -class AntiVirusProduct
    ## Antispyware
    get-ciminstance -names root\SecurityCenter2 -class AntiSpywareProduct
    ```
    </details>