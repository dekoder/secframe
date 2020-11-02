---
title: Domain Admin Accounts
weight: 25

pre: "- "
---
## Domain Administrators
#### Tier 0

<!-- need some more stuff on cleaning up domain admins 
start with active directory domain admin accounts and cleanup before driving into tiered stuff
-->
The smallest circle of administrators on a domain, these accounts are the most vital in an organization. They contain the permissions required to view all the user passwords. 

> Guard with ferocity

### Why does tier 0 have the least number of administrators?

Tier 0 administrators have the ability to access the ntds.dit file on a [Domain Controller](https://en.m.wikipedia.org/wiki/Domain_controller_(Windows)). If someone or something can access to the ntds.dit file, that person or service is considered a Tier 0 administrator.

> _Any part_ of the environment that has access to control the domain controllers is considered part of Tier 0. 

### what is the ntds.dit file

The ntds.dit file is the data store for all the user names and passwords in an active directory domain. This ntds.dit file is so important because it holds all the users and all the passwords of an organization. 

> Whoever controls this file, controls all the users, all the accounts, all the access to everything inside an organization. 

This file is the one thing that rules the domain. People that control this file can get to any type of data: Research data, PII, trade secrets. The people that can access this file can read every single email inside an organization.

 *The number of people that can access this file needs to be as small as possible. This file needs to be secured.*

 
<!-- split into separate page all that is below -->
## How to start securing Tier 0:

Take a look at a sample of systems that may be defined as Tier 0 systems.

![Secframe.com Microsoft Tiers](</redforest/phase1/images/Tier 0 Observed Systems.png?classes=shadow>)


### Access to Observed Systems
Access to the ntds file can come on many different manners: applications installed the servers, agents or services running on the servers, scheduled task or scheduled jobs, hard disk administrators, backup administrators. The list goes on.

The reference picture above is a great place to start to understand how many Tier 0 administrators, _not just domain administrators_, an organization might have. By focusing on all the separate areas that control this precious file, an organization can begin to identify areas he or she needs to protect.

### Identify Tier 0 Systems
Just like in the picture above, to begin the identification of Tier 0 assets, focus on these three subjects:
1. Applications and services running on DCs
2. Identity systems that can provision objects (users, computers, groups) into the various domains
3. Hardware the Active Directory Domain controllers run on (physical, virtual, cloud)

Once the list of assets is created, investigate each listed system. Make a current list of administrators for each. 

Examples to identify administrators:

VMware - export the administrator list. Which VMware administrator(s) have access to the domain controller vms.
Provisioning system: provisioning account name. Users with access to the provisioning account

If you need more assistance with identifying access across your organization, please reach out.
