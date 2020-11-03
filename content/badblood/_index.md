---
title: BadBlood
weight: 20
pre: "<b>1. </b>"
chapter: false
tags: ["ESAE", "Microsoft"]
---

## BadBlood by Secframe 
#### fills a Microsoft Active Directory Domain with an OU structure and thousands of objects. 

The output of BadBlood is a domain similar to one found in the real world. 

PIC 1

Use it to:
- Start a journey into privileged identity threat hunting.
- Build a test domain
- Test Vendor software


## What is BadBlood
It is a security tool for Active Directory.  Run BadBlood on a domain so that security analysts and engineers can practice using tools to gain an understanding and prescribe to securing Active Directory.

Each time this tool runs, it produces different results.  The domain, users, groups, computers and permissions are different.

> Every
> Single
> Time

1. [Download BadBlood and extract](https://github.com/davidprowe/BadBlood)
2. Run BadBlood `./badblood/invoke-badblood.ps1`
3. Enjoy

 The Invoke-BadBlood.ps1 PowerShell script is at the root of the BadBlood repo. A few prompts appear designed to ensure this is only run in a testing or educational environment. 
 
{{% notice warning %}} PLEASE! Only run this is in testing. {{ /notice }}
 
Follow the prompts to begin the deployment of BadBlood.
 
> BadBlood is designed to be run with a user who is a Domain Admin and Schema Admin. 

### 1. LAPS installed

Badblood begins by expanding the current domain’s schema by installing LAPS AD_Laps_install\InstallLAPSSchema.ps1

LAPS <!-- link to laps post--> is Microsoft’s free tool designed to remediated lateral movement across a domain by randomly generating passwords for the admin user on computer objects. 

### 2. Organizational Unit Structure Created
After extending the schema,
BadBlood creates a new OU structure on your domain.  The full details of this expansion can be viewed in the file

AD_OU_CreateStructure\CreateOUStructure.ps1
By personalizing the configuration file “3lettercodes.csv” BadBlood can customize the OUs that are added to the OUs I call "Top Level OUs". 

The detailed sub-structure of the OUs can be expanded by modifying the variables in the createoustructure.ps1.  The first release of the tool creates an OU structure similar to the one depicted below. 

BadBlood uses this new OU structure to randomize the placement of objects and permissions. 

### 3. Users Created
After the structure is complete, BadBlood begins to create a random number of users (500-10000) into the domain. This process is designed in the

AD_Users_Create\CreateUsers.ps1
During creation of each user BadBlood randomly selects an OU or container and places that person in a random path. The tool generates very random male and female users based on the text files located in the \Names folder under the AD_Users_Create folder. 

Also each user is created with a unique password.
The tool fills in a bit of information on the user account and moves onto the next step: Groups.

<!-- make some users with easier common passwords-->


Note: If you would like to create more users with this tool, edit the variable $NumOfUsers in the Invoke-BadBlood script. 


 
Tiered OU
The first release of this tool does not have parameters so you’ll have to live with editing the ps1 file.

### 4. Groups Created
After the users are complete, the tool moves on to creating the groups in the domain.  This is performed by

AD_Groups_Create\CreateGroups.ps1
The groups are randomly named in this script pulling information from a hotmail.txt file located in the same folder as the script. The groups, just like the users, are randomly placed in random OUs and Containers in the domain.

Note: If you would like to create more groups with this tool, edit the variable $NumOfGroups in the Invoke-BadBlood script. 

### 5. Computers Created
The tool now moves onto creating computers by calling

AD_Computers_Create\CreateComputers.ps1
These computers also have randomly generated names. Like the other objects previously created the computers are placed in random OUs.  If you would like to create more computers with this tool, edit the variable $NumOfComps in the Invoke-BadBlood script.  

 
badblood2
badblood3
 
### 6. ACLs Generated
The main sauce of the tool is this next step: The Permissions Generator. 
I played around a lot with Active Directory DACLs, and I made a generator to create random permissions every time this tool is run.  No matter what, this tool adds very random and very invasive permissions.  

This is the part of BadBlood that I am most proud of developing.
The permission generator script is

AD_Permission_Randomizer\GenerateRandomPermissions.ps1
Random Permissions
This script calls and imports the functions from the folder AD_OU_SetACL. There are many scripts in this folder, with many functions inside of each script.  They can be used to automate a lot of Active Directory permission tasks an admin might encounter during the work day.  

In BadBlood, I call upon these scripts to ruin, flood, and berate a Domain. These scripts are the scripts that make this Domain the worst Domain in the history of Domains.  

#### You are very welcome.

Back in the generaterandompermissions.ps1, all the permissions that are (randomly) set with BadBlood are found in the function “Create-PermissionSet.” There are a lot of permissions in this function.  BadBlood randomizes which permissions to add. It randomizes the permissions every time the function is called. BadBlood calls the function many, many times.

BadBlood takes these random permissions, and chooses random users and sets the random permissions on random OUs.  Then it chooses random groups and places the random permissions on random OUs. Then it does the same for random computers.  

> Hint: At this point in time, everything is very random.

### 7. Add Objects To Groups
The last and final step of Invoke-BadBlood is stored in Ad_Groups_Create\AddRandomToGroups.ps1.  This script performs three major functions to finish up BadBlood

Selects 80% of the users on the domain and adds them to random groups.  Now these users interior the permissions granted to the groups! Also they are affected by the permissions applied to the groups they are now a member of.
Selects 20% of the groups, and adds them to random groups.  Because of this, permissions begin to get even nastier on the domain
Selects 10% of the computers and adds them to random groups too.  Why? I thought it was a good idea and it only took one more loop to complete.
Notice the Number of Users, Computers, Groups, ACLs, and Relationships
unrolled group membership
badblood5_bloodhoundimport


## Sample Output
After BadBlood runs it’s time to gather data on the domain.  Here is sample output created by running Bloodhound on the domain destroyed by BadBlood.  

A sample output of the standard query “Shortest Path to Domain Admin”  Ain’t it ugly???
 
Spot the person on the very left “Noemi_Henry”. 
First: isnt that name awesome? Second Expand who can control Noemi

shortestpath
shortestpath_object controllers of a user
Naomi has 90+ explicit object controllers.  This domain is a red teamers’ dream. It’s also a dream for one security consultant who would like you to contact him to perform an analysis on the domain and write a plan on how to secure this infrastructure.  Please contact me with any requests on Active Directory Security

I hope you enjoy this tool in your DEVELOPMENT or TRAINING environment.  It’s the first public tool out there that can load an immense number of spam into an Active Directory domain.  

Let me know what you think.  I have an open social media network, so feel free to contact me.  If you have a question or want to talk more please get in contact with me. 

[Download BadBlood](https://github.com/davidprowe/BadBlood)