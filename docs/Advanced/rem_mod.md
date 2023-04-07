## One-to-one Remoting

One-to-one remoting resembles the SSH tool that's used on many UNIX and Linux computers in that you use a command prompt on the remote computer. While the implementation of remoting is quite different from SSH, their use cases are fairly similar. In Windows PowerShell, you enter commands on your local computer, which then transmits them to the remote computer where they run. Results are serialized into XML and transmitted back to your computer, which then deserializes them into objects and puts them into the Windows PowerShell pipeline. Unlike SSH, one-to-one remoting isn't built on the Telnet protocol.

To start one-to-one Windows PowerShell remoting, run the Enter-PSSession command, combined with its –ComputerName parameter. You can use other parameters to perform basic connection customization, which we'll cover in later topics.

After you're connected, the Windows PowerShell prompt changes to indicate the computer to which you're connected. To exit the session and return to the local command prompt, run Exit-PSSession. If you close Windows PowerShell while connected, the connection will close on its own.

## One-to-many Remoting

One-to-many remoting lets you send a single command to multiple computers in parallel. Each computer will run the command that you transmit, serialize the results into XML, and transmit those results back to your computer. Your computer then deserializes the XML into objects and puts them into the Windows PowerShell pipeline. When doing this, several properties are added to each object, including a PSComputerName property that indicates which computer each result came from. That property lets you sort, group, and filter based on computer name.

You can use one-to-many remoting using two different techniques:

+ **Invoke-Command –ComputerName name1,name2 –ScriptBlock { command }.** This technique sends the command (or commands) contained in the script block to the computers that you list. This technique is useful for sending one or two commands; multiple commands are separated by a semicolon.
+ **Invoke-Command –ComputerName name1,name2 –FilePath filepath.** This technique sends the contents of a script file with a .ps1 file name extension to the computers that you list. The local computer opens the file and reads its contents. However, the remote computers don't have to have direct access to the file. This technique is useful for sending a large file of commands, such as a complete script.

Within any script block (including the script block provided to the –ScriptBlock parameter) you can use a semicolon to separate multiple commands. For example, { Get-Service ; Get-Process } will run Get-Service, and then run Get-Process.

## Throttling
To help you manage the resources on your local computer, PowerShell includes a per-command throttling feature that lets you limit the number of concurrent remote connections established for each command. By default, Windows PowerShell will connect to only 32 computers at once. If you list more than 32 computers, the connections to the other computers will be queued. Once sessions to some of the computers from the first batch complete and return their results, connections to the computers in the next batch will be initiated.

You can alter this behavior by using the –ThrottleLimit parameter of Invoke-Command. Raising the number doesn't put an extra load on the remote computers. However, it does put an extra load on the computer where Invoke-Command was invoked. It also utilizes more bandwidth. Each concurrent connection is basically a thread of Windows PowerShell. Therefore, raising the number of computers consumes memory and processor speed on the local computer.

## Passing values
The script block or file contents are transmitted as literal text to the remote computers that run them exactly as is. The computer doesn't parse the script block or file on which the Invoke-Command was run. Consider the following command example:

``` pwsh
$var = 'BITS'
Invoke-Command –ScriptBlock { Get-Service –Name $var } –Computer LON-DC1
```

In this scenario, the variable $var is being set on the local computer rather than being included into the script block to be run on LON-DC1. In other words, $var is not defined or set in the PowerShell remoting session to LON-DC1, which is a common mistake that administrators new to Windows PowerShell often make.

Invoke-Command cannot include variables in its script block or script file unless the remote computer can understand those variables. As such, it might seem more complicated to find a way to pass data from the initiating computer to the remote computer. However, Invoke-Command actually provides a specific mechanism for doing this task.

To review, the intent behind the following command is to display a list of the 10 most recent Security event log entries on each targeted computer. However, the command will not work as written:

