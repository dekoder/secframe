---
title: How to Secure Tier 0
weight: 100
pre: ":: "
---
<!-- split into separate page all that is below -->
## How to start securing Tier 0:

Take a look at a sample of systems that may be defined as Tier 0 systems.

![Secframe.com Microsoft Tiers](</redforest/phase1/images/Tier 0 Observed Systems.png?classes=shadow&width=60pc>)

### Identify Tier 0 Systems
To begin the identification of Tier 0 assets, focus on these three subjects:
1. Applications and services running on DCs
2. Identity systems that can provision objects (users, computers, groups) into the various domains
3. Hardware the Active Directory Domain controllers run on (physical, virtual, cloud)

Once the list of assets is created, investigate each listed system. Make a current list of administrators for each. 

Examples to identify administrators:

VMware - export the administrator list. Which VMware administrator(s) have access to the domain controller vms.
Provisioning system: provisioning account name. Users with access to the provisioning account

If you need more assistance with identifying access across your organization, please reach out.