---
title: Domain Admin Accounts
weight: 100
---

### Why does tier 0 have the least number of administrators?

Tier 0 administrators have the ability to access the ntds.dit file on a domain controller. If someone or something can access to the ntds.dit file, that person or service is a Tier 0 administrator.

> _Any part_ of the environment that has access to control the domain controllers is considered part of Tier 0. 

### what is the ntds.dit file

The ntds.dit file is the data store for all the user names and passwords in an active directory domain. This ntds.dit file is so important because it holds all the users and all the passwords of an organization. 

> Whoever controls this file, controls all the users, all the accounts, all the access to everything inside an organization. 

This file is the one thing that rules the domain. People that control this file can get to any type of data: Research data, PII, trade secrets. The people that can access this file can read every single email inside an organization.

 *The number of people that can access this file needs to be as small as possible. This file needs to be secured.*

 

How to start securing Tier 0:

![Secframe.com Microsoft Tiers](</redforest/phase1/images/Tier 0 Observed Systems.png?classes=shadow>)

Anyone with access to any portion of the ntds.dit file is considered a Tier 0 administrator.

 

Tier 0 Observed Systems
Access to the ntds file can come on many different manners: applications installed the servers, agents or services running on the servers, scheduled task or scheduled jobs, hard disk administrators, backup administrators. The list goes on.

This reference picture is a great place to start understanding how many full domain administrators an organization might have. By focusing on all the separate areas that control this precious file, an administrator can begin to identify areas he or she needs to protect.

 

 
To begin the identification of tier 0 assets, focus on these three subjects:
Applications and services running on DCs
Identity systems that can provision into the various domains
Hardware the domain controllers run on
Once the list of assets is created, investigate each listed system. Make a list of administrators for each.

Examples to identify administrators:

VMware - export the administrator list. Which VMware administrator(s) have access to the domain controller vms.
Provisioning system: provisioning account name. Users with access to the provisioning account
 

I'll be going into more detail about unraveling Tier 0 administration by digging forget into the built in groups in active directory. Stay tuned

 

If you need more assistance with identifying access across your organization, please reach out.

 

 

For the full ESAE series, please begin with: What is Microsoft ESAE

 

For further reading on the admin account series, please visit the following links:

Who needs an admin account?

What is an admin account? (What are the tiers?) This post!

When do you use an admin account? Todo

Where do you put an admin account?

Why do you use an admin account? Todo

How do you use an admin account? Todo

 

Tags: ESAE, Microsoft, Framework, Red Forest


 
Related Articles
Bloodhound in Docker in a Browser. Oh My
(3 min read )
Topics: ESAE, Microsoft, Framework, Red Forest
Administrator Escalation: Creeping to the Top
(3 min read )
Topics: ESAE, Microsoft, Framework, Red Forest

 
Adding a Backdoor to AD in 400 Milliseconds
(3 min read )
Topics: ESAE, Microsoft, Framework, Red Forest
Interested in threat hunting?
BadBlood fills a domain with countless vulnerabilities. Every time it's run your domain is different! Download Badblood here.

Email
Email
Badblood download zip
 
 
© 2020 David Rowe All Rights Reserved david@secframe.com
