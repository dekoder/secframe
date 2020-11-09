


When can you see LAPS passwords? After I uncovered how easy it is to gain access to a password by moving machines around a domain, I began to think, “What ACLs do you need to access the password?” Using a BadBlood domain I put this question to the test.

This post is quick and dirty.
The reference for this research:

(For LAPS)Read permission is not enough. AD honors the read request for confidential attribute value when at least one of the following is true:

Caller is granted 'Full Control' permission
Caller is granted 'All Extended Rights' permission
Caller is granted 'Control Access' on the attribute permission (this is what LAPS PowerShell uses to grant the permission)
https://blogs.msdn.microsoft.com/laps/2015/06/01/laps-and-password-storage-in-clear-text-in-ad

 

 

I set up OUs for testing
LAPS Permission ACL Testing
I applied LAPS GPO so computers get LAPS passwords
LAPS Permission ACL Testing
Confirmed LAPS password is set on computer.
(Laps Password is blank! Click Expire. Wait. Check Again. Confirm!)LAPS Permission ACL Testing

Set user object variable for testing with the BadBlood AD_OU_SetACL scripts. (Good old Lillie Gardner. What a perfect name for a LAPS reading person.)
Also set a value for ‘inheritance’ to use on the AD_OU_SetACL scripts
LAPS Permission ACL Testing
Set $ou value to an organizational unit I want to use.
First OU I’ll configure is “Badblood.com\Laps Testing\Laps plus extended”
$ou = get-adorganizationalunit ‘<FullDNHere’
Load into memory: ‘full control permissions.ps1’ and ‘computer control permissions.ps1’ from Badblood\AD_OU_SetACL. One function loaded with ‘computer control permissions’ is the ReadComputerAdmPwd function, which lets objects read the LAPS attribute.
LAPS Permission ACL Testing
Set permissions! Only to find out I didn’t do something right.
LAPS Permission ACL Testing
Load $guidmap and $extendedrightsmap variables using lines 163 through 178 in Badblood file ‘AD_Permission_Randomizer’
LAPS Permission ACL Testing
Run it again!
ReadComputerAdmPwd -objgroup $user -objOU $OU -inheritanceType $inherittype… Success
Result Laps plus extended right on Laps plus extended OU. (Forget that this “information” write-host says “group”. I granted access to the user.)
LAPS Permission ACL Testing
Edit ReadComputerAdmPwd to only include Computer Extended properties
LAPS Permission ACL Testing
Set OU to Badblood.com\LAPS Testing\Computer All Extended. Run the same code again
The result: Computer Extended rights on Computer All Extended OU
LAPS Permission ACL Testing
Verify work by checking acl via dsa.msc gui. (
Looks like we are all set. Permissions granted for Lillie Gardner as expected
LAPS Permission ACL Testing
Run Full Control and Full Control Computers permissions on the next OUs in the list to test
LAPS Permission ACL Testing
LAPS Permission ACL Testing
The result: Permissions added to appropriate OUs
Last OU! Add only the ‘ms-mcs-admpwd read rights to Lillie’s user account on the LAPS only OU. (This does not add “Full Control”, “Extended Rights”, or “Control Access” permissions on the OU).
Edit ReadComputerAdmPWD to only include ms-mcs-admpwd attribute
LAPS Permission ACL Testing
Run on ReadComputerAdmPWD again
Result: rights added as expected
LAPS Permission ACL Testing
Test All OUs!
The Results
Permission Applied to the Organizational Unit	Can View LAPS?
Computer Object All Extended	YES
Full Computer Rights	YES
Generic All Rights	YES
Laps attribute no Extended	NO
Laps attribute Plus Extended	YES
No Rights	NO
 

LAPS Permission ACL Testing
 

What did I miss?  Are there other ACLs that need to be added to this test? 

Let me know and I'll update my test.  If LAPS is enabled in your AD environment, make sure you know who can gain administrator privileges to the computer!