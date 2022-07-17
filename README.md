# ESXI Config Backup Utility

The ESXI Config Backup Utility is a PowerShell script that makes it easy to take config backups of individual ESXI hosts or all of your ESXI hosts by connection to a vCenter appliance.  The script also makes it very easy to schedule the backups to run automatically by leveraging Windows Task Scheduler.  The script uses VMware PowerCLI to connect to the resource, initiate a backup, and store it on your local machine.  The script also includes retention settings in the event you only want to keep a certain number of them on hand. 

<img width="1085" alt="2022-07-17_19-05-53" src="https://user-images.githubusercontent.com/78672521/179428405-0d316295-5078-435a-bc53-2b4283e4171a.png">

# Features
 - Automate Scheduling of Script
 - **Weekly** or **Daily** scheduling options with time of day and day of week options.
 - Easy Deployment (Directories are created automatically and files are moved accordingly).
 - Configuration File (Paramaters are stored in a dedicated config file).
 - vCenter/Host Passwords are **not** stored in plain text.
 - Backup a single host or all hosts through vCenter.
 - Retention Options (Keep all backups or just a certain number).
 - Automatic install of VMware PowerCLI.
 - Windows Event Log Integration

# Requirements 

 - Windows OS (for scheduling)
 - PowerShell
 - VMware PowerCLI
 - Administator Access

# Instructions

The command line flags below can be used to enable the script and/or take backups.  Using **-Run** will bypass any config and give you a "Run Once" experience to take a backup.  If you need to edit your configuration, you'll need to run **-Disable** then **-SetConfig** then **-Enable**. 
## Command Line Flags
    
    -SetCreds - Use to set Host/vCenter credentials.
    -SetConfig - Use to set configuration options (IP/Hostname, Backup Schedule, Retention)
    -Enable - Use to enable backup schedule.
    -Disable - Use to disable backup schedule.
    
    -RunConfig - Use to take backups using your stored credentials and config file.
    -Run - Use to take backups without using your config file (you will be prompted for hostname/credentials).

## First Step

Download the file, be sure to open up properties on the zipped folder and unblock it. 
<img width="907" alt="2022-07-17_18-40-21" src="https://user-images.githubusercontent.com/78672521/179427632-dce0fb42-3a88-4a01-8efd-54d89cc4eb0e.png">

## Second Step

Open PowerShell window as an administrator, then run the PowerShell script in the window and wait.  

    ./ESXI-Config-Backup-Utility.ps1

It will look for PowerCLI if it doesn't exist it will install it, once it's done, it will take you back to the PowerShell command line.  

## Last Step

This isn't really the last step, but it's close enough for you to figure it out from here.  All you need to do now is use the **-Run** flag to do a one time backup or use the **-SetCreds**, **-SetConfig**, and **-Enable** flags (in that order) to get it going on a schedule. Before you use **-Enable** you can use **-RunConfig** to take a backup using your config to make sure there are no issue. 

# Notes
Below are some notes/tips that might help.

## File Locations
Everything is stored in **C:\Scripts\ESXI-Config-Backup-Utility\\** this directory is created automatically and is set in the Global Variables part of the script, if you change the location there it might work, I haven't tried it.  

## Event Logs
For several errors in the script it should create an event log entry in the Windows Event Applications Log under the name ESXI-Config-Backup-Utility, for some reason it shows up in the table view as **Backup-Utility**, I'm going to try to fix that in the future, if you expland the event details it does show the full name. 

## Changing Configs
If you want to change your config file you can run **-SetConfig** as mentioned before, it will take a backup of the existing config and put it in a directory called ConfigBackups with everything else.  You'll need to run **-Disable** then **-Enable** for the new config to take full effect. 

## Retention Settings
Retention settings seems to keep one more backup than what you requested.  I'm going to look into at some point but for now what it does is if your retention is 3, when a backup is started it will delete the oldest backups so that there are only 3 in the folder, then it will take a backup, at which point you'll now have 4 backups in the folder.  If I move the retention part to after the backup takes place that might fix it, I might do that soon.  

>**-Run** bypasses retention, only **-RunConfig** and scheduled backups run retention.

## Updates
When new versions are released I would recommend running **-Disable**, then with the new script version run **-Enable**, this will copy the new version into the correct location.  If you have issues it may be best to just delete all the files in the *C:\Scripts\ESXI-Config-Backup-Utility* directory and reconfigure the utility. Deleting the directory will not remove the scheduled task in Windows Task Scheduler, you'll need to open up task scheduler and remove it manually or use the **-Disable** flag to remove it. 
