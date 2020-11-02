---
title: Get All AWS Administrators with POWERSHELL
weight: 10
chapter: false
aliases:
    - blog/aws-powershell-get-all-aws-administrators/
---

Get All AWS Administrators with POWERSHELL
I needed to list all the accounts created as IAM users inside all the AWS Organizations for a business. I wanted to show on list all the administrators of the organizations, those people who can make sweeping changes to an environment. My end goal was to make sure that everyone who has direct IAM console administrator access was using MFA.

I wanted this audit report:  A list of users in each organization with their MFA status.  And look at that, all sorts of users (blacked-out) and root users with no MFA protection.  The below screen shot shows root user status and IAM users associated with administrator access in AWS.

This post does not go into which of these users have CLI Access in an environment.  For a guide on enumerating access keys created in your AWS environment, please see: List all Access Keys with AWS.

 
Organizations Admins without MFA-1

I started with the AWS tools to see how far I could get with this process. I enabled Security Hub and AWS Config. I configured security hub to send all my separate organizations' logs to a centralized location. I had a wonderful alert: "1.2 Ensure Multi-factor authentication (MFA) is enabled for all IAM users that have a console password"

 

AWS Security Hub MFA
There were no further details. I couldn't click anywhere on here to find users or a export any sort of lists.

I looked at AWS Config. I made sure all the IAM checks were in place:

IAM Config Checks
The 'iam-user-mfa-enabled' looked to be exactly what I wanted to see. When I clicked on it I got "No results reported". No help there without further troubleshooting. I jumped into aws-labs python code, but realized that lambda code was not the path I wanted to head down today.

 

awsCONFIG IAM USERS MFA-1
Now, I COULD do some of this with AWS CLI using "aws iam get-credential-report", but that isn't PowerShell.  I had it in my head that I wanted to make a report with the AWS PowerShell cmdlets.

I decided to start my own AWS PowerShell code repo.

List MFA Status for IAM Users in all Organizations
 

Step 1: Get all sub organizations.
AWS powershell commandlets were completely new to me. Luckily a little bit of Googling I found the answer in no time.  Connect to your master organization account and run this:

$orgs = Get-ORGAccountList|select id, name, email
Step 2: Assume role to auth to all sub organizations
Searching for switching roles in AWS PowerShell led me to the AWS PowerShell IAM user guide. On this page is a one liner that allows a person to assume a specified role of a specified organization.

$Creds = (Use-STSRole -RoleArn "arn:aws:iam::123456789012:role/RoleName" -RoleSessionName "MyRoleSessionName").Credentials
This code led me to search some more and I found the to the cross-account in Powershell blog. This was the key code I found from the page. 

#List of accounts to check
$Accounts = @(111111111111, 222222222222, 333333333333, 444444444444)

#Iterate over each account
$Accounts | % {
     $Account = $_

$RoleArn = "arn:aws:iam::${Account}:role/CMDBDiscovery"
#Request temporary credentials for each account and create a credential object

$Response = (Use-STSRole -Region $Region -RoleArn $RoleArn -RoleSessionName 'CMDB').Credentials
$Credentials = New-AWSCredentials -AccessKey $Response.AccessKeyId -SecretKey $Response.SecretAccessKey -SessionToken $Response.SessionToken
#Iterate over all regions and list instances
Get-AWSRegion | % {
ListInstances -Credential $Credentials -Account $Account -Region $_.Region
}
} | ConvertTo-Csv
I manipulated this code just a little to create a switch role function.  I figured I would need to switch roles many times in the future.  This function can be found on my AWS_Powertools Git.

Function Switch-AWSRoles {

param(
[Parameter(Mandatory=$true)][string]$OrganizationID,
[Parameter(Mandatory=$true)][string]$Role

)

$region = (Get-AWSRegion|where-object -Property isshelldefault -eq $true)

$RoleArn = "arn:aws:iam::${OrganizationID}:role/$role"

#Request temporary credentials for each account and create a credential object

$Response = (Use-STSRole -Region $Region -RoleArn $RoleArn -RoleSessionName 'CMDB').Credentials
$Credentials = New-AWSCredentials -AccessKey $Response.AccessKeyId -SecretKey $Response.SecretAccessKey -SessionToken $Response.SessionToken

$Credentials

}

With this function, it's now a one liner to create temporary credentials for an organization.

Switch-AWSRoles -OrganizationID 123456789 -Role 'access-role'

 
Step 3: Query all Administrators in all Sub Organizations 
Use the list provided in Step 1, and the function created in step 2 to authenticate to all organizations and get a list of all IAM users.  I created a function to specifically query the policy named 'AdministratorAccess' defined as:

