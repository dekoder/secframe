---
title: A SIDHistory Attack - Marching onto a DC
description: A Sidhistory Active Directory attack unveiled and revealed.  How do attackers gain access using this well hidden attribute?  How quick is this attack?
date : 2020-02-27T03:06:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory']
aliases:
    - /blog/a-sidhistory-attack-marching-onto-a-dc
---
I'll walk you through a sidhistory attack in Active Directory. An attack where a standard user account, not in any administrative group, can log into a domain controller.  This account evades any check that a big 4 auditor would use, yet still has all the access of a Domain Administrator. 

 

JaneOnTheDC (2)

This attack can be done by anyone with access to the NTDS.dit file. My Tiers post and Administrator Escalation posts go into deep detail on who can attack your domain by using the attack outlined in this post. Domain admins are not the only users with access to this file. Any person with access to the underlying hardware that contains the hard drive can attack using this method. Any person who can write to the Domain Controller's file system, has access. These people have access to modify the Active Directory NTDS file. If the NTDS.dit file is compromised, your domain is considered breached.


 
Any disgruntled employee of a managed services provider hosting your infrastructure on their ‘cloud’ or datacenter can perform this attack. Even the storage admin of your server's hard drives can manipulate this file. Anyone with access to the hard drive. Sometimes Unix admins can edit this file.

Now that it's clear that the list of administrators that can manage this file is larger than just the Domain Admins, I'll take you through the first attack that abuses the SIDHistory attribute.

 
The SIDHistory attack objective
1. Add a user to the “Administrators” and “Domain Admins” groups in AD.

2.Go undetected when looking at the two groups by not showing up on the ‘members’ list, NOR does the user show to be a ‘memberof’ those groups.

 

This is a shadow admin situation. A domain admin, that can do all Administrator functions, but doesn't show up on any auditing lists. For this example I have to import DSInternals powershell module. Git Link to Dsinternals here. These tools are provided by Michael Grafnetter and make it easy beyond easy to attack an offline NTDS.dit file.


 
 

Attack Steps:
1. First Install DSInternals Powershell module
Install-Module -Name DSInternals
DSInternals Install

What does this do? This installs the code needed to inject the sidhistory into the Active Directory database. These tools written by Michael Grafnetter are fantastic and make playing with SIDHistory child's play.

 

2. Get a random AD user to play with
 $user=(get-aduser -f *)|get-random
Get a random person

Jane Conway looks perfect for this post.  I picked a random user so that I pick a non-Domain admin, and proving any user can be targeted for this attack.


 
 

3. Get Administrator SID and Domain Admin Sid.
$admins = get-adgroup administrators

$domainadmins = get-adgroup “domain admins”.
AdminGroupand DA Group

The SID of an Active Directory domain is unique. I want to make sure that I sneak the right groups SID onto the user. If the SID doesn't match a group with permissions, this attack is useless.

 

4. Attempt to write to the active and running NTDS server… which fails.
Locked NTDS.dit

This attack I'm attempting to do a simple injection of a privilege group onto a user. Unfortunately for attackers, this attack must be done with an offline ntds.dit file.  Fortunately for defender, an attacker has to do this to a NTDS database that is not online.

Fun Fact: If an attacker writes to an offline for file and later brings it back online, the sidhistory and all attributes modified to that offline for file do replicate to all the other domain controllers.

 
 
5. Make ntds.dit writable & Inject domain admin onto the sidhistory attribute on the user.
SidHistory-SuccessWithDSInternals - right sids-1

For this POC, I did a straightforward approach: Disable Active Directory, and write to the file. It is possible for anyone with access to the server and hard drive to run this attack.

Stop AD
Inject SIDHISTORY
Start AD.
Stop-service NTDS -force

Add-ADDBSidHistory -samaccountname JANE_CONWAY -sidhistory $domainadmins.SID, $admins.sid -DBPath c:\windows\ntds\ntds.dit

Start-service NTDS
 

6. Check Jane’s sidhistory for values:
JANE-SidHistoryShown 2

After turning back on ADDS, if I pull the information for user Jane_Conway, I can see she has some information in SIDHistory. I made the picture here as clear as possible, The Domain Admin sid is pointing to the appropriate value on Jane, and the Administrator SID is pointing to the value. The administrators and domain admins SIDs are added to her user account on the SIDHistory attribute.  Step 5 proved to be successful.

 

 

7. Time to pull the group membership of the Administrators and Domain Admins groups:
Administrators here… you can see that Jane Conway is not listed

null
 


 
Domain Admins group here… you can see that Jane Conway is not listed here either.  

domain admins members - no jane conway

It turns out that Jane is neither a member of the Administrators group, or the Domain Administrator groups.  Next step is to see what she can do with only the SIDHistory property modified on her user account. 

 

8. Check Jane’s account for group memberships
 

JANE - Member of Not a member of DAs

Looking at Jane’s Group membership, you can see that she is not a member of either the Administrators group or the Domain Administrators group. But didnt I add this user to the groups? No, and yes. But mostly no. I didnt modify her 'memberof' attribute, nor did I modify the 'members' attribute on the privileged groups

 
9. Log into domain controller with Jane Conway
The non-administrator and non-domain admin should not be able to do this.  She is not a member of any of the groups listed that have access to this server.

Hostname - reports BadBlood-dc1 - my domain controller.

Whoami reports back Jane Conway - me

Get-addomaincontroller - reports badblood-dc1 - Proves I logged onto the domain controller

JaneOnTheDC (2)
10.  How did I get onto this DC?
Whoami /groups provides the result of the membership of Jane's User account:


 
whoami groups - admins - administrators-domain admins- dns admins 2

Once into the server, if I do a quick check on my user and group membership there are two very privileged groups added to this user. BUILTIN\Administrators and BADBLOOD\Domain Admins are both added to this user.

 

If you are wondering how quick it is to perform this type of attack: Just under 7 seconds.

TotalTimeTosetSIDHistory

 

Before the release of Bloodhound 3.0 the only way to report on this was to query all users for this exploit.  This query provided by Sean Metcalf was the fastest way to search and audit for this persistence attack  Sneaky AD persistence: SIDHistory

# Detect Same Domain SID History

[string]$DomainSID = ( (Get-ADDomain).DomainSID.Value )

Get-ADUser -Filter {SIDHistory -Like "*"} -Properties SIDHistory |where {$_.Sidhistory -like "$DomainSID-*"}


Thanks to Bloodhound 3.0  we can now see the sid history relations.  I'll do a later post on the sidhistory exploits in bloodhound a bit later.  If you made it this far, please subscribe or follow me on twitter.  My linkedin is an open network as well. Feel free to connect with me there!