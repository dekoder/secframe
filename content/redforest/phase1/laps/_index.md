+++
title = "LAPS Servers & Workstations"
description = "Microsoft's solution to remedy password reuse on the local admin account for computers in a domain"
pre = "3. "
date = 2020-11-03T07:36:24-05:00
weight = 90
+++

### what is LAPS?
## Local Admin Password Solution

Laps is Microsofts deployment to remedy the issue of password reuse on local administrator accounts. 

The base for LAPs is based from a github repo [Admpwd](https://github.com/GreyCorbel/admpwd). 

### What happens when comouters have he same local password

Attackers love easy paths through a network or domain. Attackers dont use unique attacks. they repeat the eay they come into a network. They repeat the way they move through a network. 

![Phase 1 critical mitigation]

Creating unique and complex local passwords for computers, and rotating those password hinders an attached movement

### A Typical 24 hour attack
1. Beachhead (Phishing Attack, etc.)
2. Lateral Movement
   1. Steal Credentials
   1. Compromise more hosts & 
credentials
3. Privilege Escalation
   1. Get Domain Admin credentials
4. Execute Attacker Mission
   1. Steal data, destroy systems, etc.
   1. Persist Presence

> source: [Critical Hygiene for Preventing Major Breaches](https://www.rsaconference.com/usa/us-2017/agenda/critical-hygiene-for-preventing-major-breaches)

Item #2 is what LAPS targets to remediate. The compromising of an entire computer system because of common passwords.

## LAPS Free or Open Source Tools

| Tool Name | Download URL | Secframe Quick Deploy Guide |
| --- | --- | --- |
| Microsoft LAPS | [Microsoft LAPS](https://www.microsoft.com/en-us/download/details.aspx?id=46899) | [Install and Configure LAPS](install_laps/)
|  AdmPwd | [AdmPwd project](https://github.com/GreyCorbel/admpwd) | Not yet Written |
| AdmPwd.E | [AdmPwd.E](https://github.com/GreyCorbel/admpwd-e) | Not yet Written |

