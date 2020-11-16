---
title: Install and Configure LAPS
weight: 10
date: 2020-11-12T16:47:17-04:00
chapter: false
draft: true
---

## Steps to install LAPS

**Don't overcomplicate things.**

1. [Update Active Directory Schema](#step1)
2. [Create and Enable GPO for Password settings sand rotation policy](#step2)
3. [Install LAPS client into windows computers](#step3)

## Step 1 (step1)
### Update Active Directory Schema

I boiled this down to one function in powershell. The [Badblood Repo Laps Install Folder](https://github.com/davidprowe/BadBlood/tree/master/AD_LAPS_Install)
has the function `.BadBlood/AD_LAPS_Install/InstallLAPSSchema.ps1`

Which runs the following code. 

```powershell
function Get-ScriptDirectory {
    Split-Path -Parent $PSCommandPath
}
$scriptPath = Get-ScriptDirectory

copy-item -path ($scriptpath + "\admpwd.ps") -destination "C:\Windows\System32\WindowsPowerShell\v1.0\Modules"
get-childitem -path ($scriptpath + "\admpwd.ps") -recurse |Foreach-object {
    Copy-item -literalpath $_.fullname -destination "C:\Windows\System32\WindowsPowerShell\v1.0\Modules\admpwd.ps"
}
copy-item -path ($scriptpath + "\AdmPwd.admx") -destination "C:\Windows\PolicyDefinitions"
copy-item -path ($scriptpath + "\AdmPwd.adml") -destination "C:\Windows\PolicyDefinitions\en-US"

Import-Module ADMPwd.ps
Update-AdmPwdADSchema
Set-AdmPwdComputerSelfPermission -OrgUnit (Get-ADDomain).distinguishedname
```

If you are a Domain Administrator and Schema Administrator, and logged into a domain controller this function does the following for you:

1. Copies the LAPS powershell modules into the modules folder on the server you are logged into
2. Imports the LAPS GPO templates into the server
3. Extends the schema for LAPS
4. Adds the computer object property on the root of the domain to each computer to write its new custom administrative password to the admpwd attribute

> Step one complete by running one script

onto...
-------

## Step 2 (step2)
### Create and enable GPO for LAPS settings on the domain

There are two GPOs to configure in the domain: One for servers. One for workstations

| Computer type | Recommended LAPS setting |
| --- | --- |
| [Tier 0]() Domain Controllers | No LAPS installation |
| [Tier 1]() Servers | 7 day maximum password age |
| [Tier 2]() Workstation | 30 day maximum password age |

----
### Tier 2 LAPS GPO
Here's an outline of what the LAPS GPO should look like for workstations, tier 2 devices.
![tier 1 laps](images/laps1.png)

The settings are outlined as follows  
Setting Name  | Suggested Setting
---|---
  | 
  
## Step 3 (step3)
### Install LAPS client into windows computers

Keep it easy. 
> Install Laps using the same GPO created in step 2

Here's how to do it
to add picture of install software

The settings are outlined as follows  
Setting Name  | Suggested Setting
---|---
  | 
----

## LAPS is now successfully deployed

Congratulations. Microsoft LAPS is now successfully deployed in the domain.
