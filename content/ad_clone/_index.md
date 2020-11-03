---
title: Active Directory Cloning
weight: 30
pre: "<b>3. </b>"
chapter: false
tags: ["Active Directory","Microsoft","Testing"]
---

## Building a test environment is crucial

Without a test environment, changes rolled into production can have a detrimental effect on the production environment.  With SecFrame's AD-Clone service a clone of a production Active Directory domain is completed a few clicks.
![Active directory test environment](images/adclone.png?height=20pc)


### Key items gained with AD-Clone:
- Development of any tools and see how they act in your environment
- Testing against production like AD environment
 


### Permissions required for this service:
- No need for a privileged account on the source domain
- Read Only domain user account.  In many environments, this is often a standard domain user account
{{% notice info %}}
Passwords are not copied across environments 
{{% /notice %}}
 

### Permissions Required On the Target/Testing domain:
- A domain administrator account.
{{% notice info %}}
Full domain administration is needed because you are building a complete new structure: copies of the OUs, users, groups. You are creating a new domain.
{{% /notice %}}
 
### With this service you get a method to repeat copy a production domain into a test domain 
1. You provide access to the empty target domain
2. I connect the domains and prepare them for cloning
3. Choose object types to copy
4. Sit back and let the the process work
 
## Once completed you will have a functioning test environment.
| Use Cases | Description |
| :---: | :--- |
| Sales POCs | Perform POCs for a customer on a copy of their live environment. No need to change a target customer's production environment. No need to wait for change controls to deploy your software. |
| Dev/Ops | Run through full code development lifecycle in a prerelease environment before rolling into production.Make changes to large batches of users and groups without fear of affecting end users |
| Architecture and Engineering  | Test every idea you have. Deploy changes and find issues before the change control process. |
| SDLC Testing | This service is perfect for any ITIL or organization that follows an SDLC. Organizations large or small. |
 

 