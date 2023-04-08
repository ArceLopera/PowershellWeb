You can use Windows Management Instrumentation (WMI) and Common Information Model (CIM) cmdlets to query and manage remote computers. When you connect to a remote computer, you can specify alternative credentials for the connection, but alternative credentials are optional. WMI and CIM cmdlets have different capabilities and different syntaxes for remote connections.

## Using WMI Cmdlets
For the WMI commands, use the -ComputerName parameter to specify a remote computer’s name or IP address. You can specify multiple computer names to run the command on multiple computers in a single statement. You can provide the computer names as a comma-separated list, an array containing multiple computer names, or a parenthetical command that produces a collection of computer names as string objects.

Use the -Credential parameter to specify an alternative username. If you specify only a username, then you're prompted for the password. If you use the Get-Credential cmdlet to store the username and password in a variable, then you can reference that variable to eliminate the password prompt. In the following example, you'll be prompted for the password:

``` pwsh
Get-WmiObject -ComputerName LON-DC1 -Credential ADATUM\Administrator -Class Win32_BIOS
```

When you specify multiple computer names, Windows PowerShell contacts them one at a time in the order that you specify. If connectivity to one computer fails, the command produces an error message and continues to try the remaining computers.

## Using CIM Cmdlets
The CIM cmdlets also provide support for ad hoc connections to remote computers by using the -ComputerName parameter. However, the CIM cmdlets don't have a -Credential parameter to specify alternate credentials. If you want to use alternate credentials, you need to create a CIM session.

You can run the following CIM command to retrieve the same information as the Get-WmiObject command in the previous code example:

``` pwsh
Get-CimInstance -ComputerName LON-DC1 -Classname Win32_BIOS
```

Remember that CIM commands use the WS-MAN protocol for ad hoc connections. This protocol has specific authentication requirements. When establishing a connection between computers in the same domain or in trusting domains, you typically have to provide a computer’s name as it displays in Active Directory Domain Services (AD DS). You can't provide an alias name or an IP address because that will result in a failure of Kerberos authentication.

## CIMSession objects

A Common Information Model (CIM) session is a persistent configuration object that's used when creating a connection to a remote computer. The connection uses WS-MAN by default, but you can specify the DCOM protocol. After a session is created, you can use it to process multiple queries for that computer. This simplifies connectivity because all of the configuration options are contained in the session. A CIM session also allows you to specify connectivity options that aren't available for an ad hoc connection.

### Creating session objects
When you create a session, you should store it in a variable to reference it later. The basic syntax to create a session and store it in a variable is:

``` pwsh
$s = New-CimSession -ComputerName LON-DC1
# You can create multiple sessions at the same time:
$sessions = New-CimSession -ComputerName LON-CL1,LON-DC1
```

When you create a session, PowerShell doesn't establish the connection immediately. When a cmdlet uses the CIM session, PowerShell connects to the specified computer, and then, when the cmdlet finishes, PowerShell terminates the connection.

In some cases, it might be beneficial to use PowerShell remoting instead of CIM sessions for remote connectivity. PowerShell remoting opens a connection to the remote computer and keeps it open until explicitly closed. If you're running multiple queries against a computer, this might improve performance.

### Using Sessions
After you've stored the session in a variable, you reference it with CIM cmdlets by using the -CimSession parameter. The following example uses a variable that contains multiple sessions:

``` pwsh
Get-CimInstance -CimSession $sessions -ClassName Win32_OperatingSystem
```

Remember that sessions are designed to work best in a domain environment, between computers in the same domain or in trusting domains. If you have to create a session to a nondomain computer or to a computer in an untrusted domain, you'll need to do additional configuration.

The help information for some cmdlets such as Get-SmbShare states that they support a -CimSession parameter. Those commands use CIM internally. When you use those commands to query a remote computer, you can provide a CIM session object to the -CimSession parameter to connect by using an existing session.

### Configuring session options
A session option object allows you to specify many settings for a session. When you create a new session, you specify the session option object to configure the session. The following example creates a session by using DCOM instead of WS-MAN:

``` pwsh
$opt = New-CimSessionOption -Protocol Dcom
$DcomSession = New-CimSession -ComputerName LON-DC1 -SessionOption $opt
Get-CimInstance -ClassName Win32_BIOS -CimSession $DcomSession
```

The first line in the preceding code creates a session option object that specifies that the DCOM protocol should be used for connectivity. The second line creates a new session by using that session option object and stores it in a variable. The final line uses the session to query the remote computer defined in the session and return the requested information.

### Removing sessions
After you create a session, it remains in memory and available for use until the instance of PowerShell is closed. You can manually remove sessions by using the Remove-CimSession cmdlet. The following example removes one or more sessions contained in a variable:

``` pwsh
$sessions | Remove-CimSession
```

To remove the sessions for a specific remote computer, you can query the sessions for that computer and then remove them, as the following example depicts:

``` pwsh
Get-CimSession -ComputerName LON-DC1 | Remove-CimSession
```

To remove all sessions, run the following command:

``` pwsh
Get-CimSession | Remove-CimSession
```
