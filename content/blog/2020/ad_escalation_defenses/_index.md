---
title: Escalation Defenses AD guardrails every company should deploy
description: These 3 items deployed greatly increase your security footprint.  They are simple.  They are quick.  They are necessary.  They are hardly deployed anywhere
date : 2020-04-23T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory']
aliases:
    - /blog/escalation-defenses-ad-guardrails-every-company-should-deploy
---

The Defenses Every Company Should Deploy:
I call this one "Escalation Defenses: AD Guard Rails Every Company Should Deploy." Due to the recent coronavirus epidemic, I was unable to speak at a cyber security conference in New Hampshire.  The virus cant stop this material from still being pertinent and something every company should deploy in their Active Directory environment, so I chose to put the slides here with some written commentary in hopes everyone who stumbles upon them can find them useful.  

These 3 items deployed greatly increase your security footprint.  They are simple.  They are quick.  They are necessary.  They are hardly deployed anywhere. 


 
With them in place, attackers have a much harder time squirming their way through your domain, and you have some basic security functions in place.  Read through this short list of 3 GPOs and why they are necessary to deploy into every Active Directory domain.

 

active directory security guard rails escalation defenses

 

This is me.  I do things in places and also I play around with Active Directory on the side since my current role is focused on cloud security.  I've been a part of, and managed the responses to multiple domain compromises.  The first time I was a part of one of these events, I was in the hands of some great managers who experienced the events before.  My latest incident I was a part of, I got a call and the person on the other line said, "I need you to do whatever it is that you need to do, so yeah... can you join a call?"

active directory incident response david rowe cissp

This is how you can get in touch with me.  I'm an open network.  Feel free to contact me.

active directory david rowe cissp


 
 

First we start with the basics to level set everyone in the audience.  If we don't know what AD is in the simplest terms, and we don't know why access is critical to the security of the infrastructure, the whole talk is pointless.  This talk is 100% AD.  No cloud based stuff in this one.  That doesn't mean that this doesn't pertain to Active Directories running in any cloud provider!  

 

active directory  today we are covering these topics

 


 
what is active directory 

 

AD is an information store that contains data about things.  

active directory object store information

Users, Group and computers are the common items that are used every day by the workplace laypeople.  This are the three basic items that we need to talk about today and that are effected by the policies laid out in this article. 

active directory hierarchy parent child user groups computers

 


 
People get to do things in the workplace based on their user account and access control lists, security settings.  They can send emails, they can change their passwords.  All this through the ACLs.

They can log into applications and computers through their group membership.    

Because of these ACLs, other people can take action against you and your groups too.  These people who can perform actions are administrators.  These administrator accounts are often the accounts that attackers or bad actors target in their attacks against a company.  The attacker wants privileges so that they can abuse the permissions and obtain an objective.  Whether that is stealing data, shutting down systems, or simply exporting passwords.

active directory  security

Permissions are applied from a top down approach.  The most privileged of the accounts are the Administrators and Domain Administrators.  There are other groups like the Account Operators that can enact change on multiple object types seen in this picture below.  Users, groups, computers and other objects can all be changed by these groups mentioned.  These are the accounts that attackers are after. (More about administrators here)

active directory hierarchy example

 


 
active directory red forest esae

After handling many breaches, Microsoft created a framework to secure privilege identities.  It is called the Enhanced Security Administrative Environment.  (What is ESAE) It includes items such as Red Forest, an administrative forest that isolates credentials.

 

active directory red forest esae

ESAE has a main purpose "Protect Identity systems using a set of buffer zones between full control of the Environment (Tier 0) and the high risk workstation assets that attackers frequently compromise"

What are the tiers, and who holds the keys to the kingdom, has more information on that.

 

ESAE Microsoft active directory 

The first large presentation I could find on the ESAE was done at an RSA conference.  Since my presentation is now a webpage, this is easy to link to: RSA Page 

