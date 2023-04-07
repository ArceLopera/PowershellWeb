You can run commands on one or hundreds of computers with a single PowerShell command. PowerShell supports remote computing by using various technologies, including Windows Management Instrumentation (WMI), remote procedure call (RPC), and Web Services for Management (WS-Management, or WS-MAN). Windows PowerShell remoting uses the WS-Management protocol to let you run any Windows PowerShell command on one or more remote computers. For example, you can establish persistent connections, start interactive sessions, and run scripts on remote computers.

Remoting uses an open-standard protocol called Web Services for Management (WS-Management or WS-MAN). As the name implies, this protocol is built on the same HTTP, or HTTPS, protocol that web browsers use to communicate with web servers. This makes the protocol easier to manage and to route through firewalls. Windows operating systems implement the protocol by using the Windows Remote Management (WinRM) service. PowerShell supports WMI, WS-Management, and Secure Shell (SSH) remoting. In PowerShell 6, Remote Procedure Calls (RPC)-based communication is not supported. In PowerShell 7 and newer, RPC is supported only in Windows.

You must enable remoting on the computers on which you want to receive incoming connections, although no configuration is necessary on computers that are initiating outgoing connections. PowerShell remoting is enabled by default for incoming connections on all currently supported versions of Windows Server. You can also enable it on any computer that's running Windows PowerShell 3.0 or newer.

## Remoting Architecture
Remoting starts with the WinRM service. It registers one or more listeners, with each listener accepting incoming traffic through either HTTP or HTTPS. Each listener can be bound to a single local IP address or to multiple IP addresses. There is no dependency on Microsoft Internet Information Services (IIS), which means that IIS doesn't have to be installed for WinRM to function.

Incoming traffic includes a packet header that indicates the traffic’s intended destination, or endpoint. In Windows PowerShell, these endpoints are also known as session configurations. Each endpoint is associated with a specific application. When traffic is directed to an endpoint, WinRM starts the associated application, hands off the incoming traffic, and then waits for the application to complete its task. The application can pass data back to WinRM, and WinRM transmits the data back to the originating computer.

In a Windows PowerShell scenario, you would send commands to WinRM, which then runs the commands. The process is listed as Wsmprovhost in the remote computer’s process list. Windows PowerShell would then run those commands and convert the resulting objects (if there are any) into XML. The XML text stream is then handed back to WinRM, which transmits it to the originating computer. Windows PowerShell on the remote computer translates the XML back into static objects. This enables the command results to behave much like any other objects within the Windows PowerShell pipeline.

Windows PowerShell can register multiple endpoints or session configurations with WinRM. In fact, a 64-bit operating system will register an endpoint for both the 64-bit Windows PowerShell host and the 32-bit host, by default. You also can create your own custom endpoints that have highly precise permissions and capabilities assigned to them.

## Without Configuration
Many Windows PowerShell cmdlets have the ComputerName parameter that enables you to collect data and change settings on one or more remote computers. These cmdlets use varying communication protocols and work on all Windows operating systems without any special configuration.

These cmdlets include:

+ Restart-Computer
+ Test-Connection
+ Clear-EventLog
+ Get-EventLog
+ Get-HotFix
+ Get-Process
+ Get-Service
+ Set-Service
+ Get-WinEvent
+ Get-WmiObject

Typically, cmdlets that support remoting without special configuration have the ComputerName parameter and don't have the Session parameter.

To find these cmdlets in your session, enter:

``` pwsh
Get-Command | where { $_.parameters.keys -contains "ComputerName" -and $_.parameters.keys -notcontains "Session"}
```

## Over SSH

PowerShell remoting normally uses WinRM for connection negotiation and data transport. SSH is now available for Linux and Windows platforms and allows true multiplatform PowerShell remoting.

WinRM provides a robust hosting model for PowerShell remote sessions. SSH-based remoting doesn't currently support remote endpoint configuration and Just Enough Administration (JEA).

SSH remoting offers basic PowerShell session remoting between Windows and Linux computers. SSH remoting creates a PowerShell host process on the target computer as an SSH subsystem. Microsoft plans to eventually implement a general hosting model similar to WinRM to support endpoint configuration and JEA.

The New-PSSession, Enter-PSSession, and Invoke-Command cmdlets now have a new parameter set to support this new remoting connection.

To use PowerShell remoting over SSH, you must install PowerShell 6 or newer and SSH on all computers. Then, you must install both the SSH client (ssh.exe) and server (sshd.exe) executables so that you can remote to and from the computers. OpenSSH for Windows is available starting with Windows 10 build 1809 and Windows Server 2019. For Linux, install the version of SSH (including the sshd.exe server) that's appropriate for your platform. You also need to install the current version of PowerShell from GitHub to ensure that the SSH remoting feature is available. You should configure the SSH server to create an SSH subsystem to host a PowerShell process on the remote computer. You also need to enable either password or key-based authentication.

