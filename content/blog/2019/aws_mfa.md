---
title: Enable AWS MFA in 10 Steps (With Pictures)
date: 09-12-2019
aliases: 
    - enable-mfa-on-aws-in-10-steps-with-pictures
---

In Amazon's IAM Best Practices guide, the recommendation for MFA states: 
 
> We recommend that you require multi-factor authentication (MFA) for all users in your account.
MFA is often used as a stop gap to stop malicious attacks.  Knowing that the identity is the first security boundary to your AWS environment, please ENABLE MFA for all users in your account.  By enabling MFA you can assure that the administrators logging into the AWS console, are authorized to do so.
 
To enable MFA, you only need to follow 10 steps:
1. Log into AWS console
![aws mfa enable iam user](/blog/2019/images/awsmfa1.png?classes=shadow&width=40pc)
2. Click on your user at the top right.  In the drop down menu click My Security Credentials
![aws mfa enable iam user](/blog/2019/images/awsmfa2.png?classes=shadow&width=40pc)
3. Scroll down on the next page
![aws mfa enable iam user](/blog/2019/images/awsmfa3.png?classes=shadow&width=40pc)

4. Click "Assign MFA device"
![aws mfa enable iam user](/blog/2019/images/awsmfa4.png?classes=shadow&width=40pc)
    - If you already have an MFA device attached you will see a different button titled "Manage MFA device"  You can remove the device on file, and add a new device with this button
    ![aws mfa enable iam user](/blog/2019/images/awsmfa41.png?classes=shadow&width=40pc)

5. Choose your MFA device. Click Continue
![aws mfa enable iam user](/blog/2019/images/awsmfa5.png?classes=shadow&width=40pc)
> Many free apps on iphone and android work for "Virtual MFA Device".  I use Google Authenticator (Play Store, Apple Store), and Duo (Play Store, Apple Store) for my MFA adventures

6. Click show QR code
![aws mfa enable iam user](/blog/2019/images/awsmfa6.png?classes=shadow&width=40pc)

7. Open your MFA application on your mobile device and scan the code in to add the account. Scanning the barcode will add the AWS application to your MFA application
![aws mfa enable iam user](/blog/2019/images/awsmfa7.png?classes=shadow&width=40pc)
    - For a full DUO guide on adding an application, [visit here](https://guide.duo.com/third-party-accounts)

8. Scroll down in the MFA window, and enter the first code you see in your mobile phone application into the box "MFA Code 1"
![aws mfa enable iam user](/blog/2019/images/awsmfa8.png?classes=shadow&width=40pc)

9 . Wait ~30 seconds.  Enter the second code you see into box "MFA Code 2"

![aws mfa enable iam user](/blog/2019/images/awsmfa9.png?classes=shadow&width=40pc)

10. Click the blue button "Assign MFA"

That's it! 

Next time you log into AWS your account will prompt for MFA


If you want to discuss what benefits you might get from an AWS security roadmap please continue reading with my [AWS Security Guides](/aws_security) or please connect with me.