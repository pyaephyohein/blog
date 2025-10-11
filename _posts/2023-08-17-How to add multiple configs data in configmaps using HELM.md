---
layout: post
title: "How to add Multiple configs data in configmap using HELM."
date: 2023-08-17 09:00:00 +0700
tags: [Helm, Kubernetes, ConfigMap, DevOps]
reading_time: 5
image: /assets/images/default-banner.png
---

### Create config folder for multiple configurations files

Firstly, you need to create a folder in your Chart folder. 
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

Folder structure is described in the following example. 
```bash 
mychart
|-- Chart.yaml
|-- charts
|-- Configs ##This is created Config folder.
|   |-- exampleconfig.xml ## new added files
|   |-- example-data2.txt ## new added files
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```
After you have copy the configuration, Let's start the helm template. 
Create the template as follows
```bash 
touch mychart/templates/example-configmap.yaml
```
and than write down the template as follows.

<script src="https://gist.github.com/pyaephyohein/da131a0844b3c6143a053626e5ab7806.js"></script>


Please check with 
```bash
helm template <location>
```
final result as following example. 
```
kind: ConfigMap
apiVersion: v1
metadata:
  name:example-configmap
  annotations:
data:    
  exampleconfig.xml: |- 
    Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.

  example-data2.txt: |-
    Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum.
```
