---
title: Admin OU
weight: 25
---
Today's topic to answer:

Q: How do you begin to standardize administrator accounts and groups?
A: By controlling where they reside. Putting them in a safe location.

Privileged Account Management

It's difficult to find all the pieces and combine them together. First you need to start with the foundation.

Admin OU Scripts / PAW Scripts

From the link above, download the PAW scripts provided in the technet article.

There is only one file from this zip you care about for this exercise:

Create-PAWOUs.ps1
 Admin OU Scripts PAW and PAM

 

 

The result from running the script is an OU at the top of the domain named "Admin." This structure is where you will keep all administrators from now on.

Period.
No exceptions.
Don't forget to congratulate yourself after you run this script in your environment.

Once you've made this administrative OU structure, you can begin to move your administrators and administrative groups into it. This is the first step to securing your domain.

 

You've built the foundation of your privileged account security.

 You gave privileged credentials a home in your environment.

 

This was the easiest thing you've ever done to secure your environment. It was free, quick, and easy.

 
Next you'll have to dive into your environment and begin to move all your administrators into this Admin OU structure. You will have to get a proper definition: who or what an administrator?

 

For this you'll need to understand the Microsoft tiered definitions

 

What is Tier 0?

What is a tier 0 device?

Tier 0 administrator?

Tier 0 group?

What is Tier 1?

What is a tier 1 device?

Tier 1 administrator?

Tier 1 group?

What is Tier 2?

What is a tier 2 device?

Tier 2 administrator?

Tier 2 group?

 

You'll learn more about the tiers in the tier definition post coming up. For now, begin planning new and existing administrators into this Admin OU structure, and you can arrange them in sub OUs later.

 

The script linked in the technet article is really great. If you prefer to manually create OUs instead of running the script, follow the outline below.

 Step 1: Create a new OU under the root of the domain named "Admin"
Step 2: open up the security properties on the OU and disable the security inherited permissions
Step 3: remove any groups or users that are not the default list below :
Step 4: create 3 ous under the admin OU: Tier 0, Tier 1, Tier 2
Step 5: Create OUs under each tier OU named:
T#-Accounts
T#-Devices
T#-Permissions
T#-Roles
T#-Servers
(Where # is the number of the tier OU you are under)

Step 6: move these default built in groups into this admin OU

For more information on the paw script, don't hesitate to read:

https://www.petri.com/keep-active-directory-secure-using-privileged-access-workstations

 

If you need more assistance with identifying access across your organization, please reach out.
