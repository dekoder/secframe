---
title: Chrome Critical Patches and Microsoft Tiers
date: 2020-11-16T16:47:17-04:00
tags: ['Chrome', 'Tiers','redforest']
images:
    - /images/featured.jpg
---

[CVE-2020-16013](https://us-cert.cisa.gov/ncas/current-activity/2020/11/12/google-releases-security-updates-chrome) is a threat that can quickly escalate into major domain interruption. Without proper tiered segmentation on an Active Directory domain, the exploit contained in this CVE can quickly change into a full domain compromise.

> For a brief overview of why privileged access management is critical to an environment, please see [What is RedForest](/redforest)

## CVE-2020-16013

### Description

The vulnerability allows a remote attacker to compromise the affected system.

The vulnerability exists due to incorrect implementation in V8 in Google Chrome. A remote attacker can create a specially crafted web page, trick the victim into visiting it and compromise the system.

Note, the vulnerability is being actively exploited in the wild.

----

The key item to take note of here is the portion: 
> A remote attacker can create a specially crafted web page, trick the victim into visiting it and compromise the system.

## Recommended steps for mitigating this threat

- Update of Chrome browsers on servers and workstations.
- Remove access to public web sites on Domain Controllers
- Remove cached credentials from all servers 
- Minimize cached credentials on workstations
