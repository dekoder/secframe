---
title: Flaws.cloud level 1
date: 06-01-2019
description: working through flaws.cloud with powershell
aliases:
   -/blog/working-my-way-through-flaws-cloud
---

 
Permissions to AWS S3 buckets are an extremely important aspect of public cloud storage. On the heels of the Capital One data breach, testing and verifying the proper access is granted to all the S3 buckets in a VPC cannot be overstressed. Granting excessive permissions on buckets leaves vulnerabilities that can be exposed and turned into data breaches.

Flaws.cloud is a free aws security training tool. The creator of flaws.cloud made a reference to his level 4 and level 5 lessons following the Capital One S3 Breach. These lessons were created two and a half years ago, and their principles still apply today. I decided to work my way through the lessons to gain a better understanding of best practices as observed by @0xdabbad00.

 

In addition to going through the levels, I took it upon myself to complete the steps in PowerShell using the AWS Powershell cmdlets. This extra step, converting to powershell, let me understand the levels and their architecture and gain a firm understanding of the basics of working with standard AWS Powershell cmdlets.

 

The first lesson dives right into a publicly accessible S3 bucket and requires you to find the sub files on the domain. The CTF is to find the file in the public S3 bucket that contains the url to the next lesson. The first step was to identify the source IP of the domain. Where $s3 = ‘flaws.cloud’

 

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\IP Address of S3.PNG
 

After getting the IP address, we need to find the s3 region to perform the next few steps. That can be done in one step seen below. I take the resolved DNS name of the S3 bucket, and using that IP address, find the DNS name again to find the NameHost s3-website-us-west-2.amazonaws.com. NICE step one can be completed in a single step:

(Resolve-dnsname ((resolve.dnsname $s3).ipaddress)).namehost

 

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\Region of S3 Bucket.PNG
 

For the sake of automation, I decided to take the region and convert it into a standard parameter needed for the common parameter “-Region”. This parameter can be found in most if not all the AWS PowerShell Commandlets:

 


 

 

I took the output of $region = (Resolve-dnsname ((resolve.dnsname $s3).ipaddress)).namehost

And split it so that I can combine it into the proper format. Some string manipulation later, I get “us-west-2” which is exactly what is needed for the –Region parameter in aws powershell.

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\Region Split.PNG
 

 

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\Region combine.PNG
 

You can automatically get the region for any S3 bucket with a few lines of code:



Now that we have the region parsed and the bucket name, using the Get-S3object function we can list all the files in a bucket:

The code in the tutorial uses the aws cli which gives a simple output of all the filenames in the bucket

 

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\All Files - aws cli.PNG
 

To figure out how to get the same output in PowerShell I dug a bit into the S3 functions to find that the Get-S3object function was the proper function. We can get the files in the bucket using the following command:

$files = Get-S3object –Bucketname $s3 –Region $regionsplit

 

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\All Files - aws powersell.PNG
 

This is an interesting set of commands on a grossly mis-configured bucket. I do not need credentials or an access key to gain access to the details of this bucket.  No need for the root account, no need for an IAM user, or a role.  Great this lists all the files, but it doesn’t give you their URL. To do that, we need to combine the ‘filename’ which is stored as the ‘Key’ in the output with the url of the bucket and region. I did this with the following foreach loop:

$output = @()
    $files|%{
         $output += "http://" + $s3 +"."+ $region +'/'+$_.key
         }
$output

 

This level and bucket is a small S3 bucket with very few objects in it. The code gives an output of all the files in the bucket and their urls:

C:\Users\ch212692\Pictures\SecFrame\Flaws.Cloud\Level 1\Output All Files.PNG
 

The new file found here is “secret-dd02c7c.html” If we use that url, we can see that we found the secret to Level 1.

I wanted to take this a small step further and decided to create a function to automate all the tasks in this level. This function can be found on my github repo “list all files in bucket.ps1” https://github.com/davidprowe/AWS_PowerTools/tree/master/S3. This script also allows you to use the switch parameter –openinchrome. With this switch, the function finds all the files in the public repo, displays the urls to all the files, and opens all the files in new tabs in Google Chrome. Be careful with this function on larger S3 repositories!

 


 

 


 

That’s it! On to Level 2!