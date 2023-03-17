1. Create two similar text files (with one or two different lines). Compare them using diff.
    <details>
        <summary>Click me for proposed solution</summary>
    ``` pwsh
    ##Create 1st file
    New-Item -Path . -Name "file1.txt" -ItemType "file" -Value "Hello World.\nBonjour Monde!" -Force
    ##Create 2nd file
    New-Item -Path . -Name "file2.txt" -ItemType "file" -Value "Hello World!\nBonjour Monde." -Force
    ##Find the difference between two files
    diff -ReferenceObject(cat file1.txt) -DifferenceObject(cat file2.txt)
    ```

    ```
    InputObject    SideIndicator
    -----------    -------------
    Hello World!   =>           
    Bonjour Monde. =>           
    Hello World.   <=           
    Bonjour Monde! <=           

    ```
    </details>

2. What happens if you run:
``` pwsh
get-service | export-csv servicios.csv | out-file
```
Why?
    <details>
            <summary>Click me for proposed solution</summary>
    The output of the above command is:
    ```
    out-file: Cannot process argument because the value of the "path" argument is NULL. Change the value of the "path" argument to a non-null value.
    At line:1 char:42
    + get-service | export-csv servicios.csv | out-file
    +                                          ~~~~~~~~
        + CategoryInfo          : InvalidArgument: (:) [Out-File], PSArgumentNullException
        + FullyQualifiedErrorId : ArgumentNull,Microsoft.PowerShell.Commands.OutFileCommand
    ```
    This is because export-csv servicios.csv creates the csv file and has null output, therefore out-file is not necessary.

    </details>

3. How would you create a file delimited by semicolons (;)? 
    HINT: Use export-csv, but with an additional parameter.

    <details>
        <summary>Click me for proposed solution</summary>
        ``` pwsh
        ps | epcsv -delimiter ";" example.csv
        ##Same as
        Get-Process | Export-Csv -delimiter ";" example.csv
        ```
    </details>

4. Export-cliXML and Export-CSV modify the system, because they can create and overwrite files. Is there a parameter that prevents overwriting an existing file? Is there a parameter that allows the command to ask before overwriting a file?

    <details>
        <summary>Click me for proposed solution</summary>
    To prevent overwriting the file if it already exists, the -NoClobber attribute can be used.

    **Example:**
    The following command will not overwrite the result from point 3
    ``` pwsh
    ps | epcsv example.csv -NoClobber
    ``` 
    To ask for confirmation, use the -Confirm parameter.
    ``` pwsh
    ps | epcsv -delimiter ";" example.csv -Confirm
    ```
    </details>

5. Windows uses regional settings, which includes the list separator. In English Windows, the list separator is a comma (,). How do you tell Export-CSV to use the system separator instead of the comma?

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet get-culture obtains system configuration information, such as language and writing information.

    ``` pwsh
    ps | epcsv test2.csv -Delimiter ((get-culture).textInfo.listSeparator)
    ```

    Using cat (alias of Get-Content), we can see that the output uses ';' as the Spanish list separator.

    ``` pwsh
    cat .\test2.csv
    ```
    </details>

6. Identify a cmdlet that allows generating a random number.
    
    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet get-random returns a pseudo-random 32-bit integer.
    </details>

7. Identify a cmdlet that displays the current date and time.

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet get-date returns an object that represents the current date and time, which can be represented in various formats such as Windows or UNIX.
    </details>

8. What type of object does the cmdlet in question 7 produce?

    <details>
        <summary>Click me for proposed solution</summary>
    Using the cmdlet gm, we can obtain the elements that make up the object and the type. As seen below, the object is part of the System package and its type is DateTime.
    ``` pwsh
    Get-Date | gm

    TypeName: System.DateTime
    ```
    </details>

9. Using the cmdlet in question 7 and select-object, display only the day of the week as follows:
    
    ```
    DayOfWeek
    ---------
    Thursday
    ```

    <details>
        <summary>Click me for proposed solution</summary>
        ```pwsh
        get-date | select -property "dayofweek"

        DayOfWeek
        ---------
        Friday
        ```

    </details>

10. Identify a cmdlet that displays information about patches (hotfixes) installed on the system.

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet that returns a list of system patches is:
    ``` pwsh
    get-hotfix
    ```
    </details>

11. Using the cmdlet from question 10, display a list of installed patches. Then extend the expression to sort the list by installation date, and display on the screen only the installation date, the user who installed the patch, and the patch ID. Remember to examine the property names.

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet that returns a list of system patches is:
    ``` pwsh
    get-hotfix | sort -property installedon | select -property installedon, installedby, hotfixid
    ```
    </details>

12. Complement the solution to question 11 so that the system sorts the results by the patch description, and includes the description, patch ID, and installation date in the listing. Write the results to an HTML file.

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet that returns a list of system patches is:
    ``` pwsh
    get-hotFix | sort -property description | select -property hotfixid,description,installedon  | convertto-html | Out-File hotfix.html
    ```
    </details>

13. Display a list of the 50 newest entries from the System event log. Sort the list so that the oldest entries appear first, and entries produced at the same time should be sorted by index number. Show the index number, time, and source for each entry. Write this information to a plain text file.

    <details>
        <summary>Click me for proposed solution</summary>
    The cmdlet that returns a list of system patches is:
    ``` pwsh
    get-eventlog -newest 50 -logname system | sort -property index | sort -property timegenerated -descending | select -property index, timegenerated, source > exercise13.txt
    ```
    </details>