## Compare Remoting with Remote Connectivity

Remoting is the name of a specific Windows PowerShell feature, not to be confused with the more generic concept of remote connectivity. Remoting is a generalized way to transmit any command to a remote computer so it runs locally on that computer. The command that you run doesn't have to be available on the computer that initiates the connection. Only the remote computers must be able to run it.

The purpose of remoting is to reduce or eliminate the need for individual command authors to code their own communications protocols. Many command authors are already required to do this to ship their products. This is why many different protocols and technologies are currently in use.

Many commands implement their own communications protocols, although in the future many of them might instead be changed to use remoting. For example, Get-WmiObject uses RPCs, whereas Get-Process communicates with the computer’s Remote Registry service. Microsoft Exchange Server commands have their own communications channels, and Active Directory commands communicate with a specific web service gateway by using their own protocol. All these other forms of communication might have unique firewall requirements and might require specific configurations to be in place to operate.


## Enabling Remoting

It's important to understand that you need to enable Windows PowerShell remoting only on computers that will receive incoming connections. No configuration is necessary to enable outgoing communications, except for making sure that any local firewall will allow the outgoing traffic.

### Manually

PowerShell remoting is enabled by default on Windows Server platforms. You can enable PowerShell remoting on other supported Windows versions, and you can also re-enable remoting if it becomes disabled. To manually enable Windows PowerShell remoting on a computer, run the Windows PowerShell Enable-PSRemoting cmdlet. This is a persistent change that you can disable later by running Disable-PSRemoting. Note that this task requires the privileges granted to local Administrators group.

The Enable-PSRemoting cmdlet performs the following operations:

1. Runs the Set-WSManQuickConfig cmdlet, which in turn performs the following tasks:

    + Starts the WinRM service.
    + Sets the startup type on the WinRM service to Automatic.
    + Creates a listener to accept requests on any IP address.
    + Enables a firewall exception for WS-Management communications.
    + Creates the simple-name and long-name session endpoint configurations, if needed.
    + Enables all session configurations.
    + Changes the security descriptor of all session configurations to allow remote access.

2. Restarts the WinRM service to make the preceding changes effective.

This command will fail on client computers where one or more network connections are set to Public instead of Work or Home. You can override this failure by adding the –SkipNetworkProfileCheck parameter. However, be aware that Windows Firewall won't allow exceptions when you're connected to a Public network.

The Set-WSManQuickConfig cmdlet doesn't affect remote endpoint configurations created by Windows PowerShell. It only affects endpoints created with PowerShell version 6 and newer. To enable and disable PowerShell remoting endpoints that are hosted by Windows PowerShell, run the Enable-PSRemoting cmdlet from within a Windows PowerShell session.

### By Using a GPO
Many organizations will prefer to centrally control Windows PowerShell remoting enablement and settings through GPOs. Microsoft supports this capability. You must set up various settings in a GPO to duplicate the steps taken by Enable-PSRemoting. To enable remoting by using Group Policy, you should configure the Allow Remote Server Management Through WinRM policy setting in the appropriate GPO. This setting also allows you to filter IP addresses from which remote connections can be initiated. In addition to configuring this policy, you should also configure appropriate firewall exceptions, as described earlier.

## Common Remoting Techniques

The Enter-PSSession and Invoke-Command commands support several parameters that you can use to change common connection options. These parameters include:

+ –Port. Specifies an alternate TCP port for the connection. Use this parameter when the computer to which you're connecting is listening on a port other than the default 5985 (HTTP) or 5986 (HTTPS). Be aware that you can, locally or through Group Policy, configure a different port as a permanent new default.
+ –UseSSL. Instructs Windows PowerShell to use HTTPS instead of HTTP.
+ –Credential. Specifies an alternative credential for the connection. This credential will be validated by the remote computer and must have sufficient privileges and permissions to perform whatever tasks you intend to perform on the remote computer.
+ –ConfigurationName. Connects to an endpoint (session configuration) other than the default endpoint. For example, you can specify microsoft.powershell32 to connect to the remote computer’s 32-bit Windows PowerShell endpoint.
+ –Authentication. Specifies an authentication protocol. The default is Kerberos authentication, but other options include Basic, CredSSP, Digest, Negotiate, and NegotiateWithImplicitCredential. The protocol that you specify must be enabled in the WS-Management configuration on both the initiating and receiving computers.

You can configure additional session options by using New-PSSessionOption to create a new session option object, and then passing it to the –SessionOption parameter of Enter-PSSession or Invoke-Command. Review the Help file for New-PSSessionOption to learn about its capabilities. You can modify the default values, such as the port number and enabled authentication protocols in the WSMan PowerShell drive.