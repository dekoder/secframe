---
title: "Active Directory And AWS Security Frameworks"
description: "Dive into Microsoft Active Directory security with red first and AWS best practices"
image: "secframe_logos/11_1_20_logo_large.png"
---
## Welcome to Secframe

Thanks for taking a look at my adventure that attempts to get people and companies into a better security mindset.
 
# Increase Your Security Footprint

Conquer security challenges with best in class solutions.  **Cost effective, quick deployments** that close many of the traditional security gaps.


## Take a dip into a world of security

{{% notice tip %}}
Each section is organized with small steps to begin a phased approach into security
{{% /notice %}}

|   |    |    |
| :--: | :----: |:-------:|

## **Secure and Organize Enterprise Assets** 


|   |    |    |
| :--: | :----: |:-------:|
| [AD Threat Hunting With BadBlood]({{%relref "badblood/_index.md" %}}) | [Redforest and Microsoft ESAE]({{%relref "redforest/_index.md" %}}) | [AD Cloning]({{%relref "ad_clone/_index.md" %}}) |
| [AWS Security Pillars]({{%relref "awssecurity/_index.md" %}}) | [Blog]({{%relref "blog/_index.md" %}}) | [AD/AWS Audits]({{%relref "contact/_index.md" %}}) |


<!--https://discourse.gohugo.io/t/display-3-most-recent-blog-posts-but-not-other-pages/22264



{- range (.Paginate ( where site.RegularPages "Section" "blog" | first 3 ) )).Pages }}


{{- range ( where site.RegularPages "Section" "blog" | first 3 ) }}
---
title: "Our Story"
date: 2018-02-10T11:26:27+11:00
featured_image: "image1.jpg"
featured_image_caption: "Caption Blah Blah"
---
{{ $img := (.Resources.ByType "image").GetMatch "images/*featured*" }}
{{ with $img }}
    <img src="{{ .Permalink }}" alt="{{ $.Title }}">
{{ end }}
-->

### Contribute to this documentation
These pages are living pages.  Based on the feedback and responses, I continue to build these resources.  If something is incomplete and you want to know more about it, please let me know.  If you have input and want to be heard, feel free to reach out to me on [LinkedIn](https://www.linkedin.com/in/davidprowe/), [Twitter](https://twitter.com/davidprowe), or [Email Me]((mailto:david@secframe.com?subject=[Secframe]%20Homepage%20Requests)) to help update this content.  To help via GitHub, just click the **Edit this page** link displayed on top right of each page, and pull request it

{{% notice info %}}
Your modification to this site will be deployed automatically when merged into the master repo.
{{% /notice %}}
