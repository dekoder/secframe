---
title: Bloodhound in Docker in a Browser. Oh My
description: A prepackaged deployment of Bloodhound and Neo4j.  Get on your testing feet in no time with this deployment.
date : 2020-09-24T00:00:00+04:00
lastmod : 2020-11-10T00:00:00+04:00
tags: ['Bloodhound','Active Directory']
aliases:
    - /blog/bloodhound-in-docker-in-the-browser.-oh-my
---
A while back I posted a tweet of me running the Active Directory penetration testing tool "Bloodhound" in a web browser. To put it in kind terms, I simply forgot I did that… forgot to write a post about it... it left my brain entirely.

I never shared the details about it. And then Covid happened (is happening) and it put my writing days into a dark abyss. A place where I felt like writing and reading security tools were things no one should be worrying about. I decided to swim above the water, take a breath, and get back into this experimentation stuff.

 
{{< tweet 1284195848562388992 >}}
 


 

When I surfaced from managing the stress from this public health crisis, I scooped into the back of my memories. What I remembered was that I created a wild experiment that for the most part worked. It has its quirks. True. But the proof of concept is solid enough for a blog.

 

Fortunately for me and my lack of documentation on the subject, the deployment I created is a docker compose file. Meaning, I didn't have to remember how I did any of it. Because all the settings, images, and software is bundled in the compose and docker files, it works whenever it is run. After months of not doing anything with this side project, I spun it back up with no issue at all, well, except for random quirks

 

For this post, I'm going to assume you know what Bloodhound is, and what neo4j is and what docker is. This docker-compose project creates two simple containers,

A standard neo4j container with a few tweaks for performance because the are used in conjunction with…
A complete desktop version of Bloodhound, running on the web, running on Ubuntu.

#### Who does number two work for?
Forgive me my Austin Powers line… number two, the bloodhound deployment.... It's a pre bundled desktop version of Bloodhound running on Ubuntu, compiled in combination with tightvnc, ratpoison and some other tools that allow bloodhound to be run in your browser.

The full desktop app, running on your web browser. You can even upload a sharp hound domain dump into it. New Englanders say 'Wicked!' for this sort of techie invention.

 

A bigger goal I have is to convert the electron package that bloodhound is, into a pure node and webpack web app. In light of me not doing that yet, because I have a full time job, I managed to package Bloodhound and neo4j into a self contained docker cluster.

 

This docker cluster hosts the full compiled application in a web browser via VNC and connects to the neo4j docker server over the docker network. What this does is allows anyone on any operating system to get an insanely quick deployment of Bloodhound running on any system.

Steps::

1. unzip bh/Bloodhound/Bloodhound.zip into the bh/Bloodhound directory (sorry for this step, github threw me a size limit issue on this unix executable)
1. Docker compose up (with the source docker yml and docker files)
1. Open web app to the URL.
1. Log into VNC with the password default: 'thispassword'
1. Log into Bloodhound: password default: BLOOD
1. Upload sharphound.zip directory data
1. Profit! $$$$
 

Let me know you like this work by retweeting this article, or follow me on twitter!

### Here's a set of pictures of the docker containers in action so you know what you're getting into.
A quick “docker-compose up --build” while in the directory. After a few minutes of deployment of the docker container, the ubuntu image via tightvnc and neo4j are available

neo4j bloodhound active directory docker
Navigate to localhost:9000 to see the tightvnc server. (This port number is specified in the docker-compose.yml file on line 14.) Log in with the password ‘thispassword’. (this password is specified in line 3 on the bh/dockerfile)

 

neo4j bloodhound active directory docker
 

Click in the neo4j url bar, then click out. (It's a weird quirk with the Bloodhound GUI here) Bloodhound then recognizes that you are pointing to the neo4j container and should give you a little green check box.

 

 

neo4j bloodhound active directory docker
 

Once you are in, it’s time to upload some data! In this repo, I put a demo domain zip file 20200721134333_BloodHound.zip in the Bloodhound folder.

neo4j bloodhound active directory docker
This screenshot you can see that I can upload it to the web app.

 

neo4j bloodhound active directory docker
 

A quick peak at the files processing and uploading into the BH container.

neo4j bloodhound active directory docker
 

Once the zip is done processing, you can click away!

neo4j bloodhound active directory docker
How about shortest paths to domain admins?

neo4j bloodhound active directory docker
 

The app says it's going to take a while to draw. Since this container and the neo4j container are designed to do one thing, the processing is done in a flash.

neo4j bloodhound active directory docker
 

You can click, and move stuff around like its installed on your machine.

neo4j bloodhound active directory docker
 

 

It was a fun little side project that helped me get familiar with docker pieces. I hope you enjoy it. I don't plan on supporting the code, but i’ll put it up on github. You can download the code here: Github

Due to size limits on github, you have to unzip bh/Bloodhound/Bloodhound.zip so that the docker image can call on the Unix executable Bloodhound. I'll fix that in some spare time...