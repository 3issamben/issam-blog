---
layout: post
title: "How to Run Docker Azure Function as a Docker container on M1/M2 MAC"
date: 2023-02-06 09:44:07
image: '/assets/img/'
description: How to Run Docker Azure Function as a Docker container on M1/M2 MAC
tags:
- Azure
- Azure Function
- Docker Azure Function M1 M2 MAC OS
- Docker Azure Function
categories: 
- Azure
twitter_text:
---

## Main issue

Microsoft offers ready docker images that allows running azure function on a container.

The available docker images supports various runtime environments e.g. Java, PowerShell and Python. Check available [images](https://hub.docker.com/_/microsoft-azure-functions)

The down side is that all the images are x86 based architecture and there is no support for arm64.

The first thing that comes to mind in this case is to add the target platform when building the docker image `docker build --platform linux/amd64 ....` This will ensure to run the image in an emulated x86 environment.`

```bash
docker build --platform linux/amd64 -t <image-name> .
```



However, when running the docker container `docker run -p 8080:80 -t <image-name>`  you will get the following error

```bash

Azure Functions Core Tools
Core Tools Version:       4.0.4483 Commit hash: N/A  (64-bit)
Function Runtime Version: 4.1.3.17473
 
System.IO.IOException: Function not implemented
   at System.IO.FileSystemWatcher.StartRaisingEvents()
   at System.IO.FileSystemWatcher.StartRaisingEventsIfNotDisposed()
   at System.IO.FileSystemWatcher.set_EnableRaisingEvents(Boolean value)
```


This error is due to running x86-based Docker on a Mac with apple silicon processor (M1, M2) through **QEMU** emulation, which often causes compatibility issues and corrupt dependencies hence run time crashes.


## Solution

Fortunately, Docker has implemented a new feature that allows running images built for x68 architecture on MAC using Apple Rosetta 2 emulator instead of **QEMU**

In order to enable Rosseta 2 virtualization feature in Docker follow the steps below:

1. Update Docker to latest version
![iTermx86](/assets/img/post/azure/docker_m1/docker_version.png){:class="img-responsive"}
2. Click on *General* in the left panel and enable *Use Virtualization framewrok*
![iTermx86](/assets/img/post/azure/docker_m1/enable_roseta.jpg){:class="img-responsive"}
3. Click on *Features in development* and enable *Use Rosetta for x86/amd64 emulation on Apple Silicon*
![iTermx86](/assets/img/post/azure/docker_m1/docker_virtu.jpg){:class="img-responsive"}

