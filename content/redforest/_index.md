---
title: Red Forest
weight: 5
pre: "<b>1. </b>"
chapter: true
---
### Chapter 1

# What is Microsoft's ESAE and Red Forest?

## The Enhanced Security Administrative Environment

ESAE is [Microsoft’s complete framework](https://social.technet.microsoft.com/wiki/contents/articles/37509.active-directory-red-forest-design-aka-enhanced-security-administrative-environment-esae.aspx) to protect Active Directory (AD). AD, in short, is the identity and access management tool in your business network that holds passwords, credentials, users, computers, groups. AD controls your access to resources across your network; 

| Information and Applications | Privileged Credentials | Resources and Servers |
| :----------------------------: | :----------------------: | :---------------------: |


{{% notice info %}}
The conference presentation that I could find that talks about ESAE, is from an RSA conference from February in 2017 [Critical Hygiene for Preventing Major Breaches](https://www.rsaconference.com/usa/us-2017/agenda/critical-hygiene-for-preventing-major-breaches) ::: [Presentation PDF download](https://published-prd.lanyonevents.com/published/rsaus17/sessionsFiles/3774/CXO-F02-Critical-Hygiene-for-Preventing-Major-Breaches.pdf)
{{% /notice %}}

Deploying ESAE is a foundation for a long term identity security solution.  Below is a timeline provided by Microsoft to deploy this security structure.

### Information

We define information as can be as anything that holds data such as emails in your organization, research data, credit card numbers, trade secrets. Information is often chopped up into different classifications depending on its importance: Unclassified, Secret, and Top Secret.  Often the Secret and Top Secret information is the information attackers want so they can monetize their attack. 

### Privileged Credentials

The term 'privileged credentials' is often used to talk about the user accounts, service accounts, or administrators in your environment.  These people and processes often have access to sensitive information.  Attackers often specifically target these privileged administrative credentials to gain access to the confidential data. 

### Resources and Servers

One of ESAE's aim is to secure the data that is on your network.  By securing the locations where the information is stored, file shares, servers, workstations, a company makes it hard for attackers become successful.  

{{% notice tip %}} 
The ESAE framework outlines several quick wins, as well as a number of long term plans to secure privileged credentials. With these privileged credentials secure, attackers are less likely to move laterally and vertically through your network
{{% /notice %}}

Microsoft open-sourced much of the documentation for deploying the architecture. The documentation dispersed across TechNet, GitHub, YouTube videos, and other media. Piecing together the documentation can be a bit of a hassle.  The goal of this page and its structure is to put the main building blocks of the framework into an easily deployable structure.

{{% notice info %}}
The basic outline for starting the deployment buckets the security items into sections via deployment time. The three buckets outlined are; The First 30 days, 90 days, and Beyond 90 days.
{{% /notice %}}

---
## ESAE Timeline
### [The First 30 Days](phase1)
![ESAE Phase 1 zero risk of operational downtime](</redforest/images/First30Days.png?classes=shadow>)

### [90 Days](phase2)
![ESAE Phase 2 single investments leads to significant positive impact](</redforest/images/First 90 Days.png?classes=shadow>)

### [Beyond 90 Days](phase3)
![ESAE Phase 3 build meaningful active directory security resilience from long term threats](</redforest/images/Beyond 90.png?classes=shadow>)

---
#### Starting Microsoft's Phased Approach

---
### Where to begin?
---

| I'd like to begin the journey. Let's get started with phase 1. |
| :---: |
| [Take me to phase 1](phase1)|

---

| I've already done some admin work and want to know what to do next. Let's get started with phase 2. |
| :---: |
| [Take me to phase 2](phase2) |

---

| Time for an investment! let's bring out the super secure stuff. |
| :---: |
| [Take me to phase 3](phase3) |

[![Roadmap to ESAE](</redforest/images/Roadmap First 30 and 90 Days.png?classes=shadow>)](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/securing-privileged-access)