active directory where did it come from


 
Of that RSA presentation, I took slides 16-19 and made this fancy slide to show the major steps of the framework.  Note that the RSA presentation was given before the fancy term of Red Forest of ESAE.

active directory esae framework

For today's topic, the only things I'm going to dive into are highlighted here: Separate Admin accounts for tiers, and lowering the attack surface of Domain Controllers.  

"Lowering attack surfaces" really means, "stop putting domain admin credentials everywhere" AND "limit number of DAs"

active directory red forest simple steps to start

Here's my brief overview of the Active Directory tiered model.  Putting this into table format this is:

Tier #	Admin Type	Privileges	Objects
0	Domain and Enterprise Admins	Highest level of privilege.  Accounts which have adminstrative control over the entire environment through the ability to manage identity and permissions enterprise-wide	
Domain controllers

Systems that managed DCs

Accounts with Access to these systems

1	Server Admins	Accounts which have administrative control over enterprise resources that server many users or manage business critical data and applications.  These cannot control Tier 0 resources	
Servers 

Server Admins

Enterprise Applications & their admins

Cloud Administrators

2	Workstation Admins	Accounts with administrative privileges over only standard user accounts and single-user devices.  Cannot control Tier 0 or Tier 1 resources	
Helpdesk Support

Devices Support (Field Services)

User Support


 
Attackers start at tier 2, and weasel their way up to tier 0.  At most organizations, there is no technical control deployed separating tier 2 and tier 0.  Attackers often exploit a workstation, and on that workstation there are tier 0 credentials sitting idle on the system waiting to be exported.  Today, I show you the most basic way to separate those credentials and slow an attackers path.

active directory tiered model

Now we get into the process of how an attacker gets from a workstation up to a highly privileged account.

active directory attacker breach path

This is my way of describing how an attacker gets through a system.  Access Path, the way the bad actor snakes through the system up to the highest privileges.

active directory escalation access path


 
Why do we do these tiers that were described before.  If we stop using tier 0 credentials for everything and using them everywhere, its harder for the attacker to gain access to the treasures they are after.   You want to lock the keys that secure the system away.  You dont want to leave them in a glass window on the outside of your shop.

active directory tiers

This is a first example of on thing that I've lived through.  The incident's patient zero was a workstation that got exposed, then broken, then credential escalation occurred.  After the workstation was broken a field service tech logged into the machine.  This credential was also used on servers.  The attacker found his/her way to a server.   The attacker got the other administrator credentials off the server then bounced to other servers until he/she found a server with a domain administrator credential cached and lying idle on the box.  At that point the full domain was compromised.

active directory tiers

This is a second path I've lived through.  Attacker got onto a publicly exposed printer that used insecure LDAP to authenticate to the Domain Controller.  The attacker exploited the printer creds to then get onto computers.  The attacker then got the SCCMapplication account from the workstation and used those creds to jump onto a jump server.  Domain admins also used that jump server.  Creds dump, domain compromised.


 
This attack was performed by a high functioning attacker group.  I cant say there was a single person behind this one, but more likely this was performed by a group of highly trained individuals.

active directory tiers

Attack 3 starts the same way, with an attacker compromising a single computer.  The attacker then finds all accounts on the domain that are kerberoastable. Cracks those credentials and jumps to more machines, then dumping the credentials on those machines.  The attacker continues to do this until they get a Domain Admin account.

active directory tiers


 
All these breaches boil down to the same simple principle for an attacker.  

Workstation or server compromised
Gain admin access on the computer
Credential dump
Find a common local admin password
Attacker traverses laterally across the network
Finds more credentials, slowly escalating
Find Domain admin hash, and dumps credentials
Attacker is now full domain admin. 
active directory tiers breach and prevention

Because the attacker has the same attack path over and over, these paths can be guarded against with very simple steps deployed in any environment.

Let it be known, you will be breached at some point in time.  There will be a computer at some point that will be breached.  The goal is to limit the breach to that one SINGLE computer, and not have the whole domain compromised.

