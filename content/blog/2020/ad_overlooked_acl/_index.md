---
title: AD Privilege Escalation Exploit The Overlooked ACL
description: If you don't think you can gain further access into a domain, think again. Use simple permissions to alter the domain and escalate privileges even further
date : 2020-02-19T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory','LAPS']
aliases:
    - /blog/ad-privilege-escalation-the-overlooked-acl
---

![privileged escalation active directory](images/privesc1.png?classes=shadow&width=60pc)
After releasing and running BadBlood, I jumped into my extremely poorly configured test domain. Why were the ACLS so bad? They were randomly generated, and I had no idea the mess I made on this test domain. I believe BadBlood deploys every Active directory ACL that can be exploited, but because the users, groups, and permissions are very randomly generated, I had an odd, yet whimsical, experience with my first domain. 

> This exploit is so, so, so easy
 
An acquaintance who decided to aid and test the very first (and slightly buggy) release of BadBlood mentioned that he wanted to gain expertise at locating LAPS exploits on a domain. A LAPS exploit is finding a computer's local admin password by looking at an attribute that holds that password. 

 

I told him that BadBlood did deploy LAPS misconfigurations. Then I began to tell him if he needed to test a machine and get the password, all he needed to do was… I promptly deleted that second message, wrote down my POC concept and now I have to share the ACL attack path and exploit that I've never seen written about or talked about publically.

 

This exploit is so, so, so, so easy, I honestly cannot believe it is not on the Bloodhound list of ACL attack paths nor was it on the giant list of Active Directory Exploits. If these permissions are on the domain, it opens up a whole new range of ways to craft deeper access into a domain. It's as easy as taking one permission and mixing it with another and "presto!" another attack path!

 

Put two things together to make something great. New ways to scare clients!
 

Previous attack paths are derived attack paths. Paths that exist because a domain is set up in one particular way. If you have access to change how objects on a domain are arranged, how a domain is configured, you can do much more on the domain and find many more places to escalate privileges. Mix changing the domain around with LAPS read permissions and you can get a machine admin password. Pair changes with GPOs that apply RDP access and open RDP the targeted device.  

 

#### The permissions overlooked are "Create/Delete" computer objects to and from a Organizational Units.

> Make the word "Move" synonymous with "Create & Delete" and "Escalation of Privilege"
 

"What? Why?" I hear the audience chanting.

### It's simple...
Known in the Active Directory GUI as create and delete computer objects this is also known as "moving computers around a domain."

Have you ever received that call, "I can't move a server into the right OU!" or "Can I please have access to move this VIPs machine?"


After getting either of these calls, the standard computer admin would allow a person to move computers to alternate OUs. This is done by granting create computer objects and delete computer objects on both the source OU and the destination OU. These permissions are often added by adding full control on computer objects on the two OUs. 

Here are the minimum permissions needed to move a computer between OUs on a domain.

![privileged escalation active directory](images/privesc2.png?classes=shadow&width=60pc)

"But David," I hear you say, "you just said create and delete a computer. I just want to move them!'

 

It's the same permissions. If you can create and delete, you can move objects around. If you can move objects around, you can make the domain more accessible to you. Make the word "Move" synonymous with "Create & Delete" and "Escalation of Privilege"

 
They are the same!

 

## Let's illuminate two scenarios and escalate some privileges.
 

For the following two examples, the user has permissions on the root of the domain to create and delete computer objects anywhere he wants. With these permissions, the user can move computers around the domain at his or her pleasure. (These scenarios are easier if it's explained this way)

 

### Move + Laps Escalation:
The person's permissions include the create/delete computer permissions, but this person only has LAPS read permissions to one OU on the domain.

 

Luckily for him, he can simply move the computer to the appropriate OU using the create/delete computer permissions, then query the LAPS attribute. Presto! Admin password. Then the person moves the computer back to the original OU. Without proper detective work or alerting, you might miss this sleight of hand. 

Proof of concept screenshot: 

1. Computer in OU where user 'lillie_gardner' cannot read admin password
1. Computer is moved into an OU where lillie can read admin password
1. Lillie can read the admin password

 
It's important to note that when a computer is moved, the changes are effective immediately.  This POC shows under 6 seconds the computer was moved and the admin password was readable.

![privileged escalation active directory](images/privesc3.png?classes=shadow&width=60pc)

### Move + RDP escalation:
The person's permissions include the create/delete computer permissions, but the attacker only has access to a user or group that only has RDP access to computers in a single OU on the domain

 

Did you know RDP privileges can be granted or denied via GPOs? Attackers do. This two-step two-layer attack is similar to LAPS because the attacker moves the machine into an OU where RDP privileges are granted via a GPO. The GPO refreshes on the computer, and "presto!" they can log into the machine. *This one sometimes requires a reboot. 

 

I could go on about this attack path, but there's no need to keep beating the dead horse. It's simple. It's straight forward. It's not been talked about enough. Share it. Discuss it. Please. Protect against it. 

 

What about you? Have you ever moved a computer around on the domain so that you could change a setting on it? If so, you have (unknowingly) used this attack.