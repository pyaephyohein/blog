---
layout: post
title: "Deploy App in ECS using Docker Compose"
date: 2023-09-06 09:00:00 +0700
tags: [Docker, ECS, Serverless]
reading_time: 5
image: //assets/images/ecs-dockercompose/dockerecs.jpg
---

Hello, One Week again!, Let's Deploy App in ECS. If you want to test your app or you don't want to deploy EC2, you can use ECS with Docker Compose. So you need IAM key and secret. 

***Docker Compose's integration for ECS and ACI will be retired in November 2023. You can use [compose-ecs](https://github.com/docker/compose-ecs)but I will use docker compose for now***

<img src="/assets/images/ecs-dockercompose/dockerecs.jpg">

#### Prepare Docker context

Firstly, you need to crate `docker context`, I will show step by step.

```bash
docker context create aws-ecs
```
I need to ceate new key and secret. If you already have aws profile you can use ```An existing AWS Profile``` or You can expose variables to use ```AWS environment variables``` 
```bash
? Create a Docker context using:  [Use arrows to move, type to filter]
  An existing AWS profile
> AWS secret and token credentials
  AWS environment variables
```
Please fill with your AWS secret and token credentials. now you have finished creating docker context. let's check that. 
```bash
docker context ls
```
It will show like that.
```bash
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                      KUBERNETES ENDPOINT   ORCHESTRATOR
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock
desktop-linux *     moby                Docker Desktop                            unix:///Users/pyaephyohein/.docker/run/docker.sock
aws-ecs    ecs

```
To use this context

```bash
docker context use aws-ecs

aws-ecs
Current context is now "aws-ecs"
```
So, you can use this context. Let's try!. You can use your own app or my docker app. 

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

Docker Compose's integration for ECS and ACI will be retired in November 2023. Learn more: https://docs.docker.com/go/compose-ecs-eol/
WARNING services.scale: unsupported attribute
[+] Running 5/10
 ⠼ helloworld             CreateInProgress User Initiated                                                                                                               16.4s
 ⠿ Cluster                CreateComplete                                                                                                                                 5.0s
 ⠿ DefaultNetwork         CreateComplete                                                                                                                                 5.0s
 ⠿ LogGroup               CreateComplete                                                                                                                                 2.1s
 ⠼ WebTCP80TargetGroup    CreateInProgress Resource creation Initiated                                                                                                  14.4s
 ⠼ CloudMap               CreateInProgress Resource creation Initiated                                                                                                  14.4s
 ⠼ WebTaskExecutionRole   CreateInProgress Resource creation Initiated                                                                                                  14.4s
 ⠿ Default80Ingress       CreateComplete                                                                                                                                 1.0s
 ⠸ LoadBalancer           CreateInProgress Resource creation Initiated                                                                                                   8.4s
 ⠿ DefaultNetworkIngress  CreateComplete                                                                                                                                 1.0s
```

In aws's ecs page, you can see like the following image. 

<img src="/assets/images/ecs-dockercompose/image.png">

After ```docker compose up``` finished, you can check
```bash
docker compose ps

Docker Compose's integration for ECS and ACI will be retired in November 2023. Learn more: https://docs.docker.com/go/compose-ecs-eol/
NAME                                               COMMAND             SERVICE             STATUS              PORTS
task/helloworld/45e8c96dbdba455b9614bd938804e42e   ""                  web                 Running             hello-LoadB-7ODE14P5RZ59-1698565622.ap-southeast-1.elb.amazonaws.com:80:80->80/http
```
Now We can access port(load-balancer dns). 

<img src="/assets/images/ecs-dockercompose/image1.png">

After finished you can delete with following command.

```bash
docker compose down
```
If Above command is too slow, you can delete in aws console :3 ***don't stop docker compose down***. 

***If you build docker image in mac ( M1, M2) please add -- platform ,```FROM --platform=linux/amd64 baseimage:tag```in ```Dockerfile```, If not you will face exec error.***


Thanks you, Please like and share my page.
