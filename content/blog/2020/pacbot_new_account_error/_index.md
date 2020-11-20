---
title: Tmobile Pacbot - Error Adding New Accounts
description: "AWS Automated Cloud Compliance Tools.  Getting PacBot to work 101"
images: "banner_magenta.png"
tags: ['AWS','security','pacbot']
date: 2020-11-19T11:16:17-04:00
chapter: false
draft: false
hidden: false
---

## Add new accounts to T-mobile's Pacbot

Tmobile's open source tool [PacBot](https://github.com/tmobile/pacbot)is an amazing AWS account audit solution.  According to [summit route's AWS security tool comparison](https://summitroute.github.io/aws_research/security_tool_comparison.html), Pacbot is one of the best open source tools on the open market.

Setting the infrastructure up is easy, as I followed the installation notes.  I deployed PacBot onto an AWS Ec2 Linux machine.  Here are the installation steps to automate the deployment:

```
sudo yum install git
git clone https://github.com/tmobile/pacbot.git
sudo amazon-linux-extras install epel
sudo yum install -y python3-pip
sudo yum -y install java-1.8.0-openjdk docker maven unzip mysql
sudo systemctl start docker
wget https://releases.hashicorp.com/terraform/0.11.11/terraform_0.11.11_linux_amd64.zip
unzip terraform_0.11.11_linux_amd64.zip
sudo mv terraform /usr/bin/
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -
sudo yum install -y nodejs
sudo npm install yarn -g
npm install -g @angular/cli
npm install -g bower
cd pacbot/
cd installer/
sudo pip3 install -r requirements.txt
```


Next I updated the settings/local.py file with the required values per the instructions on github:
   - VPC ID
   - VPC CIDR 
   
I deleted the angular necessity from `settings/common.py` since it was installed and keeps failing the check during the installation.

I also wanted to include multiple cidr blocks to allow access into my Pacbot ELB URL, so I opened the file 
```install/core/providers/aws/validate.py```
On lines 39/44 I changed the value to  `return K.VALID`.  I know the CIDR's I entered on my local.py file were correct, and I wanted the tool to deploy a security group that allowed cross CIDR access.

Attach IAM admin role to the instance and deployed the too using  `sudo python3 manage.py install`

---

### Adding new accounts

Once I got into the realm of adding new accounts, I followed the instructions on the [install page](https://github.com/tmobile/pacbot/wiki/Install)

However, no matter how long I waited for the account to show up in Pacbot, the accounts never were audited.  Jump into troubleshooting:

The logs for the resource collection job is in the AWS Batch under the AWS-Data-Collector-job. There is cloudwatch logs for the job after querying the cloudwatch logs I found the following error code:

{{< display_hor >}}

```
2020-11-19 15:06:23 [main] ERROR c.t.c.p.i.file.AssetFileGenerator - {"errcode":"NO_CRED" , "account":" 6ACCOUNTNUMBER6", "Message":"Error getting credentials for account 6ACCOUNTNUMBER6" , "cause":"1 validation error detected: Value 'pic-ro- 6ACCOUNTNUMBER6' at 'roleSessionName' failed to satisfy constraint: Member must satisfy regular expression pattern: [\w+=,.@-]* (Service: AWSSecurityTokenService; Status Code: 400; Error Code: ValidationError; Request ID: 729f305c-f0f0-4604-95d6-56b6474e1a32)"}
```
I didnt know why the 'roleSessionName' valued at 'pic-ro- ACCOUNTNUMBER' had a space in it.  I dug into the code, and didnt find anything useful.  I looked at the Cloudwatch target on the pacbot-AWS-Data-Collector rule.  The target value on the key 'accountinfo' was set to the following json:

### This Fails.

```json
{
    "encrypt": false,
    "key": "accountinfo",
    "value": "3BASEACCOUNT3, 6CLIENTACCOUNT6"
  }

```

I changed it to the following to make it work, and my data collection began at the next 6 hours cycle.

### This Works.

```json
{
    "encrypt": false,
    "key": "accountinfo",
    "value": "3BASEACCOUNT3,6CLIENTACCOUNT6"
  }


```

> Notice I removed the space after the comma on the ‘value’ attribute.

Hope this helps you get into a world of automated cloud compliance!