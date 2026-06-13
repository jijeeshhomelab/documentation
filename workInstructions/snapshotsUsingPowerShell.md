# Snapshots Operations Using PowerShell Script

## Changelog

|    Date    |   TOS   |   Issue   | Author                | Description |
|------------|---------|-----------|-----------------------|-------------|
| 26.05.2022 | VCS 1.0 | DHC-4702  | Krishnasai Dandanayak | Initial document creation |
| 08.06.2022 |         |           | Krishnasai Dandanayak |Fixes : Linked the scripts in Documentation, Edited the sentence formation, Correction in Spelling and Modification at code|

## Introduction

### Purpose

Take snapshots of VCS VMs.

### Audience

- VCS Operations

### Scope

- Use PowerShell to create snapshots
- Use PowerShell to delete snapshots
- Create a scheduled task

### Template File

Create a text file and append the servers as shown in the example below.

```txt
     gre02adc002
     gre02ctl001
     gre02ops002
```

### vCenter Names

The below vCenter names are taken for test purpose, so required vCenter names need to be updated in a text file and update the path of text file as shown in below example:

```powershell
$VC = Get-Content "C:\Snapshots\vCenter.txt"
 connect-viserver -server $VC
```

### PowerShell  

 Need to have PowerShell installed on the server. It takes 3 minutes to connect each vCenter while executing the script.

## Creation Of SnapShots

Change the path where list of servers are saved in text format.
Script can be found at [createSnapshots.ps1](files\wiSnapshotsUsingPowerShell\createSnapshots.ps1)

```powershell
     $vmlist = Get-Content "C:\Snapshots\Snapshots1.txt"
```

Use the Unique name for taking snapshots, so that it will be useful while deleting those snapshots.

```powershell
    Get-VM $vmlist | New-Snapshot -Name "RITM" -RunAsync -Confirm:$false "   
```

## Deletion Of SnapShots

By Using the servers list and the unique name we can delete the snapshots as shown in below example and by using the -RunAsync the deletion action will be done in parallel.
Script can be found at [deleteSnapshots.ps1](files\wiSnapshotsUsingPowerShell\deleteSnapshots.ps1)

```powershell
    Get-VM $vmlist | Get-Snapshot | where Name -Like "Test" | Remove-snapshot -RunAsync -confirm:$false
```

## Deletion Of Older SnapShots

Script can be found at [deleteOldSnapshots](files\wiSnapshotsUsingPowerShell\deleteOldSnapshots.ps1)

### Task Scheduler

Open the TaskScheduler from Terminal server and right click on the task library and create a new task.
Example : gre02tss01

### Creation of Task in Task Scheduler

1. Open TaskScheduler
2. Right click on TaskLibrary and click on create task.
3. Provide the Name of the task and Description of the task.
4. Then click on the Triggers tab and click on New.
5. By selecting as weekly based. It might get complete asap, as it will be non-business hours.
6. Then click on Actions Tab and click on New.
7. Here in Program/Script column browse/Paste the below path:

    ```txt
        C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
    ```

### Batch File Code

```bat
" $Path = "C:\Snapshots\deleteOldSnapshots.ps1"
echo off
powershell.exe -ExecutionPolicy Bypass -Command $Path "
```

### Disabling the Task

1. If for any reason, we don't want to run this task then right click on required task and click on disable.
2. So that it won't be running and script won't be executed.
3. Once the work is done and for enabling the task, similarly right click on our disabled task and click on enable.
4. Whenever task needs to run manually, then right click on task and click on run. So that task will be running and older snapshots will get deleted as per the old aged.
