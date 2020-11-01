---
title: Tiered Administration
weight: 12
---


### What are tiers?
 
 
In the post [Administrative Accounts](/redforest/phase1/adminaccounts/) best practices for administrator accounts is  described in detail. After understanding the process of when a person gets a separate administrative account, "What type of resources do administrators have access to?" needs to be answered.


 Microsoft has a methodology called Tiered Administration. Microsoft Services assists many companies across the globe respond to security breaches and incidents. The best and the brightest of the various cross industry Microsoft teams discussed how companies were being breached, and how Microsoft designs solutions to secure post incidents. 
 
A major process to start securing your organization, starts by segmenting users from administrators. 

#### Segmentation doesn't stop there

There are different levels of administrator access.  Administrators have different accounts based on the resources they access.

## The Tiers
Microsoft calls this administrative separation "Tiers." Full documentation on Microsoft tiers requires a bit of in depth reading

The numbers of the tiers are parallel to the [Trusted Computing Base "Protected Ring" security model.](https://en.wikipedia.org/wiki/Trusted_computing_base)
 

secframe.com TCB Diagram
TCB Diagram.png
 
![ESAE trusted computing base protected ring](</redforest/phase1/images/TCB Diagram.png?classes=shadow>)

### There are three tiers; Tier 0, Tier 1, and Tier 2

For a beginners definition of Microsoft tiers, see the image below:

![Secframe.com Microsoft Tiered Model Guidelines](</redforest/phase1/images/Secframe.com Microsoft Tiered Model Guidelines.jpg?classes=shadow>)
 

Trusted Sec Levels Mapped to Microsoft Tiers:
 

| Ring / Tier #	| Level of Trust | Trusted Sec Definition | Eli5 Definition |
| ------------- | -------------- | ---------------------- | --------------- |
| 0 | Most Trusted |	Kernel |	Domain Controllers |
| 1 |Contains Non Trusted Items	| System Services |	Servers |
| 2 | Contains more non trusted	| I/O Drivers & Operations - Utilities |	Workstations |
| 3 | Least Trusted	 | 	Applications | Anything Public |
 

 As the journey from the outside ring into the center, or from tier 2 down to tier 0, the security level increases.  With this increase of security, the number of people with access to each tier decreases. Tier 0, and the kernel ring, should have the least amount of administrators in the Active Directory environment.

 

Why does tier 0 have the least number of administrators?
Any part of the environment that has access to control the domain controllers is considered part of Tier 0. If someone or something can access to the ntds.dit file, (password hash database), that person or service is a Tier 0 administrator.

This ntds.dit file is so important because it holds all the users and all the passwords of an organization. Whoever controls this file, controls all the users, all the accounts, all the access to everything inside an organization. These people who can control this file can get to any type of data: Research data, PII, trade secrets. The people that can access this file can read every single email inside an organization.

 The number of people that can access this file needs to be as small as possible. This file needs to be secured.

 

How to start securing Tier 0:
Use the following picture to help start the identification process to identify all Tier 0 systems. Anyone with access to any portion of the ntds.dit file is considered a Tier 0 administrator.

 

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
