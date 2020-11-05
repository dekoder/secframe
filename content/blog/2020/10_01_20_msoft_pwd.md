---
title: "Microsoft's Policy on Password Expiration"
date: 2020-10-01T16:47:17-04:00
description: does this show up too
draft: false
---

There are multiple recommendations floating in security discussion boards on password policies. Some people recommend rotating passwords, which was a NIST recommendation in the years past. NIST has recently appended their password recommendations to remove recommending the expiration of passwords and password composition rules. Microsoft maps their policy to the recommendations of NIST. Microsoft no longer recommends forcing the change or rotation of users' passwords.

PIC 1
Microsoft Password Policy

source: https://mediusprodstatic.studios.ms/presentations/Ignite2018/THR3041.pptx

Microsoft recommends adopting the following modern password policy based on NIST guidance:
From Microsoft's 5 Steps to Secure Your Identity Infrastructure:

Require passwords have at least 8 characters. Longer isn't necessarily better, as they cause users to choose predictable passwords, save passwords in files, or write them down.
Disable expiration rules, which drive users to easily guessed passwords such as Spring2019!
Disable character-composition requirements and prevent users from choosing commonly attacked passwords, as they cause users to choose predictable character substitutions in passwords.
Microsoft is heavily pushing "multi-factor" on all user accounts.  The term "Multi-factor" is noted 8 times on the 5 Steps to Secure Your Identity Infrastructure article.

Password expiration requirements for users:

Source: Password Policy Recommendations For Office 365

Password expiration requirements do more harm than good, because these requirements make users select predictable passwords, composed of sequential words and numbers which are closely related to each other. In these cases, the next password can be predicted based on the previous password. Password expiration requirements offer no containment benefits because cyber criminals almost always use credentials as soon as they compromise them.
Enforce Multi-Factor Authentication registration
Make sure your users update contact and security information, like an alternate email address, phone number, or a device registered for push notifications

Enable MFA
MFA ensures that when a system detects suspicious activity, it can challenge the user to ensure that they are the legitimate account owner.

PIC 2
 

From https://pages.nist.gov/800-63-FAQ/#q-b5

Q-B5: Is password expiration no longer recommended?

A-B5: SP 800-63B Section 5.1.1.2 paragraph 9 states:

“Verifiers SHOULD NOT require memorized secrets to be changed arbitrarily (e.g., periodically). However, verifiers SHALL force a change if there is evidence of compromise of the authenticator.”
Users tend to choose weaker memorized secrets when they know that they will have to change them in the near future. When those changes do occur, they often select a secret that is similar to their old memorized secret by applying a set of common transformations such as increasing a number in the password.

Password expiration, get rid of the idea.
MFA, adopt that.