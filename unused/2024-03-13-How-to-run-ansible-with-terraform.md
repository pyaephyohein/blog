---
layout: post
title: "Run Ansible with Terraform"
comments: true
description: "Run Ansible with Terraform"
keywords: "Terraform, Ansible, IaC"
author: Pyae Phyo Hein
---

Hello LTNS, sometime we have to run some script in the linux host via ssh. In this case, Ansible is easy to undersand and easy to mananage. So most of the Infra Engineer and DevOps Engineers use Ansible for Configuration as Code. Terraform is the Best of IaC for me. It is support different  types of Cloud Providers like AWS, GCP, Azure, GCP. Some time we have to install / configure some server like webserver, vpn server and other traditional linux servers in the cloud infrastructure. We can use these two ( ansible and terraform ) for this state.

#### Pre-Request
1. Terraform
2. Ansible
3. AWS


Everybody know ```inventory``` file is the list of hosts and group, it include host names, SSH Information like ssh username, password and ssh key file path. We need to generate as dynamic file by Terraform.

File Tree
```bash
.
..
|-- Ansible
|-- |-- i
|-- Terraform
|   |-- main.tf
|   |-- variables.tf
|   |-- providers.tf
`-- readme.md

```
.....