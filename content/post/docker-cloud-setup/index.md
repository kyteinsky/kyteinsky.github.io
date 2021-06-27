+++
author = "Anupam Kumar"
title = "Hosting Docker in Cloud (Azure)"
date = "2021-06-15"
description = "Setting up docker inside an instance in Azure Cloud"
tags = [
    "docker",
    "cloud",
    "azure",
]
categories = [
    "daily-progress",
]
series = ["Docker", "Cloud"]
aliases = ["docker-cloud"]
image = 'rafael-garcin-sqZ4GeyYGx8-unsplash.jpg'
asciinema = true
+++

&nbsp;

## Azure part of story
- First create one Azure account, I am using a student account
- Create a basic instance (Ubuntu image works for most cases) with 1 vCPU and 1 GiB RAM, or according to your needs
- Download the private key and SSH into the instance
- Update the packages
- Install docker (following [this](https://docs.docker.com/engine/install/ubuntu/) guide)

Let's move on to the local machine setup

&nbsp;

## Local machine setup

### SSH Keys Setup
- We need to install docker here as well
- Add ssh key (downloaded from Azure portal) to OpenSSH Auth agent
```bash
eval 'ssh-agent'
ssh-add <path-to-pem-file>/<file-name>.pem
```
- *(or)* Add `AddKeysToAgent yes` to ~/.ssh/config file to automatically do that for you
```bash
echo "AddKeysToAgent yes" >> ~/.ssh/config
```

&nbsp;

### Docker Context Setup
- Create a new docker context to talk to the docker in Azure instance with command
```bash
docker context create azure-box --docker "host=ssh://azureuser@<public-ip-of-instance>"
```
- See all available contexts using `docker context ls`
- Switching to Azure docker is done using command `docker context use azure-box` and back to the local docker using `docker context use default`
- Now when we issue commands to docker as usual `docker ps`, it is directly communicating to the docker running in the Azure instance

> Note: 'azure-box' is just a name, can be replaced with anything of your choice

## 🎉 Now you have docker running in the cloud, accessible from the terminal

### Finally just for testing, we can spin up a Ubuntu container and bash into it:

{{< asciinema key="docker-cloud/docker-cloud.cast" rows="35" preload="1" >}}