``` pwsh
$Log = 'Security'
$Quantity = 10
Invoke-Command –Computer ONE,TWO –ScriptBlock {
  Get-EventLog –LogName $Log –Newest $Quantity
}
```

The problem is that the variables $Log and $Quantity have meanings only on the local computer, and those values are not inserted into the script block prior to those values being sent to the remote computers. Therefore, the remote computers don't know what they mean.

The correct syntax for this command is as follows:

``` pwsh
$Log = 'Security'
$Quantity = 10
Invoke-Command –Computer ONE,TWO –ScriptBlock {
  Param($x,$y) Get-EventLog –LogName $x –Newest $y
} –ArgumentList $Log,$Quantity
```

By using this syntax, the local variables are passed to the ArgumentList parameter of Invoke-Command. Within the script block, a Param() block is created, which contains the same number of variables as the –ArgumentList list of values, which, in this case, is two. Note that you can assign any names to the variables within the Param() block. They will receive data from the ArgumentList parameter based on order. In other words, because $Log was listed first on ArgumentList, its value will be passed to $x because that's the first entry in the Param() block. The variables in the Param() block can then be used inside the script block, as depicted in the example.

This syntax will work for Windows PowerShell 2.0 and newer. However, Windows PowerShell 3.0 introduced a simplified alternative approach. If you have a local variable $variable, and you want to include its contents in a command that will be run on a remote computer, you can run the following syntax:

``` pwsh
Invoke-Command –ScriptBlock { Do-Something $Using:variable } –ComputerName REMOTE
```

The $Using: prefix is properly processed by the local and remote computers, resulting in the $Using:variable being replaced with the contents of the local variable $variable.

## Running commands locally and remotely
Pay close attention to the commands that you enclose in the script block, which will be passed to the remote computer. Remember that your local computer won't process any script block contents, but simply pass it to the remote computer. For example, consider the following command:

``` pwsh
Invoke-Command –ScriptBlock { Do-Something –Credential (Get-Credential) } -ComputerName LON-DC1
```

This command will run the Get-Credential cmdlet on the remote computer. If you try running Get-Credential on a local computer, it will use a graphical dialog box to prompt for the credential.

Now consider this modified version of the command:

``` pwsh
Invoke-Command –ScriptBlock { Param($c) Do-Something –Credential $c }
               -ComputerName LON-DC1
               -ArgumentList (Get-Credential)
```

This command runs Get-Credential on the local computer and runs it only once. The resulting object is passed into the $c parameter of the script block, enabling each computer to use the same credential.

These examples illustrate the importance of writing remoting commands carefully. By using a combination of running commands remotely and locally, you can achieve various useful goals.

## Persistence
Using the techniques outlined here, every time you use Invoke-Command, the remote computer creates a new wsmprovhost process, and runs the command or commands. It then returns the results, and then closes that Windows PowerShell instance. Each successive Invoke-Command, even if made to the same computer, is akin to opening a new Windows PowerShell window. Any work done by a previous session won't exist unless you save it to a disk or some other persistent storage. For example, consider the following command:

``` pwsh
Invoke-Command –ComputerName LON-DC1 –ScriptBlock { $x = 'BITS' }
Invoke-Command –ComputerName LON-DC1 –ScriptBlock { Get-Service –Name $x }
```

In this example, the Get-Service would fail, because it's dependent on the value of a variable created as part of the previous wsmprovhost process. When the first script invoked by the Invoke-Command completes, its variables are cleared from memory. To address this issue, you can create a wsmprovhost process on a remote computer so that you can successfully send successive commands to it.

Windows PowerShell can create persistent connections, which are known as sessions, or more accurately, PSSessions. The PS designation signifies Windows PowerShell and differentiates these sessions from other kinds of sessions that might be present in other technologies, such as a Remote Desktop Services (RDS) session.

After making a PowerShell session to a remote computer, you run the desired commands within the session, but you leave the PowerShell session running. By doing this, you can run more commands in the session.

