In addition to the traditional tasks that run in the foreground within the Windows PowerShell console or ISE, you also can run background tasks. When you run a command as a background job, Windows PowerShell performs the task asynchronously in its own thread. This thread is separate from the pipeline thread that the command uses. When a command runs as a background job, even if it takes a long time to complete, you regain access to the PowerShell prompt immediately. This allows you to run other commands while the job runs in the background.

## Types of Background Jobs

There are three basic types of background jobs:

1. Local jobs run their commands on the local computer and typically access only local resources. However, you can create local jobs that target a remote computer. For example, you could create a local job that includes the following command, where the –ComputerName parameter makes it connect to a remote computer:
    ``` pwsh
    Get-Service –Name * -ComputerName LON-DC1
    ```

2. Remote jobs use Windows PowerShell remote to transmit commands to one or more remote computers. The commands run on those remote computers and the results are returned to the local computer and stored in memory. Windows PowerShell Help files refer to this kind of job as a remote job.

3. CIM and WMI jobs use the Common Information Model (CIM) and Windows Management Instrumentation (WMI) repository of management information. The commands run on your computer but can connect to one or more remote computers' repositories. Local jobs that use CIM commands use the Start-Job command, whereas WMI and other commands use the -AsJob parameter within a WMI command.

Each job type has specific characteristics. For example, local and Windows PowerShell remote jobs run in a background Windows PowerShell run space. Think of them as running in a hidden instance of Windows PowerShell. Other types of jobs might have different characteristics. Also, add-in modules add more job types to Windows PowerShell, and those job types have their own characteristics. Remote jobs are useful for managing multiple remote computers simultaneously. Remote computers run Windows PowerShell remote commands by using their own local resources. Therefore, you can include any command in the job.

Remember that these job types aren't the only ones that Windows PowerShell supports. Use modules and other add-ins to create other job types. Workflow jobs help you automate long-running tasks, or workflows, by simultaneously targeting multiple managed computers or devices. Windows PowerShell workflows offer extra resiliency benefits. When you restart target devices, unfinished workflow automatically restarts on the target device or devices and continues with the job's workflow commands.

It's important to note that interactive Windows PowerShell console workflow jobs aren't available in a noninteractive Windows PowerShell console. Scheduling a task to run at machine startup won't find suspended jobs from a noninteractive console. Scheduled tasks can't find workflow jobs to resume unless you're signed in to an interactive session.

### Local Jobs
Start local jobs by running Start-Job. Provide either the –ScriptBlock parameter to specify a single command line or a small number of commands. Provide the –FilePath parameter to run an entire script on a background thread.

By default, jobs receive a sequential job identification (ID) number and a default job name. You can't change the assigned job ID number, but you can use the –Name parameter to specify a custom job name. Custom names make it easier to retrieve a job and identify it in the job list.

At first, job ID numbers might not seem to be sequential. However, you'll learn the reason for this later in this module.

You can specify the –Credential parameter to run a job under a different user account. Other parameters allow you to run the command under a specific Windows PowerShell version, in a 32-bit session, and in other sessions.

Here are examples of how to start local jobs:

``` pwsh
Start-Job -ScriptBlock { Dir C:\ -Recurse } -Name LocalDirectory

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
2      LocalDirectory  BackgroundJob   Running       True            localhost


Start-Job -FilePath C:\test.ps1 -Name TestScript

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
4      TestScript      BackgroundJob   Running       True            localhost
```

### Remote Jobs
Start Windows PowerShell remote jobs by running Invoke-Command. This is the same command that sends commands to a remote computer. Add the –AsJob parameter to make the command run in the background. Use the –JobName parameter to specify a custom job name. All other parameters of Invoke-Command are used in the same way. Here's an example:

``` pwsh
Invoke-Command -ScriptBlock { Get-EventLog -LogName System -Newest 10 }
-ComputerName LON-DC1,LON-CL1,LON-SVR1 -AsJob -JobName RemoteLogs

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
6      RemoteLogs      RemoteJob       Running       True            LON-DC1...
```

The –ComputerName parameter is a parameter of Invoke-Command, not of Get-EventLog. The parameter causes the local computer to coordinate the Windows PowerShell remote connections to the three computers specified. Each computer receives only the Get-EventLog command and runs it locally, returning results.

The computer on which you run Invoke-Command creates and manages remote jobs. You can refer to that computer as the initiating computer. The commands inside the job are transmitted to remote computers, which then run them and return results to the initiating computer. The initiating computer stores the job’s results in its memory.

### CIM and WMI Jobs
To use CIM commands in a job, you must launch the job with Start-Job. Here's an example:

``` pwsh
Start-Job  -ScriptBlock {Get-CimInstance -ClassName Win32_ComputerSystem}

Id     Name  PSJobTypeName  State   HasMoreData  Location   Command                  
--     ----  -------------  -----   -----------  --------   -------                  
3      Job3  BackgroundJob  Running True         localhost  Get-CimInstance -Class..
```

You also can run other commands that use CIM as jobs by using Start-Job. An example is Invoke-CimMethod. The fact that CIM commands don't have an –AsJob parameter isn't important. You just need to remember to use the job commands when you want to run CIM commands as jobs.

