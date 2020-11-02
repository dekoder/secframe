---
title: List All Access Keys
weight: 75
aliases:
    - blog/list-all-aws-access-keys-for-all-organizations/
---

Viewing access key usage in IAM was introduced in Q2 2015. AWS Organizations was introduced in Q4 2016. The blog post describing how to search for IAM Access Key usage via CloudTrail was written Q1 2019. Listing all access key usage in IAM for all AWS Organizations, that's introduced today Q3 2019.

This list comes in handy when you have to gather all the API keys for auditing and governance requirements.

I had a small task ahead of me. Report via CloudTrail or Athena on the usage of all access keys for all organizations. I found myself over encumbered by organizations, and by 'over encumbered' I mean 'more than 3'. I had to repeat a manual process on each organization to list ever access key created in each organization. I used my previous work from Get All AWS Administrators with POWERSHELL as a base to build on. If you haven't read that post, give yourself 5 minutes to go through it to get a basic understanding of looping through organizations with PowerShell.

The most up to date code for this post can be found on my GitHub in the AWS_PowerTools\IAM folder. I'll eventually make a readme file, but for now all there is for guidance is this website.

The code is pretty straight forward. The function is:

Get-IAMAccessKeysAllOrgs -Role 'ROLENAME'
Where the -Role parameter is the role you are authenticating into for read access into the sub organizations.  Please note, this doesnt show access keys that are created for the root account.

The script obtains all the organizations from your master account. Then loops through those organizations to get a list of IAMUsers, and all the access keys created for them. It gathers that data and places it an easy to read output form.

{{< gist davidprowe e3c2d1c59dfd93867d18950c259b1545 >}}