## Multiple computer names
The –ComputerName parameter of Invoke-Command can accept any collection of string objects as computer names. The following list describes different techniques that can be used to create such collections:

-ComputerName ONE,TWO,THREE. A static, comma-separated list of computer names.
-ComputerName (Get-Content Names.txt). Reads names from a text file named Names.txt, assuming the file contains one computer name per line.
-ComputerName (Import-Csv Computers.csv | Select –ExpandProperty Computer). Reads a comma-separated value (CSV) file that's named Computers.csv and contains a column named Computer that contains computer names.
-ComputerName (Get-ADComputer –Filter * | Select –ExpandProperty Name). Queries every computer object in AD DS, which can take a significant amount of time in a large domain.

### Common mistakes when using computer names
Be careful where you specify a computer name. For example, review the following command:

``` pwsh
Invoke-Command –ScriptBlock { Get-Service –ComputerName ONE,TWO }
```

This command doesn't provide a –ComputerName parameter to Invoke-Command. Therefore, the command runs on the local computer. The local computer will run Get-Service targeting computers named ONE and TWO. The protocols used by Get-Service will be used instead of Windows PowerShell remoting. Compare this with the following command:

``` pwsh
Invoke-Command –ScriptBlock { Get-Service } –ComputerName ONE,TWO
```

This command will use Windows PowerShell remoting to connect to computers named ONE and TWO. Each of these computers will run Get-Service locally, returning their results using remoting.

For more interactive Windows PowerShell remoting situations, you can manage individual sessions as separate entities. To do this, you first create a session by using the New-PSSession command. The benefit of using the New-PSSession command is that the session will persist throughout multiple Invoke-Command instances, allowing you to pass variables and objects to other commands in your script. You can create persistent sessions by using the New-PSSession command and assigning it to a variable. You then can reference the variable later by using the Invoke-Command command. When finished, you can close persistent sessions by using the Remove-PSSession command.

## Compare Remoting Output with Local Output

When you run a command such as Get-Process on your local computer, the command returns object or objects of the type System.Diagnostics.Process and adds them to the Windows PowerShell pipeline. These objects have properties, methods, and frequently, events. Methods provide the ability to perform task. For example, the Kill() method of a Process object terminates the process that this object represents.

The process of converting an object into a form that can be readily transported is known as serialization. Serialization takes an object's state and transforms it into serial data format, such as XML or binary format. Deserialization converts the formatted XML or binary data into an object type.

When a command runs on a remote computer, that computer serializes the results in XML, and transmits that XML text to your computer. You do this to put the object’s information into a format that can be transmitted over a network. However, for complex objects the serialization process can use only static information about an object—in other words, its properties.

When your computer receives the XML, it's deserialized back into objects that are put in the Windows PowerShell pipeline. When you have a Process object, by piping it to Get-Member, you know it's now of the type Deserialized.System.Diagnostics.Process, a related, but different, kind of object. The deserialized object has no methods and no events.

Given the serialization and deserialization which is part of PowerShell remoting, you should consider any objects that are obtained in this manner to be a static snapshot. The values of object properties are not updatable, and the objects cannot be used to perform any actions. Therefore, any tasks that require interacting with remote objects should be performed on the remote computer as part of the PowerShell remoting session.

For example, here is an example of a command that will not yield the desired results:

``` pwsh
Invoke-Command –Computer LON-DC1 –ScriptBlock { Get-Process –Name Note* } |
Stop-Process
```

In this example, you're retrieving Process objects, but the task of stopping processes takes place on the local computer rather than the remote one. This will result in stopping any local processes that happen to have the names matching the remote ones.

The proper way to accomplish the intended outcome would be to run:

``` pwsh
Invoke-Command –Computer LON-DC1 –ScriptBlock { Get-Process –Name Note* |
Stop-Process }
```
In this case, the processing has occurred entirely on the remote computer, with only the final results being serialized and sent back. The difference between these two commands is subtle but important to understand.

