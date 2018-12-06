There are two PowerShell Scripts documented here:
 - Deployment Script
 - Run SQL Commands

---

# PowerShell: deploymentScript

## Overview

To deploy the latest version of **iLink**, **SageService** & **web.config** files from the Staging Server to named iLink Servers.

## How to execute the PowerShell script

Open PowerShell, it does not have to be in administrator mode and run the below command:

```bash
./deploymentscript nlink01-ll-sw,nlink03-ll-sw,nlink05-ll-sw
```

```bash
./deploymentscript nlink02-ll-sw,nlink04-ll-sw
```

```bash
./deploymentscript nlink06-ll-sw
```

```bash
./deploymentscript lnlink01-ll-sw
```

You will get a prompt asking for an Administrator Username & Password. Enter your Admin account name and password.

## Names of Servers

### List of Live Servers that must be deployed too

- [nLink01-LL-SW](http://nlink01-ll-sw)
- [nLink02-LL-SW](http://nlink02-ll-sw)
- [nLink03-LL-SW](http://nlink03-ll-sw)
- [nLink04-LL-SW](http://nlink04-ll-sw)
- [nLink05-LL-SW](http://nlink05-ll-sw)

### List of backup servers that should be deployed too

- [nLink06-LL-SW](http://nlink06-ll-sw)

It is also worth Deploying to the following server as it is configured ready for Live and means we do have a backup Live server ready to use.

### Name for Load Balancer

- LB01-LL-SW

It is worth to note that this server is not just for iLink but the Web Team also use if for their Web Servers.

## The Process

There is an order in which to do the deployment and it is as follows:

1. Connect to the Load Balancer and activate the Rules
    - Deploy_nowservers_1_3_5
    - Deploy_nowservers_2_4
2. You will need a C:\Temp folder on the machine you are going to run the script from, if not one will be created for you
3. Open and Windows PowerShell CLI and enter the script name along with the servers you wish to deploy too
4. Wait for the process to complete, you will have a constant update on the screen
5. There should be a log file that is create and updated throughout the process
    - e.g. C:\Temp\20181026.0850_Deployment_LogFile.txt
6. Once complete check that the servers you deployed to are working by open each in your browser
    - e.g. <http://nlink01-ll-sw>
7. Check it has been sucessful by reading the deployment log file and making sure that it's ended correctly
8. If successful then on the Load Balancer disable rule
    - Deploy_nowservers_1_3_5
9. Run the PowerShell script for the next set of servers
10. Once complete check the servers using their own url and the deployment log file
11. If successful then on the Load Balancer disable rule
    - Deploy_nowservers_2_4
12. Also deploy to any backup servers mentioned in the list

## Structure on the Staging Server to things to work

Since the code is copied from the Staging Server to the Live servers there must be a structure that is kept in tact for it all to work. DO NOT RENAME ANY FOLDERS!

On the E: Drive on the Staging Server there is a folder as below:

 - [\\\\DEVTEST01-LD-SW\Live Deployment Source](file://DEVTEST01-LD-SW/Live%20Deployment%20Source)

Under this folder there is a SQL-Scripts folder that contains SQL scripts that should be run after each deployment. There is also a folder for each server that this script can be used to deploy the latest version of iLink too. Within each folder there will be the followinf break down:

- nlink
- sageservice
- sagetasksservice

Within each one of these there is a

```bash
web.config
```

file for each system. If changes to the web.config is needed, you do not do them on the live server but within these files, so on the next deployment they will be copied across to the server.

---

# PowerShell: runSQLCommand

## Overview

This PowerShell script is designed to look into a folder on the Staging Server and execute them on the SQL server specified as a parameter to the PowerShell script.

## How to execute the PowerShell script

Open PowerShell by holding down the shift key and right mouse clicking the PowerShell icon. This will give you a popup menu where you can select "Run as a different user".

Enter your administrator Username and Password. The PowerShell session will start and it will now be running as the given user.

We use this method because when talking to the SQL Server you need to pass the credentials from your PowerShell session which are the same redentials needed to execute the SQL scripts.

Now key in one of the below commands:

**LIVE**

```bash
./runSQLcommand SQL01-LL-SW
```

**TEST**

```bash
./runSQLcommand SQLDEV02-LD-SW
```

## The location that the SQL scripts need to be placed

The location is on the Stating Server in a folder named:

 - [\\\\DEVTEST01-LD-SW\Live Deployment Source\SQL-Scripts](file://DEVTEST01-LD-SW/Live%20Deployment%20Source/SQL-Scripts)

All scripts in this folder need to have the extension of **.sql** and need to be written that they run through without requiring any additional input. 

All scripts need to be configured to run against the *LIVE* server and the *LIVE* databases. There should not be any comments or lines left that are pointing the Dev of Development enviroments.

## Log files and captured output

When the script runs it creates a log file of the progress and places this into C:\Temp. If the computer you are running it from does not have a C:\Temp one will be created for you. The names of the log file will be in the format of: 
 - yyyyMMdd.hhmm_Execute_SQLScripts_LogFile.txt

Any output from the the SQL scripts will be captured in a text file and saved in the C:\Temp folder on the PC you are running this from. The names of the output file will be in the format of:

 - yyyyMMdd.hhmm_SQLScripts_output.txt

