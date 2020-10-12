---
author: Andrew Cole
date: 2018-09-27
category:
- geek
title: Modern Infrastructure
draft: True
---

Wherein I drink the Microsoft Kool Aid and change my entire web infrastructure.

\n

<span id="\&quot;more-147\&quot;"></span>

\n

Since I last wrote on this blog the infrastructure hosting my web services has changed several times. The current configuration is a bunch of docker services running on a Digital Ocean Droplet, with a bunch of Ansible stuff keeping it all together and running.

\n

The same Ansible layout runs my infrastructure at home, which is a three node Docker Swarm cluster running on a series of Intel NUCs.

\n

Recently, with the purchase of a Visual Studio subscription (which I acquired mainly for the development licences it gave me for my home lab), I received a monthly credit for Azure, Microsoft's cloud service. I decided to utilise this for hosting my web services.

\n

Rather than set up a Linux virtual machine I've decided to run something a bit more modern. With my trip to Microsoft Ignite in Orlando, Florida this week, I've well and truly drunk the Microsoft kool-aid. The most interesting release I learned about this week was Azure DevOps, which is the rebadging of Visual Studio Team Studio.

\n

This will be a series of posts detailing my migration to a modern infrastructure for my web services. Current plans include:

\n

*   Azure DevOps continuously deploying an an Azure Resource Manager (ARM) template as Infrastructure as Code, deploying

*   An Azure Kubernetes Service (AKS) cluster, hosting

*   A series of docker containers:

*   Built utilising Azure DevOps

*   Stored in an Azure Image Repository

*   Deployed to AKS with a Helm chart

\n

Wish me luck!