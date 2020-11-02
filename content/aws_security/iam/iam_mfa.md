---
title: Enable AWS MFA in 10 Steps (With Pictures)
weight: 10
aliases:
    - blog/enable-mfa-on-aws-in-10-steps-with-pictures/
---
Enable AWS MFA in 10 Steps (With Pictures)
 

In Amazon's IAM Best Practices guide, the recommendation for MFA states: 
 
We recommend that you require multi-factor authentication (MFA) for all users in your account.
MFA is often used as a stop gap to stop malicious attacks.  Knowing that the identity is the first security boundary to your AWS environment, please ENABLE MFA for all users in your account.  By enabling MFA you can assure that the administrators logging into the AWS console, are authorized to do so.
 
To enable MFA, you only need to follow 10 steps:
Log into AWS console
AWS Security MFA 0
Click on your user at the top right.  In the drop down menu click My Security Credentials

AWS Security MFA 1

Scroll down on the next page
AWS Security MFA 2

Click "Assign MFA device"
AWS Security MFA 4
If you already have an MFA device attached you will see a different button titled "Manage MFA device"  You can remove the device on file, and add a new device with this button
AWS Security MFA 3

Choose your MFA device. Click Continue
AWS Security MFA 5
Many free apps on iphone and android work for "Virtual MFA Device".  I use Google Authenticator (Play Store, Apple Store), and Duo (Play Store, Apple Store) for my MFA adventures

Click show QR code
AWS Security MFA 6

Open your MFA application on your mobile device and scan the code in to add the account. Scanning the barcode will add the AWS application to your MFA application
AWS Security MFA 7
For a full DUO guide on adding an application, visit here

Scroll down in the MFA window, and enter the first code you see in your mobile phone application into the box "MFA Code 1"
AWS Security MFA 8a

Wait ~30 seconds.  Enter the second code you see into box "MFA Code 2"

AWS Security MFA 8

Click the blue button "Assign MFA"

That's it! 

Next time you log into AWS your account will prompt for MFA

AWS Security MFA 0
 

AWS Security MFA 10
Note* Enabling MFA in this method does not automatically enable MFA on CLI Access.