---
layout: post
title: "Nexus Repository Manager as Caching Docker Registry Proxies"
date: 2024-04-13 09:00:00 +0700
tags: [Nexus, Sonartype, K8s, Docker, DevOps]
reading_time: 5
image: /assets/images/nexus-repo-mgr/logo.png
---

ဒီတစ်ခေါက်တော့ မြန်မာလို့ပဲ ရေးလိုက်တော့မယ်နော်။ ကျွန်တော် တို့  Docker hub ကနေ image တွေ pull မယ်ဆိုရင် [download rate limit](https://docs.docker.com/docker-hub/download-rate-limit/) ကြောင့် အဆင်မပြေတာမျိုး ကြုံဖူးကြမယ် ထင်ပါတယ် အဲ့ပြဿနာကို ဖြေရှင်းဖို့အတွက် ကျွန်တော်တို့က  Docker Hub user account ကို သုံးတာမျိုး, Docker Hub မှာ Subscription ဝယ်သုံးတာမျိုးလုပ်ကြပါတယ်. အဲ့နည်းလမ်းတွေ ကို မသုံးပဲ Docker Image တွေကို chache မှတ်ထားပြီး မှတ်ထားတဲ့ server ထဲကနေ နောင်ထပ်တစ်ခါ pull တဲ့ အခါ cache server ကနေပဲ pull အောင်လည်း လုပ်ကြပါတယ်။ အခု Post မှာတော့ Sonartype ရဲ့ Nexus Repository Manager ကို သုံးပြီး စမ်းကြည့်ကြပါမယ်။ ကျွန်တော် manual ကို docker နဲ့ ပဲ လုပ်ပြသွားပါမယ်, manual မြင်ပြီးဆိုရင်တော့ k8s မှာ Containerd ပဲ ဖြစ်ဖြစ် တစ်ခြား run time ပဲ ဖြစ်ဖြစ် စမ်းကြည့်ပြီး automation တစ်ခုခုကို သုံးပြီးတော့လည်း Integration စမ်းကြည့်ကြည့်လို့ရပါတယ် 


ကျွန်တော်တို့ ပထမဆုံး Nexus Repository Manger ကို Local မှာပဲ စမ်းမှာ ဖြစ်လို့ docker compose သုံးပြီး run လိုက်ပါမယ်။ Kubernetes ပေါ်မှာဆိုရင် Helm သုံးပြီးပဲ ဖြစ်ဖြစ် VM ပေါ်မှာပဲ ဖြစ်ဖြစ်  Deploy လို့လည်း အဆင်ပြေပါတယ်။ Docker compose file ကတော့ ဒီတိုင်းပါပဲ
```bash
version: "3"
services:
  nexus:
    image: sonatype/nexus3
    restart: always
    volumes:
      - "nexus:/sonatype-work"
    ports:
      - "8081:8081"
      - "8088:8088"
volumes:
  nexus: {}
```

Port ```8081``` က GUI အတွက် ဖြစ်ပြီး ```8088``` ကိုတော့ Docker Registery အတွက် သုံးပါမယ်။

Docker compose ကို သုံးပြီး Run လိုက်ပါမယ်

```bash
docker compose up
```
server init လုပ်တာ ခဏ စောင့်ရပါမယ်
ခဏ စောင့်ပြီးရင် ```[vm-ip]:8081``` ကို ခေါ်ကြည့်မယ်ဆိုရင် UI တက်လာပါလိမ့်မယ်

<img src="/assets/images/nexus-repo-mgr/login.png">

အဲ့ဒီမှာ generated password ကို ကြည့်ဖို့ အတွက် Docker container ထဲ ဝင်ဖို့ ```docker exec``` သုံးရပါမယ်။

```bash
docker exec -it [container ID] cat /nexus-data/admin.password
```
Password ရလာပါလိမ့်မယ်

username - admin
password - 

နဲ့ login ဝင်ပြီးသွားရင် လုပ်စရာ ရှိတာ ဆက်လုပ်လို့ရပါပြီ။

ပုံထဲ က ပြထားတဲ့ အတိုင်း ဆက်သွားရင် Docker Proxy Repo ကို ရပါမယ်
<img src="/assets/images/nexus-repo-mgr/create_repo.png">

အခု တစ်ဆင့်မှာလည်း ပုံမှာ ပြထားတဲ့ အတိုင်းပဲ လိုက်ဖြည့်ပါမယ် Repo Name မှာတော့ အဆင်ပြေတာထည့်လို့ရပါတယ်။

<img src="/assets/images/nexus-repo-mgr/repo_config.png">!

Remote storage မှာ https://registry-1.docker.io သုံးပါမယ်။ တစ်ခြား self-hosted register တွေမှာသုံးချင်ရင်လဲ ထိုနည်း အတိုင်းပါပဲ။
HTTP port ကို မထည့်ပါနဲ့ဦး

ခု Docker Group ဆောက်ရပါမယ်။ အောက်က အတိုင်းပဲ စမ်းကြည့်လိုက်ပါ။ 

<img src="/assets/images/nexus-repo-mgr/docker_group.png">

Docker Proxy ကို  member add ပေးရပါမယ်

<img src="/assets/images/nexus-repo-mgr/docker_group.png">

အခုဆို စမ်းသုံးလို့ ရပြီထင်ပါတယ်

Local machine ရဲ့ /etc/docker/daemon.json မှာ
nexus ရဲ့ docker proxy address ထည့်ကြည့်ပါမယ်

```
Host's ip address =  13.250.111.79
Docker Group's Port = 8088
```

အဲ့လို ဖြစ်လို့ အောက်ပါအတိုင်း ထည့်လိုက်ပါတယ်, local DNS ဒါမှ မဟုတ် DNS တစ်ခုခုနဲ့လည်း bind ပြီးမှ ထည့်လို့ရပါတယ်။
```
example docker-proxy.example.com:8088
```

```bash
vim /etc/docker/daemon.json

{
  "insecure-registries": [
    "13.250.111.79:8088"
  ]
}
```
docker service ကို restart လုပ်ပေးရပါမယ်

```bash
sudo systemctl restart docker.service
```
Nexus UI ထဲကို သွားကြည့်မယ် ဆိုရင် docker image's cache တွေ ရှိဦးမှာ မဟုတ်ပါဘူး


<img src="/assets/images/nexus-repo-mgr/browse_proxy.png">

<img src="/assets/images/nexus-repo-mgr/browse_proxy2.png">

အဲ့ဒါဆိုရင် အခု docker pull စမ်းကြည့်ကြမယ်

```bash
docker pull 13.250.111.79:8088/alpine

### OUTPUT ###
Using default tag: latest
latest: Pulling from alpine
Digest: sha256:c5b1261d6d3e43071626931fc004f70149baeba2c8ec672bd4f27761f8e1ad6b
Status: Image is up to date for 13.250.111.79:8088/alpine:latest
13.250.111.79:8088/alpine:latest
```

```bash
docker images
### Output ###
REPOSITORY                  TAG       IMAGE ID       CREATED        SIZE
13.250.111.79:8088/alpine   latest    05455a08881e   2 months ago   7.38MB
```

Nexus UI ကို သွားကြည့်လိုက်ရင် အောက်က ပုံအတိုင်း cached ထားတဲ့ registry ကို တွေ့ရမှာ ဖြစ်ပါတယ်

<img src="/assets/images/nexus-repo-mgr/cached.png">

ဒီတစ်ခေါက်တော့ ဒီလောက်ပါပဲ 
နောင် post တွေကျရင် k8s နဲ့ ဘယ်လို တွဲသုံးသင့်လဲ​ဆိုတာကို ရေးဖို့ စဥ်းစားမိထားပါတယ်။

ကျေးဇူးတင်ပါတယ်
