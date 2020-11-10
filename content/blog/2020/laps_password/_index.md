---
title: Who Can See LAPS Passwords?
description: When can you see LAPS passwords? What ACLs do you need to access the password? Make sure you know who can gain administrator privileges to the computer!
date : 2020-01-31T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Security','Active Directory','LAPS']
aliases:
    - /blog/when-can-you-see-a-laps-password
---


## When can you see LAPS passwords? 
After I uncovered how easy it is to gain access to a password by moving machines around a domain, I began to think, “What ACLs do you need to access the password?” Using a BadBlood domain I put this question to the test.

This post is quick and dirty.
### The reference for this research:

(For LAPS)Read permission is not enough. AD honors the read request for confidential attribute value when at least one of the following is true:

> > Caller is granted 'Full Control' permission

> > Caller is granted 'All Extended Rights' permission

> > Caller is granted 'Control Access' on the attribute permission (this is what LAPS PowerShell uses to grant the permission)

> [https://blogs.msdn.microsoft.com/laps/2015/06/01/laps-and-password-storage-in-clear-text-in-ad](https://blogs.msdn.microsoft.com/laps/2015/06/01/laps-and-password-storage-in-clear-text-in-ad)

 

 

1. I set up OUs for testing
![LAPS Permission ACL Testing](images/1.png?classes=shadow&width=20pc)
1. I applied LAPS GPO so computers get LAPS passwords
![LAPS Permission ACL Testing](images/2.png?classes=shadow&width=40pc)
1. Confirmed LAPS password is set on computer.
(Laps Password is blank! Click Expire. Wait. Check Again. Confirm!)
![LAPS Permission ACL Testing](images/3.png?classes=shadow&width=20pc)
1. Set user object variable for testing with the BadBlood AD_OU_SetACL scripts. (Good old Lillie Gardner. What a perfect name for a LAPS reading person.)
1. Also set a value for ‘inheritance’ to use on the AD_OU_SetACL scripts
![LAPS Permission ACL Testing](images/5.png?classes=shadow&width=40pc)
1. Set $ou value to an organizational unit I want to use.
    1. First OU I’ll configure is “Badblood.com\Laps Testing\Laps plus extended”
    1. $ou = get-adorganizationalunit ‘<FullDNHere’
1. Load into memory: ‘full control permissions.ps1’ and ‘computer control permissions.ps1’ from Badblood\AD_OU_SetACL. One function loaded with ‘computer control permissions’ is the ReadComputerAdmPwd function, which lets objects read the LAPS attribute.
![LAPS Permission ACL Testing](images/7.png?classes=shadow&width=40pc)
1. Set permissions! Only to find out I didn’t do something right.
![LAPS Permission ACL Testing](images/8.png?classes=shadow&width=40pc)
1. Load $guidmap and $extendedrightsmap variables using lines 163 through 178 in Badblood file ‘AD_Permission_Randomizer’
![LAPS Permission ACL Testing](images/9.png?classes=shadow&width=40pc)
1. Run it again!
    1. ReadComputerAdmPwd -objgroup $user -objOU $OU -inheritanceType $inherittype… Success
    1. Result Laps plus extended right on Laps plus extended OU. (Forget that this “information” write-host says “group”. I granted access to the user.)
![LAPS Permission ACL Testing](images/10.png?classes=shadow&width=40pc)
1. Edit ReadComputerAdmPwd to only include Computer Extended properties
![LAPS Permission ACL Testing](images/11.png?classes=shadow&width=40pc)
1. Set OU to Badblood.com\LAPS Testing\Computer All Extended. Run the same code again
    1. The result: Computer Extended rights on Computer All Extended OU
![LAPS Permission ACL Testing](images/12.png?classes=shadow&width=40pc)
1. Verify work by checking acl via dsa.msc gui. (
    1. Looks like we are all set. Permissions granted for Lillie Gardner as expected
![LAPS Permission ACL Testing](images/13.png?classes=shadow&width=40pc)
1. Run Full Control and Full Control Computers permissions on the next OUs in the list to test
![LAPS Permission ACL Testing](images/14.png?classes=shadow&width=40pc)
![LAPS Permission ACL Testing](images/14_2.png?classes=shadow&width=40pc)
    1. The result: Permissions added to appropriate OUs
1. Last OU! Add only the ‘ms-mcs-admpwd read rights to Lillie’s user account on the LAPS only OU. (This does not add “Full Control”, “Extended Rights”, or “Control Access” permissions on the OU).
1. Edit ReadComputerAdmPWD to only include ms-mcs-admpwd attribute
![LAPS Permission ACL Testing](images/16.png?classes=shadow&width=40pc)
1. Run on ReadComputerAdmPWD again
    1. Result: rights added as expected
![LAPS Permission ACL Testing](images/17.png?classes=shadow&width=40pc)
1. Test All OUs!
### The Results
| Permission Applied to the Organizational Unit | 	Can View LAPS? |
| :--- | :--: |
| Computer Object All Extended |	YES |
| Full Computer Rights |	YES |
| Generic All Rights |	YES |
| Laps attribute no Extended |	NO |
| Laps attribute Plus Extended |	YES |
| No Rights |	NO |
 

![LAPS Permission ACL Testing](images/18.png?classes=shadow&width=40pc)
 

What did I miss?  Are there other ACLs that need to be added to this test? 

Let me know and I'll update my test.  If LAPS is enabled in your AD environment, make sure you know who can gain administrator privileges to the computer!