## Array

An array is a data structure that's designed to store a collection of items of the same type. You can think of an array as a variable containing multiple values or objects. Variables that contain a single value are useful, but for complex tasks you often need to work with groups of items. For example, you might need to update the Voice over IP (VoIP) attribute on multiple domain user accounts. Or you might need to check the status of a group of services and restart all of them that are stopped. When you put multiple objects or values into a variable, it becomes an array.

### Creating Arrays

You can create an array by providing multiple values in a comma-separated list. For example:

``` pwsh
$computers = "LON-DC1","LON-SRV1","LON-SRV2"
$numbers = 228,43,102
```

To create an array of strings, you put quotes around each item. If you put one set of quotes around all the items, it's treated as a single string.

You also can create an array by using the output from a command. For example:

``` pwsh
$users = Get-ADUser -Filter *
$files = Get-ChildItem C:\
```

ou can verify whether a variable is an array by using the GetType() method on the variable. The BaseType listed will be System.Array.

You can create an empty array before you're ready to put content in it. This can be useful when you have a loop later on in a script that adds items to the array. For example:

``` pwsh
$newUsers = @()
```

You also can force an array to be created when adding a single value to a variable. This creates an array with a single value into which you can add items later. For example:

``` pwsh
[array]$computers="LON-DC1"
```

### Working With Arrays

#### Display Item

``` pwsh
$users[0]
```

#### Add Item

``` pwsh
$users = $users + $user1
# or
$users += $user1
```
#### To Identify

To identify what you can do with the content in an array, use the Get-Member cmdlet. Pipe the contents of the array to Get-Member, and the results returned identify the properties and methods that you can use for the items in the array. For example:

``` pwsh
$files | Get-Member
```

When you pipe an array containing mixed data types to Get-Member, results are returned for each data type. This is also a helpful way of determining which data types are in the array.

To review the properties and methods available for an array rather than the items within the array, use the following syntax:

``` pwsh
Get-Member -InputObject $files
```

## ArrayLists

The default type of array that Windows PowerShell creates is a fixed-size array. This means that when you add an item to the array, the array is actually recreated with the additional item. When you work with relatively small arrays, this is not a concern. However, if you add thousands of items to an array one by one, recreating an array each time has a negative performance impact. The other concern when using fixed-size arrays is removing items. There's no simple method to remove an item from a fixed-size array.

To address the shortcomings of arrays, you can use an array list. An array list functions similar to an array, except that it doesn't have a fixed size. This means that you can use methods to add and remove items.

### Creating ArrayLists

``` pwsh
[System.Collections.ArrayList]$computers = "LON-DC1","LON-SVR1","LON-CL1"
```

To create an array list that's empty and ready to add items, use the following syntax:

``` pwsh
$computers=New-Object System.Collections.ArrayList
```

### ArrayList Methods

``` pwsh
$computers.Add("LON-SRV2")
$computers.Remove("LON-CL1")
```

When you remove an item from an array list, if there are multiple matching items then only the first instance is removed.

If you want to remove an item from an array list based on the index number, you use the RemoveAt() method. For example:

``` pwsh
$computers.RemoveAt(1)
```

## Hash Tables

A hash table represents a similar concept to an array since it stores multiple items. However, unlike an array which uses an index number to identify each item, a hash table uses for this purpose a unique key. The key is a string that's a unique identifier for the item. Each key in a hash table is associated with a value.

You can use a hash table to store both IP addresses and the computer names as the following table depicts.

|Key|	Value|
| --- | --- |
|LON-DC1|	192.168.0.10|
|LON-SRV1|	192.168.0.11|
|LON-SRV2|	192.168.0.12|

If the hash table is named $servers, then you access the first item in the hash table by using either of the following options:

``` pwsh
$servers.'LON-DC1'
$servers['LON-DC1']
```

You only need to use single quote marks to enclose keys that contain special characters. In the previous example, the hyphen in the computer names is a special character, which requires the key name to be enclosed in single quote marks.

### Working with Hash Tables

Working with hash tables is similar to working with an array, except that to add items to a hash table you need to provide both the key for the item and the value.

``` pwsh
$servers = @{"LON-DC1" = "172.16.0.10"; "LON-SRV1" = "172.16.0.11"}
```

Notice the following syntax in the previous example:

+ It begins with the at (@) symbol.
+ The keys and associated values are enclosed in braces.
+ The items are separated by a semicolon.

The semicolon between hash table items is required in the previous example because they're all on the same line. If you place each item on a separate line, it's not necessary to use semicolons as separators.


``` pwsh
$servers.Add("LON-SRV2","172.16.0.12")
$servers.Remove("LON-DC1")
# Update the value for a key
$servers."LON-SRV2"="172.16.0.100"
```