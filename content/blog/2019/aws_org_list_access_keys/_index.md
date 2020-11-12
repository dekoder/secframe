---
title: List AWS Access Keys with CLI Access
date: 06-06-2019
aliases:
   - /blog/list-all-aws-access-keys-for-all-organizations
---
![aws access keys powershell](images/AWSAccessKeyList.png?classes=shadow&width=60pc)

Viewing access key usage in IAM was introduced in Q2 2015. AWS Organizations was introduced in Q4 2016. The blog post describing how to search for IAM Access Key usage via CloudTrail was written Q1 2019. Listing all access key usage in IAM for all AWS Organizations, that's introduced today Q3 2019.

This list comes in handy when you have to gather all the API keys for auditing and governance requirements.

I had a small task ahead of me. Report on the usage of all access keys for all accounts in an organization. I found myself over encumbered by accounts, and by 'over encumbered' I mean 'more than 3'. I had to repeat a manual process on each account to list ever access key created in each account. I used my previous work from Get All AWS Administrators with POWERSHELL as a base to build on. If you haven't read that post, give yourself 5 minutes to go through it to get a basic understanding of looping through organizations with PowerShell.

The most up to date code for this post can be found on my GitHub in the AWS_PowerTools\IAM folder. I'll eventually make a readme file, but for now all there is for guidance is this website.

The code is pretty straight forward. The function is:

`Get-IAMAccessKeysAllOrgs -Role 'ROLENAME'`

> Where the -Role parameter is the role you are authenticating into for read access into the sub organizations.  Please note, this doesnt show access keys that are created for the root account.

The script obtains all the accounts from your master account. Then loops through those accounts to get a list of IAMUsers, and all the access keys created for them. It gathers that data and places it an easy to read output form.

If you have any questions feel free to ask. My linkedin network is open and you can send me a message via twitter.

AWS

 
```powershell

Function Get-IAMAccessKeysAllOrgs{ 
 
param(
    [Parameter(Mandatory=$true)]$Role
    
)

    $orgs = Get-ORGAccountList|select id, name, email
    $AKDetails = @()
    
    $accesskeys = @()

    foreach ($org in $orgs){
        $id =$org.Id
        $nm = $org.name
        $userlist = @()
        $accesskeys = @()
        if ((Get-STSCallerIdentity).account -ne $id){
    
            $id = $null
            $credentials = $null
            $rolearn = $null

                $id= $org.id
                $RoleArn = "arn:aws:iam::${Orgid}:role/$role"
                $Credentials = Switch-AWSRoles -OrganizationID $id -Role $role
                $userlist = get-iamuserlist -Credential $Credentials 
                $userlist |% { 
                    $uarn = $_.arn
                    $uorg = ($uarn -split ":")[4]
                    $accesskeys += Get-IAMAccessKey -UserName $_.UserName -Credential $credentials
                   }
                $accesskeys |% {
                        $lastused = Get-IAMAccessKeyLastUsed -AccessKeyId $_.AccessKeyId -Credential $credentials
                        
                        $obj = new-object psobject
                        $obj |Add-member NoteProperty OrganizationID $uorg
                        $obj |Add-member NoteProperty OrganizationName $nm
                        $obj |Add-member NoteProperty UserName $lastused.UserName 
                            $obj |Add-member Noteproperty AccessKeyID $_.AccessKeyId
                            $obj |Add-Member NoteProperty KeyCreateDate $_.CreateDate
                            $obj |Add-Member NoteProperty KeyStatus $_.Status
                                $obj |Add-member Noteproperty LastUsed $lastused.AccessKeyLastUsed.LastUsedDate
                                $obj |Add-member Noteproperty Region $lastused.AccessKeyLastUsed.Region
                                $obj |Add-member Noteproperty Service $lastused.AccessKeyLastUsed.ServiceName
                                $akdetails += $obj
                        }
        }

        else {


         $id = $null
            $credentials = $null
            $rolearn = $null

                $id= $org.id
                $userlist = get-iamuserlist 
                $userlist |% { 
                    $uarn = $_.arn
                    $uorg = ($uarn -split ":")[4]
                    $accesskeys += Get-IAMAccessKey -UserName $_.UserName
                   }
                $accesskeys |% {
                        $lastused = Get-IAMAccessKeyLastUsed -AccessKeyId $_.AccessKeyId
                        
                        $obj = new-object psobject
                        $obj |Add-member NoteProperty OrganizationID $uorg
                        $obj |Add-member NoteProperty OrganizationName $nm
                        $obj |Add-member NoteProperty UserName $lastused.UserName 
                            $obj |Add-member Noteproperty AccessKeyID $_.AccessKeyId
                            $obj |Add-Member NoteProperty KeyCreateDate $_.CreateDate
                            $obj |Add-Member NoteProperty KeyStatus $_.Status
                                $obj |Add-member Noteproperty LastUsed $lastused.AccessKeyLastUsed.LastUsedDate
                                $obj |Add-member Noteproperty Region $lastused.AccessKeyLastUsed.Region
                                $obj |Add-member Noteproperty Service $lastused.AccessKeyLastUsed.ServiceName
                                $akdetails += $obj
                        }
        }
        
        }
        $akdetails |sort-object organizationid,LastUsed
            
            }

```
 

