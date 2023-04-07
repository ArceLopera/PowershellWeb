## Disconnected sessions
In Windows PowerShell 3.0 and newer, you can also manually disconnect from sessions. This allows you to close the session in which a PowerShell session was established, even shut down the local computer, without disrupting commands running in the PowerShell session on the remote computer. This is particularly useful for running commands that take a long time to complete and provides the time and device flexibility that IT professionals need.

## Controlling sessions
Every computer has a drive named WSMan that includes many configuration parameters related to the session, such as:

+ Maximum session run time
+ Maximum idle time
+ Maximum number of incoming connections
+ Maximum number of sessions per administrator

You can explore these configuration parameters by running dir WSMan:\localhost\shell, and change them in that same location. You also can control many of the settings through Group Policy.

## Create and manage persistent PSSessions

You use the New-PSSession command to create a persistent connection. The command contains many of the same parameters as Invoke-Command, including -Credential, –Port, and –UseSSL. This is because you're creating a connection that is identical to the one Invoke-Command creates. However, instead of closing this connection immediately, you're leaving it running.

PowerShell sessions do have an idle timeout, after which the remote computer will close them automatically. A closed PowerShell session differs from a disconnected PowerShell session, because closed PowerShell sessions can't be reconnected. In this case, you can only remove the PowerShell session, and then recreate it.

New-PSSession can accept multiple computer names, which causes it to create multiple PowerShell session objects. When you run the New-PSSession command, it outputs objects representing the newly created PowerShell sessions. You can assign these PowerShell sessions to a variable to make them easier to refer to, and to use in the future.

You can use a PowerShell session as soon as you create it. Both the Invoke-Command and Enter-PSSession commands can accept a PowerShell session object instead of a computer name. Invoke-Command can accept multiple PowerShell session objects. You use the commands’ –Session parameter for this purpose. When you use this parameter, the commands use the existing PowerShell session instead of creating a new connection. When your command finishes running or you exit the PowerShell session, the PowerShell session remains running and connected, and ready for future use.

For example, you can use the following commands to enter a PowerShell session on LON-CL1 and then close it:

``` pwsh
$client = New-PSSession –ComputerName LON-CL1
Enter-PSSession –Session $client
Exit-PSSession
```

Alternatively, you could use the following commands to achieve the same results:

``` pwsh
$computers = New-PSSession –ComputerName LON-CL1,LON-DC1
Invoke-Command –Session $computers –ScriptBlock { Get-Process }
```

For example, the following command can use the $dc variable to open a PowerShell session to LON-DC1 within a script or code block:

``` pwsh
$dc = New-PSSession –ComputerName LON-DC1
```

The following command creates remote sessions on Server01 and Server02, and the session objects are stored in the $s variable:

``` pwsh
$s = New-PSSession -ComputerName Server01, Server02
```

Now that the sessions are established, you can run any command in them. And because the sessions are persistent, you can collect data from one command and use it in another command.

For example, the following command runs a Get-HotFix command in the sessions in the $s variable, and it saves the results in the $h variable:

``` pwsh
Invoke-Command -Session $s {$h = Get-HotFix}
```

The $h variable is created in each of the sessions in $s, but it doesn't exist in the local session. Now you can use the data in the $h variable with other commands in the same session, and the results are displayed on the local computer. For example:

``` pwsh
Invoke-Command -Session $s {$h | where {$_.InstalledBy -ne "NTAUTHORITY\SYSTEM"}}
```

## Disconnect PSSessions

You can disconnect from PSSessions when both the initiating computer and the remote computer are running Windows PowerShell 3.0 and later. Disconnecting is typically a manual process. In some scenarios, Windows PowerShell can automatically place a connection into the Disconnected state if the connection is interrupted. However, if you manually close the Windows PowerShell host application it won't disconnect from the sessions, it will just close them.

Using disconnected sessions is similar to the following process:

1. Use New-PSSession to create the new PSSession. Optionally, use the PSSession to run commands.
2. Run Disconnect-PSSession to disconnect from the PSSession. Pass the PSSession object that you want to disconnect from to the command’s –Session parameter.
3. Optionally, move to another computer and open Windows PowerShell.
4. Run Get-PSSession with the –ComputerName parameter to obtain a list of your PSSessions running on the specified computer.
5. Use Connect-PSSession to reconnect to the desired PSSession.

You cannot review or reconnect to another user’s PSSessions on a computer.

## Implicit Remoting

One of the ongoing problems in the Windows management space is version mismatch. For example, Windows servers 2019 and 2022 include many new Windows PowerShell commands. You can make these commands available on Windows 10 or Windows 11 as part of the Remote Server Administration Tools (RSAT). However, you might not be able to use the same approach with older versions of Windows.

You must be familiar with another ongoing problem if you recently had to rebuild a workstation. The problem is the sheer amount of time it can take to track down and install administrative tools and Microsoft Management Consoles (MMCs) on a computer. Assuming all of them are compatible with your Windows versions, installation alone can take days.

These problems lead administrators to forgo installing tools on workstations, and instead access tools directly on the server through Microsoft Remote Desktop. However, this isn't a good solution because it puts the server in the position of having to be a client, while simultaneously providing services to hundreds or thousands of users. The advent of Server Core, which lacks a graphical user interface (GUI), was in part to make servers perform better and need fewer updates. However, this also means that they can't run GUI tools and MMCs.

### Implicit remoting brings tools to you
Implicit remoting brings a copy of a server’s Windows PowerShell tools to your local computer. In reality, you're not copying the commands at all; you're creating a kind of shortcut, called a proxy function, to the server’s commands. When you run the commands on your local computer, they implicitly run on the server through remoting. Results are then sent back to you. It's exactly as if you ran everything through Invoke-Command, but it's much more convenient. Commands also run quicker, because commands on the server are co-located with the server’s functionality and data.

### Using implicit remoting
While implicit remoting became available in Windows PowerShell 2.0, it became much easier to use starting with Windows PowerShell 3.0. All that's required is to create a session to the server containing the module that you want to use. Then, using Import-Module and its –PSSession parameter, you import the desired module. The commands in that module, and even its Help files become available in your local Windows PowerShell session.

With implicit remoting, you have the option of adding a prefix to the noun of commands that you import. Doing this can make it easier, for example, to have multiple versions of the same commands loaded simultaneously without causing a naming collision. For example, if you import both Microsoft Exchange Server 2016 and Exchange Server 2019 commands, you might add the 2016 and 2019 prefix to each of them, respectively. This enables you to run both sets of commands. In reality, each would be running on their respective servers, enabling you to run both sets (perhaps in a migration scenario) side-by-side.

The Help option also works for commands that are running through implicit remoting. However, the Help files are drawn through the same remoting session as the commands themselves. Therefore, the remote computer must have an updated copy of its Help files. This can be a concern on servers because they might not be used frequently and might not have had Update-Help run on them recently to pull down the latest Help files.