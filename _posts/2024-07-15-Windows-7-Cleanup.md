---
layout: post
title: "Windows 7 Clean up tmp files"
date: 2024-07-15 04:00:00 -0500
categories: Windows
tags: Windows
---

Occasionally windows 7 will fill up temp locations from windows updates or other various reasons. Below is a script to clean up all the files. 

> *Note: Always inspect scripts from unknown locations*
{: .prompt-warning }

Feel free to download the link here <a href="https://s3.us-east-1.wasabisys.com/documentationpics/Cleanup.ps1">Cleanup.ps1</a>

```powershell
Function Cleanup {
<#
.Synopsis
   Aautomate cleaning up a C: drive with low disk space
   PowerShell v3
#>
function global:Write-Verbose ( [string]$Message )

# check $VerbosePreference variable, and turns -Verbose on
{ if ( $VerbosePreference -ne 'SilentlyContinue' )
{ Write-Host " $Message" -ForegroundColor 'Yellow' } }

$VerbosePreference = "Continue"
$DaysToDelete = 7
$LogDate = get-date -format "MM-d-yy-HH"
$objShell = New-Object -ComObject Shell.Application 
$objFolder = $objShell.Namespace(0xA)
$ErrorActionPreference = "silentlycontinue"
                    
Start-Transcript -Path C:\Windows\Temp\$LogDate.log

## Cleans all code off of the screen.
Clear-Host

$size = Get-ChildItem C:\Users\* -Include *.iso, *.vhd -Recurse -ErrorAction SilentlyContinue | 
Sort Length -Descending | 
Select-Object Name, Directory,
@{Name="Size (GB)";Expression={ "{0:N2}" -f ($_.Length / 1GB) }} |
Format-Table -AutoSize | Out-String

$Before = Get-WmiObject Win32_LogicalDisk | Where-Object { $_.DriveType -eq "3" } | Select-Object SystemName,
@{ Name = "Drive" ; Expression = { ( $_.DeviceID ) } },
@{ Name = "Size (GB)" ; Expression = {"{0:N1}" -f( $_.Size / 1gb)}},
@{ Name = "FreeSpace (GB)" ; Expression = {"{0:N1}" -f( $_.Freespace / 1gb ) } },
@{ Name = "PercentFree" ; Expression = {"{0:P1}" -f( $_.FreeSpace / $_.Size ) } } |
Format-Table -AutoSize | Out-String                      
                    
## Stops the windows update service. 
Get-Service -Name wuauserv | Stop-Service -Force -Verbose -ErrorAction SilentlyContinue
## Windows Update Service has been stopped successfully!

## Deletes the contents of windows software distribution.
Get-ChildItem "C:\Windows\SoftwareDistribution\*" -Recurse -Force -Verbose -ErrorAction SilentlyContinue |
Where-Object { ($_.CreationTime -lt $(Get-Date).AddDays(-$DaysToDelete)) } |
remove-item -force -Verbose -recurse -ErrorAction SilentlyContinue
## The Contents of Windows SoftwareDistribution have been removed successfully!

## Deletes the contents of the Windows Temp folder.
Get-ChildItem "C:\Windows\Temp\*" -Recurse -Force -Verbose -ErrorAction SilentlyContinue |
Where-Object { ($_.CreationTime -lt $(Get-Date).AddDays(-$DaysToDelete)) } |
remove-item -force -Verbose -recurse -ErrorAction SilentlyContinue
## The Contents of Windows Temp have been removed successfully!
             
## Delets all files and folders in user's Temp folder. 
Get-ChildItem "C:\users\*\AppData\Local\Temp\*" -Recurse -Force -ErrorAction SilentlyContinue |
Where-Object { ($_.CreationTime -lt $(Get-Date).AddDays(-$DaysToDelete))} |
remove-item -force -Verbose -recurse -ErrorAction SilentlyContinue
## The contents of C:\users\$env:USERNAME\AppData\Local\Temp\ have been removed successfully!
                    
## Remove all files and folders in user's Temporary Internet Files. 
Get-ChildItem "C:\users\*\AppData\Local\Microsoft\Windows\Temporary Internet Files\*" `
-Recurse -Force -Verbose -ErrorAction SilentlyContinue |
Where-Object {($_.CreationTime -le $(Get-Date).AddDays(-$DaysToDelete))} |
remove-item -force -recurse -ErrorAction SilentlyContinue
## All Temporary Internet Files have been removed successfully!
                    
## Cleans IIS Logs if applicable.
Get-ChildItem "C:\inetpub\logs\LogFiles\*" -Recurse -Force -ErrorAction SilentlyContinue |
Where-Object { ($_.CreationTime -le $(Get-Date).AddDays(-60)) } |
Remove-Item -Force -Verbose -Recurse -ErrorAction SilentlyContinue
## All IIS Logfiles over x days old have been removed Successfully!
                  
## deletes the contents of the recycling Bin.
## The Recycling Bin is now being emptied!
$objFolder.items() | ForEach-Object { Remove-Item $_.path -ErrorAction Ignore -Force -Verbose -Recurse }
## The Recycling Bin has been emptied!

## Starts the Windows Update Service
##Get-Service -Name wuauserv | Start-Service -Verbose

$After =  Get-WmiObject Win32_LogicalDisk | Where-Object { $_.DriveType -eq "3" } | Select-Object SystemName,
@{ Name = "Drive" ; Expression = { ( $_.DeviceID ) } },
@{ Name = "Size (GB)" ; Expression = {"{0:N1}" -f( $_.Size / 1gb)}},
@{ Name = "FreeSpace (GB)" ; Expression = {"{0:N1}" -f( $_.Freespace / 1gb ) } },
@{ Name = "PercentFree" ; Expression = {"{0:P1}" -f( $_.FreeSpace / $_.Size ) } } |
Format-Table -AutoSize | Out-String

## before and after results

Hostname ; Get-Date | Select-Object DateTime
Write-Verbose "Before: $Before"
Write-Verbose "After: $After"
Write-Verbose $size
## Completed Successfully!
Stop-Transcript } Cleanup


```