Start a WMI job by running Get-WmiObject. This is the same command you'd use to query WMI instances. Add the –AsJob parameter to run the command on a background thread. There's no option to provide a custom job name. The Get-Help information for Get-WmiObject states the following for the –AsJob parameter:

To use this parameter with remote computers, the local and remote computers must be configured for Windows PowerShell remoting. Additionally, you must start Windows PowerShell by using the Run as administrator option in Windows 7 and newer versions of Windows.

WMI jobs don't require that you enable Windows PowerShell remoting on either the initiating computer or the remote computer. However, they do require that WMI be accessible on the remote computers.

Here's an example:

``` pwsh
Get-WmiObject -Class Win32_NTEventLogFile -ComputerName localhost,LON-DC1 -AsJob

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ---

-            -------------   -----         -----------     --------
10     Job10           WmiJob          Running       True            localho...
```

## Job Objects
Notice that each of the preceding examples results in a job object. It represents the running job, and you can use it to monitor and manage the job.

Each job consists of at least two job objects. The parent job is the top-level object and represents the entire job, regardless of the number of computers to which the job connects. The parent job contains one or more child jobs. Each child job represents a single computer. A local job contains only one child job. Windows PowerShell remoting and WMI jobs contain one child job for each computer that you specify.

### Retrieving Jobs

Run Get-Job to list all current jobs. You can list a specified job by adding the –ID or –Name parameter and specifying the desired job ID or job name. Retrieve child jobs by using the job ID. Here are examples:

``` pwsh
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
2      LocalDirectory  BackgroundJob   Running       True            localhost
4      TestScript      BackgroundJob   Completed     True            localhost
6      RemoteLogs      RemoteJob       Failed        True            LON-DC1...
10     Job10           WmiJob          Failed        False           localho...


Get-Job -Name TestScript

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
4      TestScript      BackgroundJob   Completed     True            localhost


Get-Job -ID 5

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
5      Job5                            Completed     True            localhost
```

Notice that each job has a state. Parent jobs always display the state of any failed child jobs, even if other child jobs succeeded. In other words, if a parent job contains four child jobs, and three of those jobs finished successfully but one failed, the parent job status will be Failed.

### Listing Jobs
List the child jobs of a specified parent job by retrieving the parent job object and expanding its ChildJobs property. In Windows PowerShell 3.0 and newer, use the -IncludeChildJobs parameter of Get-Job to display a job’s child jobs. Here's an example:

``` pwsh
C:\PS>Get-Job -Name RemoteJobs -IncludeChildJobs
   
Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
7      Job7                            Failed        False           LON-DC1
8      Job8                            Completed     True            LON-CL1
9      Job9                            Failed        False           LON-SVR1
```

The earlier method uses the following example:

``` pwsh
Get-Job -Name RemoteLogs | Select -ExpandProperty ChildJobs

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
7      Job7                            Failed        False           LON-DC1
8      Job8                            Completed     True            LON-CL1
9      Job9                            Failed        False           LON-SVR1
```

This technique enables you to discover the job ID numbers and names of the child job objects. Notice that child jobs all have a default name that corresponds with their ID number. The preceding syntax will work in Windows PowerShell 2.0 and newer versions.

### Job-Management Commands
Manage jobs by using several available Windows PowerShell commands. Pipe one or more jobs to each of these commands or specify jobs by using the –ID or –Name parameters. Both parameters accept multiple values, which means that you can specify a comma-separated list of job ID numbers or names. The job-management commands include:

+ Stop-Job, which halts a job that's running. Use this command to cancel a job that's in an infinite loop or that has run longer than you want.
+ Remove-Job, which deletes a job object, including any command results stored in memory. Use this command when you're finished working with a job, so the shell releases memory.
+ Wait-Job, which you typically use in a script. It pauses script processing until the jobs you indicate reach the specified state. Use this command in a script to start several jobs, and then make the script wait until those jobs complete before continuing.

The Windows PowerShell process manages remote, WMI, and local jobs. When you close a PowerShell session, Windows PowerShell removes all jobs and their results, and you can no longer access them.

### Retrieve Results

When a job runs, Windows PowerShell stores all command outputs in memory, starting with the first output that the command produced. You don't have to wait for a command to complete before output becomes available.

The job list indicates whether a job has stored results that you haven’t yet retrieved. Here's an example:

``` pwsh
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location
--     ----            -------------   -----         -----------     --------
13     Job13           BackgroundJob   Running       True            localhost
```

In this example, job ID 13 is still running, but the HasMoreData column indicates that results already have been stored in memory. To receive a job's results, use the Receive-Job command.

By default, job results are removed from memory after they're delivered to you. That means that you can use Receive-Job only once per job. Add the –Keep parameter to retain a copy of the job results in memory, so that you can retrieve them again.

If you retrieve the results of a parent job, you'll receive the results from all child jobs. You also can retrieve the results of a single child job or multiple child jobs.

You also can retrieve the results of a job that's still running. However, unless you specify –Keep, the job object’s results will be empty until the job’s command adds new output.

Here's an example:

``` pwsh
Receive-Job –ID 13 -Keep | Format-Table –Property Name,Length
```