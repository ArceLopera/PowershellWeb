Scheduled jobs are a combination of Windows PowerShell background jobs and Windows Task Scheduler tasks. Similar to background jobs, you define scheduled jobs in Windows PowerShell. Additionally, like tasks, job results are saved to disk, and scheduled jobs can run even if Windows PowerShell isn't running.

## Scheduled tasks
Scheduled tasks are part of the Windows core infrastructure components. Other Windows components and products that run on Windows extensively use scheduled tasks. They're generally simpler than scheduled jobs. The Task Scheduler enables you to configure a schedule for running almost any program or process, in any security context, triggered by various system events or a particular date or time.

However, scheduled tasks can't capture and manipulate task output. A scheduled task can run almost anything runnable on a Windows device, so it's impossible to anticipate and capture the scheduled task’s output. However, because a Windows PowerShell scheduled job is always a Windows PowerShell script, even if that script runs a non-Windows PowerShell program, the system is able to capture output. In this case, a Windows PowerShell object is returned at the end of the script block. A scheduled task consists of:

+ The Action, which specifies the program to be run.
+ The Principal, which identifies the context to use to run an action.
+ The Trigger, which defines the time or system event that determines when the program is to be run.
+ The Additional settings, which further configure the task and control how the action is run.

Commands that work with scheduled tasks are in the ScheduledTasks module that's included with Windows 10 and Windows Server 2019. To review the complete list of commands, run the following command:

``` pwsh
Get-Command –Module ScheduledTasks
```

As an example, check on the available scheduled tasks by running the Get-ScheduledTask command. This will list all available scheduled tasks, regardless of whether they're enabled or disabled.

Get information on a specific task by running Get-ScheduledTask with the -TaskPath parameter. For best practices, ensure that you put the actual path in quotes. Get further information about a particular task by using the Get-ScheduledTaskInfo command. You can then combine these commands using a pipeline to get additional information. For example, retrieve detailed information about the Automatic Update task running on your system by entering the following command:

``` pwsh
Get-ScheduledTask -TaskPath "\Microsoft\Windows\WindowsUpdate\" | Get-ScheduledTaskInfo
```

You also can create and run scheduled tasks from the Task Scheduler. However, what if you're running Windows PowerShell commands or scripts, or Windows tools that don't write their output to a file? If output is important to you, a better choice is using a Windows PowerShell scheduled job. After that job is in the Task Scheduler, you can manipulate it further. You can start or stop it in the Task Scheduler. If you want to create multiple scheduled jobs or tasks locally, or even on remote computers, automate their creation and maintenance with the scheduled job or the scheduled task commands.

Adding Windows PowerShell scripts as scheduled jobs in the Task Scheduler can greatly improve your productivity. PowerShell Gallery contains thousands of scripts that you can use or modify for your specific use, and these scripts are separated into various categories.

For example, there are hundreds of viable scripts that you can run against Active Directory Domain Services (AD DS) and other Active Directory services. Some of these scripts can be very useful. An example is the script that finds user accounts that haven't been used for more than 90 days and then disables them, which can help strengthen domain security. You can modify this script to your specific domain, and then create it as a scheduled job. After you configure this task, you can then find and manipulate the job in the Task Scheduler. Schedule it to run every week and provide a report about what accounts, if any, were disabled.

## Define Scheduled Jobs

Scheduled jobs are a useful combination of Windows PowerShell background jobs and Windows Task Scheduler tasks. Similar to the latter, scheduled jobs are saved to disk. You can review and manage Windows PowerShell scheduled jobs in the Task Scheduler, enabling and disabling tasks or simply running the scheduled job. You can even use the scheduled job:

+ As a template for creating other scheduled jobs.
+ To establish a one-time schedule or periodic schedule for starting jobs.
+ To set conditions under which jobs start again.

You can do all of these tasks from the Task Scheduler.

Windows PowerShell saves the results of scheduled jobs to disk and creates a running log of job output. Scheduled jobs have a customized set of commands that you can use to manage them. You can use these commands to create, edit, manage, disable, and re-enable scheduled jobs, job triggers, and job options.

To create a scheduled job, use the scheduled job commands. Note that anything created in Task Scheduler is considered a scheduled task even if it's in the Microsoft\Windows\PowerShell\ScheduledJobs path in the Task Scheduler. After you create a scheduled job, review and manage it in the Task Scheduler by selecting a scheduled job to:

+ Find the job triggers on the Triggers tab.
+ Find the scheduled job options on the General and Conditions tabs.
+ Review the job instances that have already been run on the History tab.

When you change a scheduled job setting in Task Scheduler, the change applies for all future instances of that scheduled job.

The commands that work with scheduled jobs in the PSScheduledJob module are included in the current versions of the Windows Server and Client operating systems. To review the complete list of commands, run the following command:

``` pwsh
Get-Command –Module PSScheduledJob
```

Scheduled jobs consist of three components:

+ The job itself defines the command that will run.
+ Job options define options and running criteria.
+ Job triggers define when the job will run.

You typically create a job option object and a job trigger object, and store those objects in variables. You then use those variables when creating the actual scheduled job.

The ScheduledTasks module includes commands that can manage all tasks in the Windows Task Scheduler.

### Job options
Use New-ScheduledJobOption to create a new job option object. This command has several parameters that let you define options for the job, such as:

+ –HideInTaskScheduler, which prevents the job from displaying in the Task Scheduler. If you don't include this option, the final job will display in the Task Scheduler graphical user interface (GUI).
+ –RunElevated, which configures the job to run under elevated permissions.
+ –WakeToRun, which wakes the computer when the job is scheduled to run.

