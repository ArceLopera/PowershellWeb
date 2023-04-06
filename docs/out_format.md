Here is a detailed table of the most common cmdlets available for reading and writing files in different formats in PowerShell:

|File Format|	Cmdlet for Reading|	Cmdlet for Writing|
|:--|:--|:--|
|[Text Files (.txt)](#text)|	[Get-Content](#reading-a-text-file)|	[Set-Content](#writing-to-a-text-file)|
|[CSV Files (.csv)](#csv)|	[Import-Csv](#import-csv)|	[Export-Csv](#export-csv)|
|[JSON Files (.json)](#json)|	[ConvertFrom-Json](#reading-a-json-file)|	[ConvertTo-Json](#writing-to-a-json-file)|
|[XML Files (.xml)](#xml)|	[Select-Xml](#reading-an-xml-file)|	[Export-Clixml](#writing-to-an-xml-file)|
|[HTML Files](#html)|[Get-Content](#reading-a-text-file)|[ConvertTo-Html](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/convertto-html?view=powershell-7.3)|

Here are some examples of how to use these cmdlets:
## Text

### Get-Content

The Get-Content cmdlet gets the content of the item at the location specified by the path, such as the text in a file or the content of a function. For files, the content is read one line at a time and returns a collection of objects, each of which represents a line of content.

Beginning in PowerShell 3.0, Get-Content can also get a specified number of lines from the beginning or end of an item.

``` pwsh
$computers = Get-Content C:\Scripts\computers.txt
```

You can use wildcards in the path for Get-Content to obtain data from multiple files at a time. When you use wildcards for the path, you can modify the files selected by using the -Include and -Exclude parameters. When you use -Include, only the specified patterns are included. When you use -Exclude, all files are included except the patterns specified. Using wildcards can be useful when you want to scan all text files for specific content such as an error in log files.

``` pwsh
Get-Content -Path "C:\Scripts\*" -Include "*.txt","*.log"
```

You can limit the amount of data that you retrieve with Get-Content by using the -TotalCount and -Tail parameters. The -TotalCount parameter specifies how many lines should be retrieved from the beginning of a file. The -Tail parameter specifies how many lines to retrieve from the end of a file.

``` pwsh
Get-Content C:\Scripts\computers.txt -TotalCount 10
```

### Set-Content

Set-Content is a string-processing cmdlet that writes new content or replaces the content in a file. Set-Content replaces the existing content and differs from the Add-Content cmdlet that appends content to a file. To send content to Set-Content you can use the Value parameter on the command line or send content through the pipeline.

If you need to create files or directories for the following examples, see New-Item.

``` pwsh
Set-Content C:\example.txt "This is some text."
```

This command will write the text "This is some text" to the file file.txt located in the C:\ directory. If the file already exists, its content will be overwritten. If the file does not exist, it will be created.

### Add-Content

The Add-Content cmdlet appends content to a specified item or file. You can specify the content by typing the content in the command or by specifying an object that contains the content.

If you need to create files or directories for the following examples, see New-Item.

``` pwsh
Add-Content -Path .\*.txt -Exclude help* -Value 'End of file'
```

The Path parameter specifies all .txt files in the current directory, but the Exclude parameter ignores file names that match the specified pattern. The Value parameter specifies the text string that is written to the files.

Use Get-Content to display the contents of these files.

### Clear-Content

The Clear-Content cmdlet deletes the contents of an item, such as deleting the text from a file, but it does not delete the item. As a result, the item exists, but it is empty. Clear-Content is similar to Clear-Item, but it works on items with contents, instead of items with values.

``` pwsh
Clear-Content "..\SmpUsers\*\init.txt"
```

This command deletes all of the content from the init.txt files in all subdirectories of the SmpUsers directory. The files are not deleted, but they are empty.


## CSV

A CSV (Comma Separated Values) file is a plain text file format that stores tabular data in a simple, structured way, where each line represents a row, and the data within each row is separated by commas. The first line of the file usually contains the headers, which specify the column names.

When working with CSV files in PowerShell, it's important to keep in mind that the file format is very simple and flexible, but can also be prone to errors if the data is not well-formed. It's important to validate and clean the data before working with it, and to always specify the correct headers and data types when importing or exporting CSV files.

### I/O
``` pwsh
Import-Csv C:\example.csv
```

#### Import-CSV 

This cmdlet reads a CSV file and creates custom objects for each row, using the headers as the property names. You can then work with these objects as you would with any other PowerShell object. Here's an example:

``` pwsh
$csv = Import-CSV C:\data\users.csv
foreach ($user in $csv) {
    Write-Output $user.Name
}
```
This code imports a CSV file containing user data, and then outputs the Name property of each user object.


#### Export-CSV 


``` pwsh
$data = @{"Name"="John"; "Age"=30}
$data | Export-Csv C:\example.csv -NoTypeInformation
```

This cmdlet writes a collection of objects to a CSV file, using the object properties as the column names. Here's an example:

``` pwsh
$users = Get-ADUser -Filter *
$users | Select-Object Name, EmailAddress | Export-CSV C:\data\users.csv -NoTypeInformation
```

This code retrieves all users from Active Directory, selects the Name and EmailAddress properties, and then exports them to a CSV file.

### Conversion To/From

#### ConvertTo-CSV
This cmdlet converts a collection of objects to a CSV format, but does not write it to a file. You can then use other cmdlets (like Out-File or Set-Content) to write the CSV to a file. Here's an example:

``` pwsh
$processes = Get-Process
$processes | Select-Object Name, ID, CPU | ConvertTo-CSV | Out-File C:\data\processes.csv
```

This code retrieves information about running processes, selects the Name, ID, and CPU properties, converts it to CSV format, and then writes it to a file.

Here's another example of how to use ConvertTo-CSV:

``` pwsh
# Create an array of objects
$people = @(
    [PSCustomObject]@{
        Name = "John"
        Age = 35
        Occupation = "Software Engineer"
    },
    [PSCustomObject]@{
        Name = "Jane"
        Age = 28
        Occupation = "Project Manager"
    }
)

# Convert the objects to CSV format
$csv = $people | ConvertTo-CSV

# Output the CSV data
$csv

```

In this example, we first create an array of custom objects representing people with different attributes like name, age, and occupation. We then pipe this array to the ConvertTo-CSV cmdlet to convert it to CSV format and store the output in a variable called $csv. Finally, we output the CSV data using the $csv variable.

#### ConvertFrom-CSV

ConvertFrom-CSV is a cmdlet that allows you to convert CSV data back into PowerShell objects. This cmdlet takes the input CSV data, converts it into PowerShell objects, and outputs the objects.

Here's an example of how to use ConvertFrom-CSV:

``` pwsh
# Convert CSV data to PowerShell objects
$csvData = @"
Name,Age,Occupation
John,35,Software Engineer
Jane,28,Project Manager
"@ | ConvertFrom-CSV

# Output the objects
$csvData
```

In this example, we first define a CSV string containing data about people, with each person represented as a row with columns for name, age, and occupation. We then pipe this CSV string to the ConvertFrom-CSV cmdlet to convert it into PowerShell objects and store the output in a variable called $csvData. Finally, we output the objects using the $csvData variable.

## JSON

JavaScript Object Notation (JSON) is a lightweight data format that's similar to XML, because it can represent multiple layers of data. JSON is a lightweight data-interchange format compared to XML because of its simpler syntax.

Windows PowerShell doesn't include cmdlets that import or export JSON data directly from a file. Instead, if you have JSON data stored in a file, you can retrieve the data by using Get-Content and then convert the data by using the ConvertFrom-Json cmdlet.
### Reading a JSON file
``` pwsh
$json = Get-Content .\example.json | ConvertFrom-Json
```

### Writing to a JSON file
``` pwsh
$data = @{"Name"="John"; "Age"=30}
$data | ConvertTo-Json | Out-File .\example.json
```
### Testing a JSON file
``` pwsh
"{'name': 'Ashley', 'age': 25}" | Test-Json
```
## XML

XML is a more complex data storage format than CSV files. The main advantage of using XML for Windows PowerShell is that it can hold multiple levels of data. A CSV file works with a table of information in which the columns are the object properties. In a CSV file, it's difficult to work with multivalued attributes, whereas XML can easily represent multivalued attributes or even objects that have other objects as a property.
### Reading an XML file

The use of Import-Clixml to retrieve data from an XML file creates an array of objects. Because XML can be complex, you might not easily be able to understand the object properties by reviewing the contents of the XML file directly. You can use Get-Member to identify the properties of the data that you import.

``` pwsh
$users = Import-Clixml C:\Scripts\Users.xml
```

You can limit the data retrieved by Import-Clixml by using the -First and -Skip parameters. The -First parameter specifies to retrieve only the specified number of objects from the beginning of the XML file. The -Skip parameter specifies to ignore the specified number of objects from the beginning of the XML file and to retrieve all the remaining objects.

``` pwsh
$xml = Select-Xml -Path C:\example.xml -XPath "//book"
```


### Writing to an XML file
``` pwsh
$data = @{"Name"="John"; "Age"=30}
$data | Export-Clixml C:\example.xml
```

## HTML

PowerShell provides a variety of cmdlets that can be used to manipulate HTML files, such as Invoke-WebRequest, Invoke-RestMethod, and ConvertTo-Html.

Here are some examples of how to use these cmdlets to manipulate HTML files in PowerShell:

1. Downloading an HTML file using Invoke-WebRequest:

    ``` pwsh
    Invoke-WebRequest -Uri https://example.com -OutFile example.html
    ```

2. Extracting data from an HTML file using Invoke-RestMethod:

    ``` pwsh
    $response = Invoke-RestMethod -Uri https://example.com
    $response.Tables[0] | ConvertTo-Html -Fragment
    ```

3. Converting a PowerShell object to an HTML table using ConvertTo-Html:

    ``` pwsh
    Get-Process | Select-Object Name, CPU, WorkingSet | ConvertTo-Html -Head "Process Report" -PreContent "<h1>Current Processes</h1>"
    
    ```


These are just a few examples of how to manipulate HTML files in PowerShell. The possibilities are endless, and these cmdlets can be combined with other PowerShell commands to perform more complex tasks.

ConvertTo-Html creates a simple list or table that's coded as HTML. You can control the HTML format in a limited way through a variety of parameters, such as:

+ ‑Head. Specifies the content of an HTML head section.
+ ‑Title. Sets the value of the HTML title tag.
+ ‑PreContent. Defines any content that should display before the table or list output.
+ ‑PostContent. Defines any content that should display after the table or list output.

## OUTPUT TO FILE OR PRINTER
If you want to save the output of a command to an unformatted plain text file, you can use either of these methods (using the get-process command as an example):

+ get-process > processes.txt (using redirection)
+ get-process | out-file processes.txt (using the pipeline)

Both versions are functionally equivalent, but the out-file command can receive parameters to change the line width and to avoid overwriting an existing file.
To read a plain text file as text strings, you use the Get-Content cmdlet (with aliases cat or type):

``` pwsh
Get-Content processes.txt
```

PowerShell also supports the text redirection operators (> and >>) that cmd.exe uses. These operators act as an alias for Out-File. The greater than sign (>) at the end of a pipeline directs output to a file, overwriting the content. Two consecutive greater than signs (>>) direct output to a file, appending the output to any text already in the file.

Out-File is the easiest way to move data from PowerShell to external storage. However, the text files that Out-File creates are usually intended for reviewing by a person. Therefore, reading the data back into Windows PowerShell in a way that enables data manipulation, sorting, selection, and measurement is frequently difficult or impractical.

Out-File doesn't produce any output of its own, which means that the command doesn't put objects into the pipeline. After you run the command, you should expect no output on the screen.

If you want to send the output of a command to the printer, you use the out-printer cmdlet:

``` pwsh
get-process | out-printer
```