active directory tiers breach and prevention

How do you prevent the exploit of local software that lets an attacker get admin?  Patch.  Its a good practice to deploy.  Just patch the stuff you can patch.

active directory tiers breach and prevention


 
If you deploy a cached credential GPO (pic coming later) you can remove "The attacker dumped the cached credentials on the server

active directory tiers breach and prevention

If you deploy LAPS, which you can copy how its done with BadBlood, you remove the common password used across all the desktop and server computers on your domain.  Here I erased "Finds a local administrator password identical across machines"

active directory tiers breach and prevention

If you deploy a simple block domain administrators from logging into Servers and Workstations GPO, you remove the exposure of the administrative credentials.  Here I removed "Attacker moved laterally across the domain probing servers until he find a computer where a domain administrator credential was stored.

active directory tiers breach and prevention

 

Once again the cached credential GPO fixes something.  This time if an attacker was bouncing around your domain and there were no cached credentials, it would be much harder for the attacker to find a hash to crack and get a password in plain text.  

Although it is hard to crack a hash into plain text, it can be done quickly.  I was a part of one Incident Response where the attacker had dumped the credential and started using plain text password for a 14 character password in under 8 minutes.

Here I removed "Attacker dumps DA hash from machine and cracks it"

active directory tiers breach and prevention

With all safeguards in place, there is no simple easy pathway for the Bad Actor to jump from a workstation to obtain domain administrator credentials.  By deploying the fixes, it is much harder for an attacker to move through the directory.

active directory tiers breach and prevention

Whats this magic sauce to fix your domain so that attackers can't jump from tier 2 to tier 0?  How expensive is it? 


 
active directory tiers gpos

How expensive is it?  It's free.  GPOs are free.  And they are simple.  We start with Blocking logins across tiers.  The MAIN FOCUS here is domain administrators.  Sure we can do things like blocking cross tier access, but if you are not managing the domain administrators properly, there's way too much for you to fix already.  So do yourself a favor and only focus on the heart of your domain; domain admins.

active directory tiers gpos

You can make this GPO in under 5 minutes.  Please focus on the WMI filtering.  This GPO NEEDS that WMI filter applied so that you don't accidentally block domain admins from logging into the domain controllers.  What does this break?  It breaks everywhere on tier 1 and tier 2 where a domain admin is being used.  The simple solution is to start deploying server administrators and workstation administrators.  You should do this before a compromise, and if you really need help doing this know that this can be done in even the most stubborn risk adverse, slow moving environments in under a month.  If you are really aggressive, you can do this and deploy this and all the cached credentials in under an hour.

active directory tiers gpos


 
This is the the big guns, and stops more persistent attacks than you could ever realize.  And once again, its free.  Do this...sometime this month after reading this post.

active directory tiers gpos

What are cached credentials?  The are the stored logons of previous user accounts that logged into the workstations and servers. 

Why do you need them?  If you have people who take computers with them that dont have access to the company's network.  ie: someone with a laptop.  

active directory tiers gpos

Did you know windows defaults the cached credentials to 10?

Do you remember that crappy deployment of RC4 called WEP?  Credentials are stored on the system with the same RC4 hashing algorithms.

active directory tiers gpos

 


 
If you want to play around with local credentials, there are an ample amount of tools to do this.  Here's a short list of free things.

active directory tiers gpos

Back to the blue team stuff.  

Now that you know you never pick up and leave the network with a server in your arms, set this to zero on the servers.  This has no effect to end users using the servers.  As long as your network is on and working, you can authenticate to the servers and do your work.  

This is trivial

active directory tiers gpos

For workstations its another matter.  People need to move around with laptops.  Set this to a maximum of 4 on workstations.

active directory tiers gpos

Now that you know the most simple things to do, do them.  Schedule time to roll these items into test OUs, and understand that by deploying these GPOs, you will be hardening your domain for free.