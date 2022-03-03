---
layout: post
title: "How to Disable SSH root login"
comments: true
description: "How to disable ssh root login"
keywords: "dummy content"
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
