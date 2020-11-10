---
title: Administrator Escalation - Creeping to the Top
description: You can have all the permissions of a group without ever being a member of the group. You can do this without showing up in the group member list.
date : 2020-02-19T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory']
aliases:
    - /blog/administrator-escalation-creeping-to-the-top
---

Did you know the list of people in an organization that can modify Domain Admins and Administrators is larger than just the Administrators and and DAs? This post lets you in on the simple secret of who can modify and attack a domain by exploiting a Tier 0 backdoor.

JANE-SidHistoryShown 2

 
```
You can have all the permissions of a group without ever being a member of the group. You can do this without showing up in the group member list. Auditors, blue teamers, red teamers and the like often miss persistent attackers by looking at each group and listing what is inside of that group.

Anyone that controls the active directory database, the ntds.dit file can attack and take ownership of an Active Directory domain.

An attack targeting the NTDS file is a Tier 0 attack.(what the heck is tier 0? ) People with the following permissions can play out these attacks:

Domain admins
Storage admins
Virtual Infrastructure Admins
Cloud Admins
Software update admins - think SCCM admins
Tier 0 Observed Systems

 
The Escalation: A story of administrators
Scene set to an office with a pair of keys that unlock an ancient door.
In the corner of your office sits a red door. Rounded at the top. Archaic patterns dance around the sides designed to bring the owner good luck. The door comes from a time where doors were made to look like mythical animals. It almost looks alive, and when you aren't looking this door looks like it is moving. It's solid metal. On the right side of the door, a small square keyhole.
The auditor wipes the powdery white frosting from his index finger and points. "That door right there. How many copies of the keys do you have? One for you and how many more"
 
Here the auditor is describing the members of the Domain Admins and Administrators.
 
Your fingers fumbles along your ancient key. Its pattern designed perfectly. The teeth jagged, reminding you of the dragon that the door resembles. Freely, you hand over the list containing the copies of the keys. The auditor looks at the list with a glance so quick it barely registers in your memory.
"Great! Thanks for that." The auditor says. "I'm impressed. Two! Only two copies. You're the best. Let me write that down. Two people in charge of two keys!"
 

In the domain administrator group, only two people. Two people in charge of the domain.
 
The auditor scribbles his findings down on his paper and circles it. Stars it. Puts a smiley right next to it. He leaves. You pass the test.
When he leaves you walk to the office down the hall. "Hey Jim! We passed again. The count is still low, the door is safe!" You open the drawer at the bottom of his desk, place the keys in the drawer, you turn and leave.
 

You placed the keys in their storage compartment. Just like a key has a place where it is stored, a file has a storage location on a hard drive.  
 

Jim watches you leave, unlocks then slides open the bottom drawer of his desk. It moans and creaks as the rails groan to open. Jim reaches to the back checking the keys are still there. They are. He gives a thumbs up to the rest of his team. The nod in recognition.
Jim is a storage admin, or he is a password vault admin. Whichever group he is in, he and his three teammates share this desk. They all have access to the keys.
 

Two people in charge of two keys. Four people in charge of the storage of those keys. Now there are 6 people in control of the keys. Six people in charge of the domain...
 
You walk over to Bethany's desk. "Hey Bethany! It's about time for the quarterly maintenance on the door. Any chance you can clean the locks this week?"
"Of course!" She replies. "You want to clean it up on 'Patch Tuesday' like usual?"
"That's perfect!" Your excitement shows in your smile, "Thank you for always doing this without me!"
 
Bethany's role is like a software update manager. She can control anything that happens to the server, or anything that happens to that door. She has access to push any software to a server. She can update anything on that door, including the lock. She doesn't have a key, but she can see every small detail on that lock and knows how it works. She can change or update the lock if she wants. She too can open that door.
 

Bethany walks over to her intern. This intern skipped the 'Learning to Code Securely' class in college. He is itching to help out and today is his first major upgrade. Bethany confirms with the intern that he is ready to install the updates today.
Bethany's intern, and the rest of her team, have access to update the door and the key. There are 8 members of this team.
 

Two people in charge of two keys. Four people in charge of the storage of those keys. Eight people in charge of the updates of those keys. Two plus four plus eight is now 14 people in charge of the management of that door and it's lock.
Fourteen people in charge of the domain. Yet, there are only two keys.
 
As the number of people in charge of the management of a system increases, so too does the number of people who have pathways to control the keys.
Jim leaves his office and locks up for the night.  As he turns around, he sees the company's CEO.
"Goodnight Anna.  Have a good night!"
"Night Jim.  I'll be following you out tonight.  I just have to lock a few doors people left open."  Anna takes her master key, and locks the doors to the offices. She slides the key into her purse, then leaves with Jim.
 

Fifteen people...
```

These scenarios can continue to be expanded upon to show other areas where administrators have access to the keys and the lock. The important thing to realize is that everyone with access to the Domain Controller, AND the software that manages that server, AND the hardware that manages the server, all those individuals have access to manage the domain controller.

In this persistence attack series, I toy with tattooing a couple of highly privileged groups on a standard user account. I'll play around with offline access or file level access to the ntds.dit file, the database used by Active Directory to store user information.

SidHistory-SuccessWithDSInternals - right sids

Stay tuned for attack demonstrations on the Ntds file, and how the access to this one tiny file can be detrimental to the security of Active Directory.