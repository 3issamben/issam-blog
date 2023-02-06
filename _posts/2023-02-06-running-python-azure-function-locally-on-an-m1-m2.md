---
layout: post
title: "Running Python Azure Function locally on an M1 M2 MACOS"
date: 2023-02-06 09:44:07
image: '/assets/img/'
description: How to run Azure Functions locally on MACs with M1 or M2 processor
tags:
- Azure
- Azure Function
- Python Azure Function M1 M2 MAC OS
categories: 
- Azure
twitter_text:
---

## Main issue

Azure Functions Core Tools is great tool that allows developing and testing functions on your local machine. Unfortunately, Azure Function Core Tools does not support local python function development on mac OS with arm64 processors i.e. M1 and M2 macs.

Until today there is an open [issue](https://github.com/Azure/azure-functions-python-worker/issues/915) on Github you can follow.

When trying to run the python function func start you will receive the following error `Microsoft.Azure.WebJobs.Script: Architecture Arm64 is not supported for language python`


## Solution

The workaround consists of creating an emulated environment of x64 and install all required dependencies.

### Prepare the 86x emulation environment

1. In your Mac, open Finder, choose Applications, and locate Terminal.
2. Create a separate parallel environment by duplicating the Terminal and renaming it to <terminal_name>x86.
![iTermx86](/assets/img/post/azure/m1/iterm_app.jpg){:class="img-responsive"}
3. Click right on the new terminal application iTermx86 then click info.
![iTermx86](/assets/img/post/azure/m1/iterm_get_info.png){:class="img-responsive"}
4. Make sure you enable Open using Rosseta in the the new terminal application
![iTermx86](/assets/img/post/azure/m1/rosseta.png){:class="img-responsive"}
5. In order to check the 86x emulation on the new terminal run
```bash 
arch
```
The expected response is `i386`


### Install Required Tools

Once you have the 86x emulation up and running it is time to install tools and dependencies.

- Homebrew
- Python
- Azure Functions Core Tools

Note: Make sure to run all the commands in the new terminal

1. Install homebrew
``` bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
2. Make sure the homebrew is installed to this path `/usr/local/bin/brew`
3. Install Python using homebrew
```bash
/usr/local/bin/brew install python@3.9
```
4. Verify that python has been installed to `/usr/local/bin/python3.9`
5. Install Azure Functions Core Tools
```bash
/usr/local/bin/brew tap azure/functions
/usr/local/bin/brew install azure-functions-core-tools@4
```
6. Verify that Azure Core has been installed in `/usr/local/Cellar/azure-functions-core-tools@4/...`


### Set Aliases

In order to make the usage of brew, python and azure functions easy it is better to add the following aliases to your ~/.zshrc file

1. Update ~/.zshrc
```bash
if [ $(arch) = "i386" ]; then
    alias python3="/usr/local/bin/python3"
    alias brew86='/usr/local/bin/brew'
    alias pyenv86="arch -x86_64 pyenv"
    alias func="/usr/local/Cellar/azure-functions-core-tools@4/4.0.4915/func"
fi
```
2. Restart your terminal or source your ~/.zshrc file
```bash
source ~/.zshrc
```

3. Validate the the correct packages and versions by running.
```bash
which python3
which brew86
which func
```
### Running Azure Function Locally
Now all the required packages are installed it is time to run new azure function

1. Create new virtual environment and activate it.
```bash
cd <azure_function_project>
python3 -m venv venv
source venv/bin/activate
```

2. Install required dependencies if exists
```bash
pip install -t requirements
```

3. Run the Azure Function locally
```bash
func start
```