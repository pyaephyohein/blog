---
layout: post
title: "Deploy App in ACI using Docker Compose"
comments: true
description: "Deploy App in ACI using Docker Compose"
keywords: "docker, ACI, serverless, Azure"
author: Pyae Phyo Hein
---

Hello! Last week, We deployed Hello World app in ECS using Docker Compose. This week, Let's deploy in ACI ( Azure Container Instance). 

<img src="/assets/images/aci-dockercompose/aci_docker.png">
#### Prepare Docker context
All are the same with ECS only need to login using 
```bash 
 docker login azure
```
I will redirect to the Azure SSO. and sign in with Azure Account.

Next step is create context. 

```bash 
docker context create aci azure

# Select resource group
```
```bash
docker context ls
# to check context list
```
```bash
docker context use azure
```

***Docker Compose's integration for ECS and ACI will be retired in November 2023. You can use [compose-ecs](https://github.com/docker/compose-ecs)but I will use docker compose for now***





#### Prepare Docker Compose

```bash
git clone git@github.com:pyaephyohein/helloworld.git
```
#### Deploy 

```bash
cd helloworld
```
Now we can deploy, let us deploy with ```docker compose``` and please wait a while.

```bash
docker compose up
```

After ```docker compose up``` finished, you can check

```bash
docker compose ps

Docker Compose's integration for ECS and ACI will be retired in November 2023. Learn more: https://docs.docker.com/go/compose-ecs-eol/
NAME                COMMAND             SERVICE             STATUS              PORTS
helloworld_web      ""                  web                 Running             20.247.138.90:80->80/tcp:80->80/TCP
```
Now you can access via Public IP.  

<img src="/assets/images/aci-dockercompose/image.png">

After finished you can delete with following command.

```bash
docker compose down
```


***If you build docker image in mac ( M1, M2) please add -- platform ,```FROM --platform=linux/amd64 baseimage:tag```in ```Dockerfile```, If not you will face exec error.***


Thanks you, Please like and share my page. 