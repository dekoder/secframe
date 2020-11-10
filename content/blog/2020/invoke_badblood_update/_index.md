---
title: Invoke-Badblood.ps1 New Features and Speed Increase
description: Major updates rolling into the BadBlood Active Directory Generator toolkit.  Check out sidhistory injection, speed upgrades and more.
date : 2020-04-23T03:06:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory']
aliases:
    - /blog/invoke-badblood.ps1-update-new-features-and-speed-increase
---

I wanted to inform the interested crowds about a number of updates added to the BadBlood tool-set.

First and foremost, the user creation process is sped up. Everyone said "...I mean the user creation is a little slow, but it works."   I took that feedback to heart and I introduced stored variables into the user creation script, createusers.ps1. This now allows me to store an array of the Domain, OUList and scriptdir, and not query those three objects every time CreateUser is ran. This speeds up the user creation portion of badblood by about 300%

 

powershell create user automate
 

You can see in line 67 the createuser is using the domain, oulist and scriptdir variables. I pull these arrays in just moments before on the invoke-badblood.ps1 master script.

 

powershell create test domain
 

 

Updates to AddRandomTogroups.ps1
I had some suggestions from users saying that there were too many paths to DA and critical groups had too many objects inside of them. I took this to heart to do two things:

Add variables to the nesting script, ..\AD_Groups_Create\addrandomtogroups.ps1
Remove insanity on the local builtin groups and critical security groups
The variables Userlist, Grouplist, and LocalGroupList can now be used to call the AddRandomToGroups Function. This allows for a little faster run time of the nesting portion of the script, and an all around faster run time of the entire invoke-badblood function.

 

powershell how do i add users to groups
Below are the standards that the script uses if the variables arent used to call the function.

how do i filter critical groups powershell
 

The  invoke-badblood using the parameters to call on the AddRandomToGroups function...

 

how do i nest groups and users in powershell
 

AddRandomToGroups updated to include three main segmentation of groups:
Groups created by Badblood - standard security groups
Domain Local groups: ie Administrators and Key Admins
Domain Critical Groups: ie Enterprise Admins and Domain Admins
 

how do i add multiple users to a group with powershell
 

Using these new groups, I added a few more loops and lowered the number of user objects and group objects that get nested into the Critical Security Groups and Local Security groups. An example is seen below.

 

how do i add to critical groups in powershell
 

 

Bonus content created: SIdhistory injection
Using the material from my post Marching to the Top, I added the sidhistory injection flow into the Badblood repo. These scripts are seen at ..\BonusContent\SidHistory_Dsinternals.ps1. To make this script work, you need the dsinternals powershell module. Luckily for you its way easy to install and the method to deploy it is on line 2 of the sidhistory_dsinternals.ps1 script. Just comment it out.

 

active directory attack persistence sidhistory
 

This injection script gets the SID of 3 groups on your domain:

Administrators
Domain Admins
Enterprise Admins
The script then stops AD Services, injects the sid of these 3 groups into 1-10 users on your domain. It also injects the Sidhistory into 1-10 groups on the domain.

 

Fun fact, did you know sidhistory works on groups. And anyone nested in a group with a sidhistory gets the permissions from the sid?
 

active directory threat hunting and incident response
 

Sidhistory: Groups to groups

 

active directory threat hunting
 

 

Fixed the hotmail.txt file
This is used for group generation on the script ..\AD_groups_create\creategroups.ps1. I received some comments that some groups were failing when they were created. I removed all the invalid characters from the txt file. This creategroups.ps1 file injects the hotmail.txt file pulled from the securitylist repo. Hopefully this resolves all the instances of this error below.

 

active directory get-adgroup new-adgroup
 

 

New script added for testing and querying:
..\AD_Connect_To_TestDomain\Add-testdrive.ps1
 

Running add-testdrives function allows you to connect to your domain from your workstation or server. It adds a powershell drive to your powershell window. Once you CD to the $testname of your domain you can run standard AD queries on your test badblood generated domain, just like you were on any server that was on that domain. This is a perfect way for remote querying, testing, and administration of the domain.