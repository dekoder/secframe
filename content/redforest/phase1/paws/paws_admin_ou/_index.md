---
title: "Privileged Access Admin OU"
description: "What is the administrative OU?  How to begin with Red Forest PAWs. Patrol privileged credentials"
image: 'images/pawfig2.jpg'
date: 2020-11-01T15:36:24-05:00
weight: 30
pre: ": "
draft: true
tags: ['microsoft','security','redforest','paws']
---

1. Download the PAW script from the technet library. [PAWS OU Creation Script](https://gallery.technet.microsoft.com/Privileged-Access-53a4673a)
1. Create OUs
1. Create Tiered groups using scripts
2. Move people and objects

>  Remember there is much work to do. Eventually focus on Tier 1 and Tier 2 credentials.  For this small moment in time, FIRST make sure Tier 0 is secure as possible.

### Protecting Active Directory and Admin Users and Privileges by creating an administrative OU
 
*CENTRALIZE ADMINISTRATIVE ACCOUNTS:*

The location of the administrative users and groups is often overlooked. If i were to perform an audit on any random domain, there's a major chance that I'd find administrative accounts scattered across multiple Organizational Units. 

> Often by accident this scattered user placement creates pathways that attackers exploit to gain higher level of privileges.

## Get Organized

[http://aka.ms/cyberpaw](http://aka.ms/cyberpaw)