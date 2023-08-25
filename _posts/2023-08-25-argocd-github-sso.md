---
layout: post
title: "Argo-CD login integration with github SSO"
comments: true
description: "Argo-CD login integration with github SSO"
keywords: "kubernetes, helm, Argo-CD, Git Hub, Cloud Native, SSO"
---
### Introduction
ဒီတစ်ပတ်တော့ မြန်မာလိုပဲ​ရေးလိုက်တော့မယ်နော်။ ဒီဟာကို တော့ organization အကြီး တွေမှာတော့ သုံးလေ့သုံးထ ရှိမရှိတော့မသိပါဘူး သူတို့မှာက LDAP လို ဟာတွေ ရှိနေတော့ LDAP မဟုတ်ရင် တစ်ခြား တစ်ခုခုသုံးကြပါလိမ့်မယ်။ အခုတော့ GITHUB ရဲ့  SSO ပဲ​ခေါ်မလား OAuth App ပဲ​ခေါ်မလား တစ်ခုခုပေါ့လေ အဲ့တာနဲ့ Argo-CD ကို ဘယ်လို လုပ်ရမလဲဆိုတာ ကို စမ်းကြည့်ကြည့်ပါမယ်။ အဲ့တော့ ပထမဆုံး Github မှာ OAuth app တစ်ခု လုပ်ရမှာပေါ့. ကျွန်တော်တို့က Personal မဟုတ်ပဲ Team အနေနဲ့ ပဲ ဆိုတော့ Github's Organization မှာပဲ လုပ်ကြည့်ကြပါမယ်။ အဲ့ဒိတော့ Github Organization တစ်ခုနဲ့ သက်ဆိုင်ရာ Team တွေ ရှိပြီးသားလို့ ယူဆလိုက်ပါမယ် ကိုယ်ဟာကိုယ်ပဲ သွားလုပ်လိုက်ကြပေါ့ လေ အဲ့ကိစ္စတွေ လုပ်ပြနေရင် အများကြီးရေးရမှာမို့ပျင်းလို့ မရေးတော့ဘူးနော်:3. 

<img src="/assets/images/argocdsso/argocd-logo.png">

### Create a new Oauth apps.
Oauth Apps တစ်ခု ဆောက်ကြည့်ရအောင်. ပထမဆုံး Github မှာ organization ဆောက်ပြီး team members တွေ ဆောက်ထားရပါမယ်။ ကျွန်တော်ကတော့ အခုလိုမျိုး လုပ်ထားလိုက်ပါတယ်။ 

```
Organization
|
|-- DevOps
    | -- devops1
    | -- devops2
|-- Dev
    | -- developer1

```
Link ကိုတော့ ဒီလို သွားလို့ရပါတယ် 

```
https://github.com/orgs/your-org-name-here/teams
```

Team ဆောက်ပြီးပြီဆိုတော့ Oauth Apps ဆီကို ဆက်သွားရအောင်. 

```
Organization setting > Developer Settings > Oauth Apps > New Org Oauth Apps
```

အောက်က link မှာလည်း organization name အစားထိုးပြီး သွားလို့ရပါတယ်.

```
https://github.com/organizations/your-org-name-here/settings/applications/new
```

အောက်က ပုံမှာလို မြင်ရပါလိမ့်မယ်။ လိုအပ်တာတွေဖြည့်လိုက်ပါ.

<img src="/assets/images/argocdsso/oauthreg.png">

Client Secret Generate လိုက်ပြီး ထွက်လာတဲ့ secret key ကို save ထားလိုက်ပါ Argo-CD ဖက်မှာ ပြန်သုံးရမှာပါ။ 

<img src="/assets/images/argocdsso/clientsctgen.png">


GitHub အပိုင်းကတော့ ပြီးသွားပါပြီ
### Argo-CD Preparation
Argo-CD helm ကို Bitnami repo က နေ pull ပြီး preparation လုပ်ကြမယ်။

ထုံးစံအတိုင်း helm repo add ပါမယ်။ ကျွန်တော်က Bitmani သုံးမှာဖြစ်လို့ bitnami repo add မယ် pull မယ်ပေါ့။

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

