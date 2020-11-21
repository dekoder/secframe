---
title: "Administrator Management"
weight:  200
chapter: true
description: "Where do you start with securing active directory? Security starts with an admin OU."
pre: "- Bonus Content: "
tags: ["ESAE", "Microsoft","Active Directory","Security"]
aliases: 
    - /blog/why-arent-your-administrators-in-one-place/
---

With a basic understanding of the various [Administrative Accounts](adminaccounts), [Microsoft Tiers](tiers), it's time to look into where do these accounts reside in an Active Directory Domain once these accounts are made.-


#### Why aren't your administrators in one place?

### Protecting Active Directory and Admin Users and Privileges by creating an administrative OU
 
*CENTRALIZE ADMINISTRATIVE ACCOUNTS:*

The location of the administrative users and groups is often overlooked. If i were to perform an audit on any random domain, there's a major chance that I'd find administrative accounts scattered across multiple Organizational Units. 

> Often by accident this scattered user placement creates pathways that attackers exploit to gain higher level of privileges.

## Get Organized
<!-- i think each one of these below is good enough topic for a post -->
Top items for managing administrators:
- Where do the administrative accounts go?
- Who are the administrators?
- How do you standardize the accounts?
- How can to remain in good practice?
 
By managing these four administrative account items, you can be certain you know who is an administrator in a domain, and you will know what they can do on the domain. 
Microsoft open sourced a lot of the information on privileged account management already.

### Get Started With Admin User Management
1. [Admin Location Management](admin_ou)
2. [Admin Reporting](admin_reports)
3. [Admin Account Object Standards](admin_standards)
4. [Admin Maintenance](admin_maintenance)

{{< parentsection section="redforest" >}}
