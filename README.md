CryptoBlocker
==============

This is a solution to block users infected with different ransomware variants.

The script will install File Server Resource Manager (FSRM), and set up the relevant configuration.

<b>Script Deployment Steps</b>

<i><b>NOTE:</b> Before running, please add any known good file extensions used in your environment to SkipList.txt, one per line.  This will ensure that if a filescreen is added to the list in the future that blocks that specific file extension, your environment won't be affected as they will be automatically removed.  If SkipList.txt does not exist, it will be created automatically.</i>

1. Checks for network shares
2. Installs FSRM
3. Create batch/PowerShell scripts used by FSRM
4. Creates a File Group in FSRM containing malicious extensions and filenames (pulled from https://fsrm.experiant.ca/api/v1/get)
5. Creates a File Screen in FSRM utilising this File Group, with an event notification and command notification
6. Creates File Screens utilising this template for each drive containing network shares

<b> How it Works</b>

If the user attempts to write a malicious file (as described in the filescreen) to a protected network share, FSRM will prevent the file from being written and send an email to the configured administrators notifying them of the user and file location where the attempted file write occured.

<b>NOTE: This will NOT stop variants which use randomised file extensions, don't drop README files, etc</b>

<b>Usage</b>

First, download the files DeployCryptoBlockerInstall.ps1 and DeployCryptoBlockerModify.ps1 into folder c:\C:\RamsomBlock\ (if you want change this folder, please review this files and change the path in the $SkipListLoc ).

This script configure de FileGroups, FileScreen, and apply this filescreen for each drive network shares.
Once this file are executed, you can add your own filters, and apply the CryptoBlocker template for source.

At next you can schedule the execution of DeployCryptoBlockerModify.ps1 via task scheduler.
This file is the same of DeployCryptoBlockerInstall.ps1 except for the part of installing the FSRM role (we assume it is already installed), and the part to recreate both the FileScreen and to apply the Filter to the network drives that we have configured.
In this file the File Group are not deleted and recreated. Only modify with the new list of suspicious extensions.
Therefore, since it is not necessary to create the FileScreen, it is not necessary to reapply the filters to the folders to be protected, and the protection of selected folders (not only network shares) always be active.

An event will be logged by FSRM to the Event Viewer (Source = SRMSVC, Event ID = 8215), showing who tried to write a malicious file and where they tried to write it. Use your monitoring system of choice to raise alarms, tickets, etc for this event and respond accordingly.

<b>Disclaimer</b>

This script is provided as is.  I can not be held liable if this does not thwart a ransomware infection, causes your server to spontaneously combust, results in job loss, etc.
