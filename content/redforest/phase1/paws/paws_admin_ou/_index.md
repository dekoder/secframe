---
title: "Tiered OUs and Admin OU"
description: "What is the administrative OU?  How to begin with Red Forest PAWs. Patrol privileged credentials"
image: 'images/pawfig2.jpg'
date: 2020-11-01T15:36:24-05:00
weight: 30
pre: ": "
draft: true
tags: ['microsoft','security','redforest','paws',"Active Directory"]
disableMermaid: false
---

## Begin work towards the ESAE

With a base understanding of tiers, administrators, unique passwords for computers and a brief definition of why a privileged access workstation is needed for Active Directory Security, it's time to start investing time into making changes in the environment. 

### Prerequisites

1. Download the PAW script from the technet library. [PAWS OU Creation Script](https://gallery.technet.microsoft.com/Privileged-Access-53a4673a)

### PAW Scripts

The zip linked above on the technet article have three scripts with three specific purposes

Script | Purpose
--- | ---
Create-pawOU | Set up new OU for tiered administration
Create-pawGroups |  Import groups.csv to create a sample tiered administrator set
Set-PAWOUdelegation | Use the groups above and set the proper delegation on the new OUs created

> Beginning to deploy Active Directory security is as easy as 1, 2, 3
1. Create OUs
1. Create Groups
1. Push delegation

#### Create-PAWOU OU Structure {#oustructure}
The Create-pawou script sets up an OU structure to allow administrative objects to be separate and secure from standard AD objects.  The Script goes through steps to set up the following structure:

- [Create OUs](#createous)
    - [Admin Base OU](#adminous)
        - [Tiered Base OUs](#tierous)
            - [Sub OUs under Tiers](#tiersubs)


---

### What This Post Covers
1. Create Administrative OUs

>  Remember there is plenty of work to do. Eventually the focus will shift to Tier 1 and Tier 2 credentials.  For this small moment in time, FIRST make sure Tier 0 is secure as possible.

#### Protecting Active Directory and Admin Users and Privileges by creating an administrative OU
 
*CENTRALIZE ADMINISTRATIVE ACCOUNTS:*

The location of the administrative users and groups is often overlooked. If I were to perform an audit on any random domain, there's a major chance that I'd find administrative accounts scattered across multiple Organizational Units. 

> Often by accident this scattered user placement creates pathways that attackers exploit to gain higher level of privileges.


----

## 1. Create Organizations Units {#createous}
### Active Directory PAW & Admin OU Layout
#####  [Back to the OU Top](#oustructure)

The privileged organization unit deploy is a bit different mindset for administrators. Understand that the admin OU is created specifically to secure the Active Directory Domain.

** These OUs are deployed by running the `create-pawou.ps1`

> Security > Everything Else

---

### Base Admin Organizational Unit Structure {#adminous}

#####  [Back to the OU Top](#oustructure)

1. The base OUs are created to store all the relative objects.
2. The base OUs are at the top of the domain, the first level of OUs possible to create on the domain

The Create-PAWOUs.ps1 script dynamically generates the working directory, domain name, and default context with the following code:
```powershell
#Get current working directory
$sLocation = Get-Location

$DomainName = (Get-ADDomain).Name
$sDSE = (Get-ADRootDSE).defaultNamingContext

#$sPath = ("OU="+ $DomainName + " Objects," + $($sDSE))
```

It uses the variables stored above to create the following OU structure at the top of the domain:

OU Name | Purpose | Created with the following code:
--- | --- | --
Admin | Holds all tier 0, 1, 2 user and service accounts, groups, and devices that manage those tiers. Also holds tier 0 computers that are not DCs | `New-ADOrganizationalUnit -Name "Admin" -Path "$sDSE"`
Groups | Holds group objects on the domain. **No tiered** groups here | `New-ADOrganizationalUnit -Name "Groups" -Path "$sDSE"`
Tier 1 Servers | Holds app and data servers. **No PAWs** here | `New-ADOrganizationalUnit -Name "Tier 1 Servers" -Path "$sDSE"`
Workstations | Holds Domain Joined computers (Tier 2 computers) | `New-ADOrganizationalUnit -Name "Workstations" -Path "$sDSE"`
User Accounts | Holds regular user accounts | `New-ADOrganizationalUnit -Name "User Accounts" -Path "$sDSE"`
Computer Quarantine | This is to be the new default computer OU | `New-ADOrganizationalUnit -Name "Computer Quarantine" -Path "$sDSE"`

---

The Active Directory Secure OU structure now begins to take shape:

{{<mermaid align="center">}}
graph LR;
    A[(DomainRoot.com)]
    A --> B{{Admin}} 
    A --> C{{Groups}}
    A --> D{{Tier 1 Servers}}
    A --> E{{Workstations}}
    A --> F{{User Accounts}}
    A --> G{{Computer Quarantine}}
    
{{< /mermaid >}}

---

### Tier OUs, Sub OUs for the Admin OU {#tierous}

#####  [Back to the OU Top](#oustructure)

In order to not have a 'flat' domain OU structure, Microsoft deploys multiple OUs underneath the 'Admin OU' to store objects that are tiered objects on the domain.  

Admin Sub OU Name | Purpose | Created with the following code:
--- | --- | ---
Tier 0 | Holds all Tier 0 users, service accounts, groups, devices (PAWS) and servers | `powershell New-ADOrganizationalUnit -Name "Tier 0" -Path ("OU=Admin,$sDSE")`
Tier 1 | Holds all Tier 1 users, service accounts, groups, devices (PAWS). **No servers are stored here** | `powershell New-ADOrganizationalUnit -Name "Tier 1" -Path ("OU=Admin,$sDSE")`
Tier 2 | Holds all Tier 2 users, service accounts, groups, devices (PAWS) **No Workstations are stored here**  | `powershell New-ADOrganizationalUnit -Name "Tier 2" -Path ("OU=Admin,$sDSE")`


---

The Active Directory Secure OU structure is now modified to:

```mermaid
graph LR
    root[(DomainRoot.com)] --> 1{{Admin}}
    root --> 2{{Groups}}
    root --> 3{{Tier 1 Servers}}
    root --> 4{{Workstations}}
    root --> 5{{User Accounts}}
    root --> 6{{Computer Quarantine}}
    subgraph 1g[The PAW OUs.]
      1
        1 --> 10(Tier 0)
        1 --> 11(Tier 1)
        1 --> 12(Tier 2)
    end
    subgraph 2g[All Non Admin Groups on Domain.]
      2
    end
    subgraph 3g[Data and App Servers.]
      3 
    end
    subgraph 4g[Standard domain joined computers]
      4
    end
    subgraph 5g[Standard User Accounts.]
      5
    end
    subgraph 6g[New Default Computer OU.]
      6
    end
    click 1 "/redforest/phase1/paws/paws_admin_ou/#tierous"
    click 10 "/redforest/phase1/paws/paws_admin_ou/#t0ous"
    click 11 "/redforest/phase1/paws/paws_admin_ou/#t1ous"
    click 12 "/redforest/phase1/paws/paws_admin_ou/#t2ous"

linkStyle 0,1,2,3,4,5,6 stroke-width:1px;

style 1g stroke:#323232,stroke-width:1px,stroke-dasharray:5;
style 2g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 3g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 4g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 5g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 6g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;

```
---

### Sub OUs under the Tier OUs in the Admin OU {#tiersubs}

#####  [Back to the OU Top](#oustructure)

The Active Directory Security Administrator now needs to take the step to expand the OUs underneath the Tiered OUs under the Admin OU.  This is also performed by the `Create-PAWOUs.ps1`

#### Sub Ous for Admin\Tier 0 OU {#t0ous}

Tier Sub OU Name | Description | Created with the following code:
--- | --- | ---
Accounts | Tier 0 User (People) accounts | `New-ADOrganizationalUnit -Name "Accounts" -Path ("OU=Tier 0,OU=Admin,$sDSE")`
Groups | Tier 0 Groups - Management groups for Tier 0 Accounts, Service Accounts, Devices, and Tier 0 Servers | `New-ADOrganizationalUnit -Name "Groups" -Path ("OU=Tier 0,OU=Admin,$sDSE")`
Service Accounts | Tier 0 Service Accounts that perform Tier 0 work on schedules | `New-ADOrganizationalUnit -Name "Service Accounts" -Path ("OU=Tier 0,OU=Admin,$sDSE") `
Devices | Tier 0 PAWs | `New-ADOrganizationalUnit -Name "Devices" -Path ("OU=Tier 0,OU=Admin,$sDSE")`
Tier 0 Servers | Tier 0 Servers that manage applications and services on Domain Controllers. [Tier 0 Devices]({{< ref "/redforest/phase1/adminaccounts/Tier 0 Admins/#observed" >}}) | `New-ADOrganizationalUnit -Name "Tier 0 Servers" -Path ("OU=Tier 0,OU=Admin,$sDSE")`

#### Sub Ous for Admin\Tier 1 OU {#t1ous}

Tier Sub OU Name | Description | Created with the following code:
--- | --- | ---
Accounts | Tier 0 User (People) accounts | `New-ADOrganizationalUnit -Name "Accounts" -Path ("OU=Tier 1,OU=Admin,$sDSE")`
Groups | Tier 0 Groups - Management groups for Tier 0 Accounts, Service Accounts, Devices, and Tier 0 Servers | `New-ADOrganizationalUnit -Name "Groups" -Path ("OU=Tier 1,OU=Admin,$sDSE")`
Service Accounts | Tier 0 Service Accounts that perform Tier 0 work on schedules | `New-ADOrganizationalUnit -Name "Service Accounts" -Path ("OU=Tier 1,OU=Admin,$sDSE") `
Devices | Tier 0 PAWs | `New-ADOrganizationalUnit -Name "Devices" -Path ("OU=Tier 1,OU=Admin,$sDSE")`


#### Sub Ous for Admin\Tier 2 OU {#t2ous}

Tier Sub OU Name | Description | Created with the following code:
--- | --- | ---
Accounts | Tier 1 User (People) accounts | `New-ADOrganizationalUnit -Name "Accounts" -Path ("OU=Tier 2,OU=Admin,$sDSE")`
Groups | Tier 1 Groups - Management groups for Tier 1 Accounts, Service Accounts, Devices, and Tier 1 Servers | `New-ADOrganizationalUnit -Name "Groups" -Path ("OU=Tier 2,OU=Admin,$sDSE")`
Service Accounts | Tier 1 Service Accounts that perform Tier 0 work on schedules | `New-ADOrganizationalUnit -Name "Service Accounts" -Path ("OU=Tier 2,OU=Admin,$sDSE") `
Devices | Tier 1 PAWs | `New-ADOrganizationalUnit -Name "Devices" -Path ("OU=Tier 2,OU=Admin,$sDSE")`

---

The Active Directory Secure Admin OU structure is now modified to:


```mermaid
graph LR
    root[(DomainRoot.com)] --> 1{{Admin}}
    root --> 2{{Groups}}
    root --> 3{{Tier 1 Servers}}
    root --> 4{{Workstations}}
    root --> 5{{User Accounts}}
    root --> 6{{Computer Quarantine}}
    subgraph 1g[The PAW OUs.]
      1
        1 --> 10(Tier 0)
        1 --> 11(Tier 1)
        1 --> 12(Tier 2)
    end
    subgraph 1g0[Tier 0 OUs]
    10 --> B0A0(Accounts)
    10 --> B0D0(Devices)
    10 --> B0G0(Groups)
    10 --> B0SA0(Service Accounts)
    10 --> B0T0S0(Tier 0 Servers)
    end
    subgraph 1g1[Tier 1 OUs]
    11 --> B0A1(Accounts)
    11 --> B0D1(Devices)
    11 --> B0G1(Groups)
    11 --> B0SA1(Service Accounts)
    end
    subgraph 1g2[Tier 2 OUs]
    12 --> B0A2(Accounts)
    12 --> B0D2(Devices)
    12 --> B0G2(Groups)
    12 --> B0SA2(Service Accounts)
    end
    subgraph 2g[All Non Admin Groups on Domain.]
      2
    end
    subgraph 3g[Data and App Servers.]
      3 
    end
    subgraph 4g[Standard domain joined computers]
      4
    end
    subgraph 5g[Standard User Accounts.]
      5
    end
    subgraph 6g[New Default Computer OU.]
      6
    end
    
    click 1 "/redforest/phase1/paws/paws_admin_ou/#tierous"
    click 10 "/redforest/phase1/paws/paws_admin_ou/#t0ous"
    click 11 "/redforest/phase1/paws/paws_admin_ou/#t1ous"
    click 12 "/redforest/phase1/paws/paws_admin_ou/#t2ous"
linkStyle 0,1,2,3,4,5,6 stroke-width:1px;

style 1g fill:transparent,stroke:#323232,stroke-width:1px,stroke-dasharray:5;
style 2g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 3g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 4g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 5g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;
style 6g fill:transparent,stroke:#E5E5E5,stroke-width:1px,stroke-dasharray:5;

```
---

### Sub OUs under Groups, Tier 1, Workstations and User Accounts {#grps}

#### Top Level OU: Groups Sub OUs
{{% notice info %}}
I will be collapsing the admin OU for the next chart.  For reference on the admin ou, please click on the box in the chart.
{{% /notice %}}
Sub OU Name | Description | Created with the following code:
--- | --- | ---
Security Groups | Groups that provide access to resources | `New-ADOrganizationalUnit -Name "Security Groups" -Path ("OU=Groups,$sDSE")`
Distribution Groups | Place for all email distribution groups | `New-ADOrganizationalUnit -Name "Distribution Groups" -Path ("OU=Groups,$sDSE")`
Contacts Accounts | Contains objects with no security permissions.  External Email users | `New-ADOrganizationalUnit -Name "Contacts" -Path ("OU=Groups,$sDSE")`
