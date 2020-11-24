---
title: "Privileged Access Admin OU"
description: "What is the administrative OU?  How to begin with Red Forest PAWs. Patrol privileged credentials"
image: 'images/pawfig2.jpg'
date: 2020-11-01T15:36:24-05:00
weight: 30
pre: ": "
draft: true
tags: ['microsoft','security','redforest','paws',"Active Directory"]
---

## Begin work towards the ESAE

With a base understanding of tiers, administrators, unique passwords for computers and a brief definition of why a privileged access workstation is needed for Active Directory Security, it's time to start investing time into making changes in the environment. 

### Prerequisites

1. Download the PAW script from the technet library. [PAWS OU Creation Script](https://gallery.technet.microsoft.com/Privileged-Access-53a4673a)
1. Create OUs
1. Create Tiered groups using scripts
2. Move people and objects

>  Remember there is plenty of work to do. Eventually the focus will shift to Tier 1 and Tier 2 credentials.  For this small moment in time, FIRST make sure Tier 0 is secure as possible.

#### Protecting Active Directory and Admin Users and Privileges by creating an administrative OU
 
*CENTRALIZE ADMINISTRATIVE ACCOUNTS:*

The location of the administrative users and groups is often overlooked. If I were to perform an audit on any random domain, there's a major chance that I'd find administrative accounts scattered across multiple Organizational Units. 

> Often by accident this scattered user placement creates pathways that attackers exploit to gain higher level of privileges.

### PAW Scripts

The zip linked above on the technet article have three scripts with three specific purposes

Script | Purpose
--- | ---
Create-pawOU | Set up new OU for tiered administration
Create-pawGroups |  Import groups.csv to create a sample tiered administrator set
Set-PAWOUdelegation | Use the groups above and set the proper delegation on the new OUs created

> Beginning an Active Directory security roadmap is as easy as 1, 2, 3

1. Create OUs
2. Create Groups
3. Push group permissions to OUs

----

## PAW / Admin OU Layout

The privileged organization unit deploy is a bit different mindset for administrators. Understand that the admin OU is created specifically to secure the Active Directory Domain.

** These OUs are deployed by running the `create-pawou.ps1`

#### The security of the domain is paramount

> Security > Everything Else

### Base Organizational Unit Structure

1. The base OUs are created to store all the relative objects.
2. The base OUs are at the top of the domain, the first level of OUs possible to create on the domain


OU Name | Purpose | Created with code
--- | ---
Admin | Holds all tier 0, 1, 2 user and service accounts, groups, and devices that manage those tiers. Also holds tier 0 computers that are not DCs | New-ADOrganizationalUnit -Name "Admin" -Path "$sDSE"
Groups | Holds group objects on the domain. **No tiered** groups here |
New-ADOrganizationalUnit -Name "Groups" -Path "$sDSE"
Tier 1 Servers | Holds app and data servers. **No PAWs** here |
New-ADOrganizationalUnit -Name "Tier 1 Servers" -Path "$sDSE"
Workstations | Holds Domain Joined computers |
New-ADOrganizationalUnit -Name "Workstations" -Path "$sDSE"
User Accounts | Holds regular user accounts |
New-ADOrganizationalUnit -Name "User Accounts" -Path "$sDSE"
Computer Quarantine | This is to be the new default computer OU |
New-ADOrganizationalUnit -Name "Computer Quarantine" -Path "$sDSE"
