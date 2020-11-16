---
title: Tiered Administration
weight: 12
pre: ": "
aliases: 
    - /blog/what-are-tiers-and-who-holds-the-keys-to-the-kingdom
---


### What are tiers?

Microsoft has a methodology called Tiered Administration. Microsoft Services assists many companies across the globe respond to security breaches and incidents. The best and the brightest of various Microsoft teams discussed how companies were being breached, and how Microsoft designs solutions to secure post incidents. 
 
To respond to incidents, Microsoft created Tiers: a method to segment an Active directory domain to protect the privileged identities. Start securing your organization, starts by segmenting users from administrators. 
 
 
In the post [Administrative Accounts](/redforest/phase1/adminaccounts/) best practices for administrator accounts is  described in detail. After understanding the process of when a person gets a separate administrative account, understand what resources people access. for that we...

#### Dive into Tiers

There are different levels of administrator access.  Administrators have different accounts based on the resources they access.

## The Tiers
Microsoft calls this administrative separation "Tiers." Full documentation on Microsoft tiers requires a bit of in depth reading

The numbers of the tiers are parallel to the [Trusted Computing Base "Protected Ring" security model.](https://en.wikipedia.org/wiki/Trusted_computing_base)
 


![ESAE trusted computing base protected ring](</redforest/phase1/images/TCB Diagram.png?classes=shadow&?width=40pc>)

### There Are Three Tiers For Administrators; Tier 0, Tier 1, and Tier 2

For a very brief pictorial definition of Microsoft tiers, see the image below:

![Secframe.com Microsoft Tiered Model Guidelines](</redforest/phase1/images/Secframe.com Microsoft Tiered Model Guidelines.jpg?classes=shadow&width=40pc>)
 

Trusted Sec Levels Mapped to Microsoft Tiers:
 

| Ring / Tier #	| Level of Trust | Trusted Sec Definition | Eli5 Definition |
| ------------- | -------------- | ---------------------- | --------------- |
| 0 | Most Trusted |	Kernel |	Domain Controllers |
| 1 |Contains Non Trusted Items	| System Services |	Servers |
| 2 | Contains more non trusted	| I/O Drivers & Operations - Utilities |	Workstations |
| 3 | Least Trusted	 | 	Applications | Anything Public, Standard Users |
 

 As the journey from the outside ring into the center, also from tier 2 down to tier 0, the security level increases.  With this increase of security, the number of people with access to the resources decreases. Tier 0, and the kernel ring, has the least amount of administrators in the Active Directory environment.

## The Journey to Tiers

Since there are three administrator tiers, begin with understanding there is three sets of administrators to add into a domain. 

| Tier # | Administrator Type |
| ---: | :--- |
| 0 | [Domain Admin](domainadmins) |
| 1 | [Server Admin](serveradmins) |
| 2 | [Workstation Admin](workstationadmins) |


To get started with deploying the tiers, start with the biggest most impactful item that secures the largest section of the domain, and as a bonus, it takes the least amount of time: [Tier 0; The Domain Administrators](domainadmin)
