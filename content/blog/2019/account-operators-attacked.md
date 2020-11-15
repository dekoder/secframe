---
title: "5 ways Attackers Exploit Account Operators"
date: 2020-01-01T16:47:17-04:00
draft: false
aliases: 
    -  /blog/account-operators
    -  /blog/account-operators/
---


Using builtin groups is a practice often used by organizations using Microsoft's Active Directory. One of these groups, the Account Operators, is commonly used for basic user administration. As an organization grows the dangers of using this group grows. If the use of the account operators continues without review and governance, security gaps are added to the organization.

Upon completion of Active Directory Gap Analyses, I've notice businesses often use Account Operators as the easy way to delegate the management of users and groups. When there is a need for a non technical person to manage groups, the person often gets added to this group. Once added to the group he or she then has access to manage users and groups across the domain.

![Account Operators Can Manage (1)](/blog/2019/images/accountopsmanage.png?classes=shadow&width=40pc)

I'm going to demonstrate a few scenarios where attackers exploit permissions once they gain control of an account that's a member of this group.

The Account Operators group has extensive permissions across the domain. The group has every right described in my other post, "Who needs an administrator account?". They also have access to all the users, groups, permissions outlined here (schema post)

I'll journey through a few of the permissions listed in the previous articles. I'll demonstrate just how quick, and simple it is for an attacker to jump through the directory once they control this group.  It's important to note that attackers aren't always after full Domain Administrative control, and sometimes are only after information on servers or workstations.

 

### Scenario 1: Privilege escalation through "change user passwords."
This is the typical scenario Microsoft described with the lateral movement attacks. The attacker steps consist of:

1. Dump acl and computer permissions on a domain. (Bloodhound or acldump)
2. Find a workstation or server to target for an attack.
3. Change password of a user with access to the targeted - computer.
4. Rdp to be computer with the new compromised account
5. Dump credentials on computer.
6. Continue further lateral movement
 

### Scenario 2: Self escalation of privileges

1. Find computer to target.
1. Find the administrative group that can administer the computer.
1. Add self to administrator group identified.
1. Account now has admin access to rdp or smb or any other 1. Administrative function in the computer
 

### Scenario 3: Change owner on a Group - Persistent attack:
1. Attacker finds a target group.
1. The attacker changes the security on the group so that their account is the "owner."
1. The attacker sits and waits.
1. When the opportune time comes, Saturday 2:45a.m., the attacker escalates privileges on their account by adding their account to the group.
1. The attacker gets the information they want
1. Attacker removes their account from the group
1. The attacker again sits and waits to remain hidden.
 

## Lesser known paths
 
### Scenario 4: LAPS exploitation and full ownership of all computers.
> I.e. full local admin access to all machines on the domain

1. Account Operators has control of all attributes on "user" class objects
1. "Computer" class object is a sub class of user - see account operators and classes post
1. Laps attribute is mapped onto computer objects.
1. Attacker has inherited rights to LAPS and all computers on the domain
 

### Scenario 5: Allow log on locally to domain controllers. i.e. a default AD GPO configuration
1. Attacker finds a hypervisor admin account
1. Attacker logs into hypervisor
1. Attacker locates a domain controller running on a hypervisor.
1. Attacker uses the compromised account operator account to log into the domain controller.

Sean Metcalf has a fantasic presentation on the GPO exploitation.  This video starts at 30:15, right where the Account Operators and GPO permissions are listed.

 {{< youtube git1a6cu048 >}}


These five scenarios demonstrate why the Microsoft recommendation for Account Operators is to "Leave it empty." My script, [Github Account Operators Cleanup](https://github.com/davidprowe/AD_Sec_Tools/tree/master/AD_User_AccountOpsCleanup), to empty the account operators group and the full guide will be available shortly.