```bash
helm pull bitnami/argocd
```
```bash
tar -xzf argo-cd-x.x.xx.tgz
```
```bash
cd argo-cd
```
အဲ့တော့ နှစ်သက်ရာ IDE နဲ့ဆက်ပြင်ကြပါပေါ့။ 

ဘယ်နေရာတွေမှာ ပြင်ပေးရမလဲ ဆိုရင်။ အရင်ဆုံး values.yaml ထဲ dex: ကို enable:true လုပ်ပေးရပါမယ်။

server အောက်က url ကို လည်း update လုပ်ပေးရပါမယ်
```yaml
server:
  url : http://your-argocd-url
  ```
secret အောက်က extra မှာ client secret အတွက် secret ထည့်ပေးရပါမယ်။
```yaml
extra:
    dex.githubclientSecret: "<client-secret>"
```
အဲ့တာပြီရင်တော့ အောက် block ကို ရှာပြီး သက်ဆိုင်ရာ values တွေကို ထည့်ပေးလိုက်ပါ 

```yaml
  dex.config: |
     connectors:
       # GitHub example
       - type: github
         id: github
         name: github
         config:
           clientID: <client-id>
           clientSecret: $dex.githubclientSecret
           orgs:
           - name: mgou-dev
             teams:
             - DevOps
             - Dev
```
Team name တွေကို Case sensitive ရှိလို့ မှန်အောင် ပေါင်းပေးပါ ကျွန်တော် စမ်းတုန်းက DevOps ကို  devops လို့ထားမိလို့ ရွာလည်သွားသေးတယ် :3

ingress enable ဖို့လည်း မမေ့ပါနဲ့ enable လုပ်ပြီးသားဖြစ်မယ်လို့ ယူဆပါတယ်။ tls on မထားရင်လည်း ```insecure: false``` ကို true လုပ်ထားပေးပါ။

Team တွေမထည့်ပဲ organization တစ်ခု လုံးကို access ပေးမယ်ဆို ကိစ္စမရှိပေမဲ့ Team တွေထည့်မယ်ဆိုရင်တော့  Policy အတွက် configmap လုပ်ပေးရပါမယ်.

```rbac-cm.yaml``` ကို helm ထဲမှာထည့်ရေးလို့ ရပေမဲ့ ကျွန်တော်စာတွေအများကြီး မရေးချင်တော့လို့ definitioin အနေနဲ့ပဲ ထည့်လိုက်ပါတယ် :3  
ယခု policy အတိုင်းဆိုလျင်  DevOps team က admin access ရပြီး Dev Team က Read Only access ပဲ ရပါမယ်. ```mgou-dev``` နေရာမှာ ကိုယ့်ရဲ့ organization နာမည်ကို ထည့်ပေးရပါမယ်။
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    g, mgou-dev:DevOps, role:admin
    g, mgou-dev:Dev, role:readonly
```

အဲ့တာဆို Argo-CD Deploy ကြည့်ရအောင်

```bash
kubectl create ns argocd
```
```bash
helm install argo-cd . -f values.yaml -n argocd
```
```bash
kubectl apply -f rbac-cm.yaml
```

Pod အားလုံး Runပြီ ဆိုလျှင်တော့ browser မှာ ခေါ်ကြည့်ပါမယ်.Home page တက်လာပါပြီ။
<img src="/assets/images/argocdsso/argocdhomepage.png">
```Log IN VIA GITHUB``` ဆိုလျှင် Github Authentication page ကို ခေါ်သွားပြီ ပြန် permission ရမယ်ဆိုရင်တော့ login ဝင်လို့ရသွားမှာပဲ ဖြစ်ပါတယ်။

<img src="/assets/images/argocdsso/githubauth.png">

ဒီအဆင့် ပြီးသွားရင်တော့ Argo-CD ထဲ login ဝင်တာ အောင်မြင်သွားပါပြီ။ 

ပြီပါပြီး ဒါပဲ။မောတယ်...