Use other parameters to configure jobs that run when the computer is idle and other options. Many parameters correspond to options in the Task Scheduler GUI.

Create a new option object and store it in a variable by using the following command:

``` pwsh
$opt = New-ScheduledJobOption –RequireNetwork –RunElevated -WakeToRun
```

You don't need to create an option object if you don't want to specify any of its configuration items.

### Job triggers
A job trigger defines when a job will run. Each job can have multiple triggers. You create a trigger object by using the New-JobTrigger command. There are five basic types of triggers:

+ –Once specifies a job that runs one time only. You can also specify a –RandomDelay, and you must specify the –At parameter to define when the job will run. That parameter accepts a System.DateTime object or a string that can be interpreted as a date.
+ –Weekly specifies a job that runs weekly. You can specify a –RandomDelay, and you must specify both the –At and –DaysOfWeek parameters. –At accepts a date and time to define when the job will run. –DaysOfWeek accepts one or more days of the week to run the job. You'll typically use –At to specify a time and use –DaysOfWeek to define the days the job should run.
+ –Daily specifies a job that runs every day. You must specify –At and provide a time when the job will run. You can also specify a –RandomDelay.
+ –AtLogOn specifies a job that runs when the user logs on. This kind of job is similar to a logon script, except that it's defined locally rather than in the domain. You can specify –User to limit the user accounts that trigger the job, and –RandomDelay to add a random delay.
+ –AtStartUp is similar to –AtLogOn, except that it runs the job when the computer starts. That typically runs the job before a user signs in.

For example, the following command creates a trigger that runs on Mondays and Thursdays every week, at 3:00 PM local time:

``` pwsh
$trigger = New-JobTrigger -Weekly -DaysOfWeek Monday,Thursday -At '3:00PM'
```

## Create and Register a Scheduled Job

Use Register-ScheduledJob to create and register a new scheduled job. Specify any of the following parameters:

+ –Name is required and specifies a display name for the job.
+ –ScriptBlock is required and specifies the command or commands that the job runs. You also could specify + –FilePath and provide the path and name of a Windows PowerShell script file that the job will run.
+ –Credential is optional and specifies the user account that will be used to run the job.
+ –InitializationScript accepts an optional script block. The command or commands in that script block will run before the job starts.
+ –MaxResultCount is optional and specifies the maximum number of result sets to store on disk. After this number is reached, the shell deletes older results to make room for new ones. The default value for the + -MaxResultCount parameter is 32.
+ –ScheduledJobOption accepts a job option object.
+ –Trigger accepts a job trigger object.

To register a new job by using an option object in $opt and a trigger object in $trigger, use the following example:

``` pwsh
$opt = New-ScheduledJobOption -WakeToRun

$trigger = New-ScheduledTaskTrigger -Once -At (Get-Date).AddMinutes(5)

Register-ScheduledJob -Trigger $trigger -ScheduledJobOption $opt -ScriptBlock { Dir C:\ } -MaxResultCount 5 -Name "LocalDir"

Id         Name            JobTriggers     Command       Enabled   
--         ----            -----------     -------        -------   
1          LocalDir        1                Dir C:\        True
```

Windows PowerShell registers the resulting job in the Windows Task Scheduler and creates the job definition on disk. Job definitions are XML files stored in your profile folder in \AppData\Local\Microsoft\Windows\PowerShell\ScheduledJobs.

You can run Get-ScheduledJob to review a list of scheduled jobs on the local computer. If you know a scheduled job’s name, you can use Get-JobTrigger and the –Name parameter to retrieve a list of that job’s triggers.

## Retrieve The Results

Because scheduled jobs can run when Windows PowerShell isn't running, results are stored on disk in XML files. If you create a job by using the –MaxResultCount parameter, the shell automatically deletes old XML files to make room for new ones. This deletion ensures that no more XML files exist than were specified in the –MaxResultCount parameter.

After a scheduled job finishes, running Get-Job in Windows PowerShell displays the scheduled job's results as a job object.

Here's an example:

``` pwsh
Get-Job

Id     Name      PSJobTypeName   State         HasMoreData     Location       Command
--     ----      -------------   -----         -----------     --------       -------
6      LocalDir  PSScheduledJob  Completed     True            localhost      Dir C:\
```

You can use Receive-Job to get a scheduled job's results. If you don't specify –Keep, you can receive a job's results only once per Windows PowerShell session. However, because the results are stored on disk, you can open a new Windows PowerShell session and receive the results again. For example:

``` pwsh
Receive-Job -id 6 -Keep


    Directory: C:\


Mode                LastWriteTime     Length Name                    
----                -------------     ------ ----                    
d----         7/26/2021  12:33 AM            PerfLogs                
d-r--        11/28/2021   1:54 PM            Program Files           
d-r--        12/28/2021   2:22 PM            Program Files (x86)     
d----        11/16/2021   9:33 AM            reports                 
d----         9/18/2021   7:28 AM            Review                  
d----          1/5/2022   7:49 AM            scr                     
d----          1/5/2022   7:50 AM            scrx                    
d-r--         9/15/2021   8:16 AM            Users                   
d----        12/19/2021   3:24 AM            Windows                 
-a---          1/1/2022   9:39 AM    2892628 EventReport.html        
-a---          1/2/2022  12:37 PM         82 Get-DiskInfo.ps1        
-a---        12/30/2021  12:33 PM        246 test.ps1
```

Each time the scheduled job runs, Windows PowerShell creates a new job object to represent the results of the most recent job that ran. You can use Remove-Job to remove a job and delete its results file from disk, as the following example depicts:

``` pwsh
Get-Job -id 6 | Remove-Job
```

