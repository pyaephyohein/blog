---
layout: post
title: "How to add Multiple configs data in configmap using HELM."
comments: true
description: "How to add Multiple configs data in configmap using HELM."
keywords: "helm,kubernetes,configmap,devops"
---

### Create config folder for multiple configurations files

Firstly, you need to create a folder in your Chart filder. 
```bash
mkdir Configs
```
Folder structure is described in the following example. 
```bash 
mychart
|-- Chart.yaml
|-- charts
|-- Configs ##This is created Config folder.
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```
Next, copy the configuration files you want to the ```Configs``` folder.

After you have copy the configuration, Let's start the helm template. 
```bash 
touch mychart/templates/example-configmap.yaml
```
and than write down the template as follows.

<script src="https://gist.github.com/pyaephyohein/da131a0844b3c6143a053626e5ab7806.js"></script>
