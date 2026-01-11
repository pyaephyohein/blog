---
layout: post
title: "How to Disable SSH root login"
date: 2022-03-03 09:00:00 +0700
categories: tech
tags: [SSH, Linux]
reading_time: 3
image: /assets/images/default-banner.png
---

### Configure, disable ssh root login.

``` bash
sudo vim /etc/ssh/sshd_config
```
change as follow line
```bash
PermitRootLogin no
```
```bash
sudo systemctl restart sshd.service
```
