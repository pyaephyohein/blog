---
layout: post
title: "Why I use External DNS"
comments: true
description: "How to Download or Use This Theme"
keywords: "kubernetes, helm, DNS, Route53, Cloudflare, Cloud Native"
---

### Why I use External DNS
Last years ago, I started using External DNS for the first time. It was easy to manage the DNS Records on the AWS's Route 53, and other DNS services like Cloudflare and so on. You don't have to worry about creating too many DNS records by yourself :P. 

### How it works.
External DNS is great tools for Kubernetes ingress. It can be used to automatically create DNS records when I deploy ingress in Kubernetes Cluster. So let's start with a simple example. 

### Example with Route 53. 
In this example, I will show you with helm and argocd. so you can use other tools and IaC like terraform.
<br>
I will use my domain ```test.mgou.dev```for this example. 
<br>
<br>
Please create Hosted Zone in route53.
<br>
<img src="../assets/images/aws-hosted-zone-test.png">
<br>
You need to create AWS POLICY with following JSON. 
<br>
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "route53:ChangeResourceRecordSets"
      ],
      "Resource": [
        "*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [ 
        "route53:GetChange"
      ],
      "Resource": [ 
        "arn:aws:route53:::change/*" 
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "route53:ListHostedZones",
        "route53:ListResourceRecordSets",
        "route53:ListHostedZonesByName"
      ],
      "Resource": [
        "*"
      ]
    }
  ]
}
```
You can also allow permission only one dns zone per account. But I just used one account for all zone for this example. 
<br>
<br>
Create IAM account and attache above Permission. 
<br>
<img src="../assets/images/extdnsuser+policy.png">
<br>
<br>
And Than please create AWS ACCESS KEY and SECRET. And Note this Key and Secret. 

So finished the aws part. Next is External DNS HELM. Let's prepare!.

I will use Argocd and Own Git REPO. I assume you already know these parts.
<br>
Add Bitnami Helm Repo 
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```
Pull helm released package.
```bash
helm pull bitnami/external-dns
```
Extract Pulled package.
```bash
tar -xzf external-dns-6.23.6.tgz
```
<br>
<br>
let's update helm value.yaml. Update this block in value.yaml. 
<br>
```yaml
aws:
  ## AWS credentials
  ## @param aws.credentials.secretKey When using the AWS provider, set `aws_secret_access_key` in the AWS credentials (optional)
  ## @param aws.credentials.accessKey When using the AWS provider, set `aws_access_key_id` in the AWS credentials (optional)
  ## @param aws.credentials.mountPath When using the AWS provider, determine `mountPath` for `credentials` secret
  ##
  credentials:
    secretKey: "<Your AWS Access Key Secret>"
    accessKey: "<Your AWS Access Key ID>"
    ## Before external-dns 0.5.9 home dir should be `/root/.aws`
    ##
```
```you can also use kubernetes secret``` if you don't want to add in vaule.yaml
and than you can use any method to Deploy. I used Argocd.
<br>
<img src="../assets/images/extdnsargocd.png">
<br><br>
So let's Test. I have to test vault. So I just deploy the Vault with ingress ```vault.test.mgou.dev```. 
<br>
<img src="../assets/images/vaultargocd.png">
<br><br>
After Deploy Vault Let's check External DNS logs. It said successfully updated.
<br>
<img src="../assets/images/extdnslogs.png">
<br>
<br>
Let's check DNS records in Route 53. They are created. Vaule will be ingress class's loadbalancer address. 
<br>
<img src="../assets/images/extdnsrecords.png">
<br>
<br>
Now we can access to vault Home Page. with ```vault.test.mgou.dev```
<br>
<img src="../assets/images/vaulthome.png">
<br>
<br>
Next week, Let's test with Cloudflare.