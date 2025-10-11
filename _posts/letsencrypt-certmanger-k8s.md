---
layout: post
title: "SSL Certificate with Let's Encrypt and Cert-Manager in K8s"
date: 2023-09-02 09:00:00 +0700
tags: [Kubernetes, Helm, Let's Encrypt, SSL, Cert-Manager]
reading_time: 5
image: /assets/images/jekyll-banner.png
---

### Intro
ဒီတစ်ပတ်မှာတော့ let's encrypt နဲ့ cert-manager ကို တွဲသုံးပြီး ဘယ်လိုမျိုး SSL သုံးလို့ ရမလဲဆိုတာ စမ်းကြည့်ကြမယ်ဗျို့။ ပုံမှန်ဆိုရင် Self-signed certificate ကို သုံးတတ်ကြပေမဲ့ certificate ထုတ်ရတာ ပျင်းတာပဲ ဖြစ်ဖြစ် ဒီတိုင်း စမ်းကြည့်ချင်တာပဲ ဖြစ်ဖြစ် organization က သုံးချင်တာပဲ ဖြစ်ဖြစ် lets encrypt ကို clusterissure အဖြစ် သုံးပြီး certificate request ပြီးသုံးကြတယ်ပေါ့။

<img src="/assets/images/letencrypt-certmgr/cert_let.png">

### Deploy Cert-manager
အဲ့တော့ ပထမဆုံး ကျွန်တော်တို့ Cert-manager deployment လုပ်ကြမယ်။ Cert-manager ကိုတော့ helm သုံးပြီး deploy လုပ်ကြပါမယ်။

```bash
helm repo add jetstack https://charts.jetstack.io
```
```bash
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.12.0 \
  --set installCRDs=true
```
ဒါဆိုရင် Cert-manager deploy လုပ်လို့ပြီးသွာပါပြီ။ ဒါပြီးရင် Lets encrypt ClusterIssuer ထည့်ပေးရပါမယ်။

ကျွန်တော်တို့က Lets encrypt ရဲ့ stag နဲ့ prod env နှစ်ခုစလုံး စမ်းသုံးမှာဆိုတော့ နှစ်ခုလုံးကို create ပေးဖို့လိုပါတယ်။ ပုံမှန်ဆိုရင်တော့ stag နဲ့ prod က env သပ်သပ်ဆီဆိုတော့ တစ်ခုဆိုရပါပြီ။
  

### Deploy Let's Encrypt
letsencrypt-clusterissuer-stag.yaml

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-stag
  annotations:
      "helm.sh/hook": post-install

spec:
  acme:
    # The ACME server URL
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: <your-email-address>
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt-stag
    # Enable the HTTP-01 challenge provider
    solvers:
    - http01:
        ingress:
          ClassName: nginx
```
```bash
kubectl apply -f letsencrypt-clusterissuer-stag.yaml
```

letencrypt-clusterissuer-prod.yaml

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
  annotations:
      "helm.sh/hook": post-install

spec:
  acme:
    # The ACME server URL
    server: https://acme-v02.api.letsencrypt.org/directory
    # Email address used for ACME registration
    email: <your-email-address>
    # Name of a secret used to store the ACME account private key
    privateKeySecretRef:
      name: letsencrypt
    # Enable the HTTP-01 challenge provider
    solvers:
    - http01:
        ingress:
          ClassName: nginx
```
```bash
kubectl apply -f letencrypt-clusterissuer-prod.yaml
```
***Optional: Cert-manager deploy တဲ့အချိန် တစ်ခါထဲပါသွားချင်ရင်တော့ ```helm pull ```ပြီး အထက်က  Definition Files တွေကို Cert-manager Chart ရဲ့ Templates Directory ထဲ ထည့်ပေးလိုက်ပြီး ```helm install``` လိုက်ရင် ရပါပြီ။ ```apiVersion: cert-manager.io/v1``` က CustomResources ဖြစ်တဲ့အတွက် cert-manager တစ်ခုလုံး helm install ပြီးမှ ClusterIssuer add လို့ရမှာဖြစ်တဲ့အတွက် helm post-install annotation ကြေညာပေးဖို့ လိုပါတယ် မဟုတ်ရင် unknown resource error တတ်ပါလိမ့်မယ်။***
```yaml
annotations:
      "helm.sh/hook": post-install
```

အဆင်ပြေတဲ့နည်းလမ်းသုံးကြပါ အဆင်ပြေပါတယ်။

### Deploy Hello World App
အဲ့တော့ ကျွန်တော်တို့ App တစ်ခု စမ်းပြီး Deploy ကြည့်ရအောင်။
ကျွန်တော်က hello world app တစ်ခု ကို ရှာပြီး စမ်းပြထားပါတယ်။

```bash
helm repo add giantswarm https://giantswarm.github.io/giantswarm-catalog
```
```bash
helm pull giantswarm/hello-world
```
```bash
tar -xzf hello-world**.tar.gz
```
```bash
cd hello-world
```

အဆင့်သင့်ဖြစ်ပြီဆိုတော့ အဆင်ပြေတဲ့ IDE နဲ့ Update လုပ်လိုက်ပါ။( ဒီ chart မှ policy error ရှိနေလို့ templates ထဲက policy file ကို delete ပစ်လိုက်ပါတယ် လိုက်လုပ်ကြည့်ရင်း တိုင်ပတ်နေကြမှာဆိုးလို့ နောင်မှ သပ်သပ် helloworld helm ရေးပေးပါမယ် :3)

ingress block ကို ခုလို update ထားပါတယ်. staging env မှာ deploy မှာမို့ ```letsencrypt-stag```ကို သုံးထားပါတယ်။ ပြီးရင်တော့ အဆင်ပြေသလို deploy လို့ ရပါတယ်။
```yaml
ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-stag"
  hosts:
  - host: hello.mgou.dev
    paths:
    - path: /
      pathType: ImplementationSpecific
  tls:
  - secretName: hello-app-secret
    hosts:
    - hello.mgou.dev
```
ကျွန်တော်ကတော့ argocd နဲ့ deploy လုပ်လိုက်ပါတယ် အဲ့တော့ တစ်ခုချင်းပြနေရင် ကြာမှာမို့ ဒီအဆင့်ကို သိကြမယ်ထင်လို့ ကျော်လိုက်ပါမယ် :P


Deploy ပြီးတဲ့အခါ cm-acme-http-solver ဆိုပြီး pod တစ်ခုက Certificate request ဖို့နဲ့ cert-manager နဲ့ သက်ဆိုင်ရာ အလုပ်တွေလုပ်ဖို့ တက်လာပါလိမ့်မယ်။

<img src="/assets/images/letencrypt-certmgr/image.png">

အဲ့ဒီ Pod ကို ကြည့်ရင် Certificate request တာတွေကို တွေ့ရပါလိမ့်မယ် Cert-manager ရဲ့ Pod နဲ့ တွဲကြည့်ရင် အပြန်အလှမ်အလုပ်လုပ်တဲ့ Log တွေကို တွေ့ရပါလိမ့်မယ်

<img src="/assets/images/letencrypt-certmgr/image-1.png">

အဆင်ပြေသွားပြီဆိုရင် Pod က delete သွားပါလိမ့်မယ်။ ကျွန်တော်တို့ browser မှာ ingress url ကို ခေါ်ကြည့်လိုက်ရင် hello world pageကို မြင်ရမှာဖြစ်ပါတယ်။ 

<img src="/assets/images/letencrypt-certmgr/image-5.png">

Certificate ကို ကြည့်ကြည့်မယ်ဆိုရင် အောက်ပါအတိုင်း lets encrypt ရဲ့ Staging Certificate ကို ရနေတာမြင်ရပါမယ်။

<img src="/assets/images/letencrypt-certmgr/image-2.png">

Prod ကို မသွားခင် Lets encrypt ရဲ့ Rate Limit တွေ အကြောင်းကို [Staging](https://letsencrypt.org/docs/staging-environment/) နဲ့ [Prod](https://letsencrypt.org/docs/rate-limits/) မှာ ဖတ်ကြည့်လို့ ရပါတယ်

Rate limite ရှိတဲ့အတွက် စမ်းမယ်ဆိုရင် staging နဲ့ပဲ စမ်းကြည့်ကြပါ မဟုတ်ရင်တော့ Domain ပြောင်းပြီးသုံးမှ အဆင်ပြေပါလိမ့်မယ်။
Prod အတွက်တော့ values.yaml မှာ အခုလိုပဲ Update လုပ်လိုက်ပါတယ် အဓိက annotation မှာ ClusterIssuer name ကို မှန်အောင် ထည့်ဖို့ပါပဲ။

```yaml
ingress:
  enabled: true
  className: nginx
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt"
  hosts:
  - host: hello-prod.mgou.dev
    paths:
    - path: /
      pathType: ImplementationSpecific
  tls:
  - secretName: hello-app-secret
    hosts:
    - hello-prod.mgou.dev
```
App ကို Deploy ပြီး ingress url ကို browser မှာ ခေါ်ကြည့်ရင် home page ကိုမြင်ရမှာ ဖြစ်ပြီး certificate ကို ကြည့်ရင်တော့ lets encrypt ရဲ့ Prod Certificate ကို မြင်ရမှာဖြစ်ပါတယ်

<img src="/assets/images/letencrypt-certmgr/image-4.png">

အောက်ပါ message ကတော့ prod certificate ကို တစ်နေ့ထဲမှာ multiple request လုပ်လိုက်လို့ Rate limit ထိတဲ့ error ပါ။ 

<img src="/assets/images/letencrypt-certmgr/image-3.png">

***ကျွန်တော်တို့ lets encrypt သုံးတဲ့အခါ Local DNS က အဆင်မပြေပါဘူး lets encrypt က A, AAAA record တွေစစ်တဲ့အခါ မတွေ့တော့ secret ကို issue မပေးတာမျိုးတွေကြုံရပါတယ်။ ကျွန်တော့ Local environment မှာမရတာမျိုးလည်း ဖြစ်ချင်ဖြစ်မှာပေါ့ :3။  Record ကို Cloud service တစ်ခုခု Route53, CloudFlare နဲ့ တစ်ခြား DNS service တွေနဲ့ဆိုလျှင်တော့ အဆင်ပြေပါတယ်။***


***Ref*** 
[Let's Encrypt Docs](https://letsencrypt.org/docs/)
[Cert-manager Tutorial](https://cert-manager.io/docs/tutorials/acme/nginx-ingress/)