Use case: This user has full access and can delegate permissions to every service and resource in AWS.

Policy description: This policy grants all actions for all AWS services and for all resources in the account.

Source
 
I created a function to do the hard work for you.  See the updated code on the Git link provided for full details.  By default this code exports a list of users attached directory or indirectly to the policy "AdministratorAccess'. As of this posting it's in its beta stages, but does exactly what I asked it to :) Get-AWSRoleUserList . 
 
Function Get-AWSRoleUserList {
#This function currently lists all IAM user accounts specified in an instance under the policy titled: AdministratorAccess
param(
[Parameter(Mandatory=$true)]$Role, #where $role is the role you want to assume to authenticate to another organization.
[string]$PolicyName = "AdministratorAccess"
)
#get all organizations
$orgs = Get-ORGAccountList|select id, name, email
$adminlist = @()

#loop through each org
foreach ($org in $orgs){
$id =$org.Id
$nm = $org.name
#create new psobject for reporting. List Org ID, full org ARN, name, and will map Administrators to this object
$obj = new-object psobject
$obj |Add-member NoteProperty OrganizationID $id
$obj |Add-member NoteProperty Arn $org.arn
$obj | Add-Member NoteProperty OrganizationName $nm
$account = $org.id

try {
$Credentials = Switch-AWSRoles -OrganizationID $id -Role $role

#List all policies, get all entities on policy
$admins = Get-IAMEntitiesForPolicy -PolicyArn "arn:aws:iam::aws:policy/$PolicyName" -Credential $Credentials
$obj | add-member NoteProperty PolicyGroups $admins.policygroups
if ($admins.policygroups.count -gt 0){
$i = 0
foreach ($grp in $admins.policygroups){
$grpmembers = get-iamgroup -GroupName $admins.PolicyGroups[$i].groupname -Credential $Credentials
$obj |add-member NoteProperty PolGroupMembers $grpmembers.users
$i++
$grpmembers = $null
}

}
else{

}
}
catch {$admins = Get-IAMEntitiesForPolicy -PolicyArn "arn:aws:iam::aws:policy/$PolicyName"
$obj | add-member NoteProperty PolicyGroups $admins.policygroups
if ($admins.policygroups.count -gt 0){
$i = 0
foreach ($grp in $admins.policygroups){
$grpmembers = get-iamgroup -GroupName $admins.PolicyGroups[$i].groupname
$obj |add-member NoteProperty PolGroupMembers $grpmembers.users
$i++
}
}
else{
}
}

$obj | add-member NoteProperty PolicyRoles $admins.PolicyRoles
$obj | add-member NoteProperty PolicyUsers $admins.PolicyUsers
$adminlist += $obj

}
$adminlist
}
 
Usage of this script is simplified down to one line.

Get-AWSRoleUserList -Role AccessRole 

Where "-Role" is the role used to authenticate to sub organizations.  This code default checks the "administratoraccess" policy, but this can be changed to any role you want to query.  To store the output to a variable, you can do something like this

$admins = Get-AWSRoleUserList -role AccessRole

The output of the function lists all IAM users for all organizations attached to the specified role.  It shows groups attached to the policy, the users attached to the groups, and individual users attached to the policy

AdminList

 

In this example, you can see a" tempadmingroup" and a group named "Bob" have administrator access in some organizations.  You can also see there are users attached to these groups in column PolGroupMembers.  You can also see in the last column, there are users attached directly to the policy in some of the organizations.

 

Step 4: Find MFA status for all Users from a list
There was no quick command to show MFA status for all users, so I had to do some data manipulation. 

Get list of users to check (done with step 3)
Get all MFA devices in all organizations
Change the mfa data to match the user list for validation
Match mfa data to all root and IAM users to see if MFA is enabled on their accounts
I was able to make all the steps into a function which can be found on my git. Get-AWSIAMUsersNoMFADevice

The usage of the script is:

Get-AWSIAMUsersNoMFADevice -userlist $storedlistofusers -role AccessRole

Where "-Userlist" is any group of users exported from the Get-AWSRoleUserList command.

 

Organizations Admins without MFA-1

You can find the updated code in the git repo.  If you need help in setting up MFA for users in your AWS environment please read: 10 Steps to Enable MFA

Wrapping all the functions together, you can now get a list of all admins in all organizations, with their MFA status with one line of code.  (You have to specify the assumed $role variable)

Get-AWSIAMUsersNoMFADevice -UserList (Get-AWSRoleUserList -Role $role) -Role $role