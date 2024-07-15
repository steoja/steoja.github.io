---
layout: post
title: "Install Docker on Ubuntu"
date: 2024-07-15 13:00:00 -0500
categories: Ubuntu Docker
tags: Ubuntu Docker
---

# Ubuntu 22.04

Update apt 
```bash
sudo apt update
```
Install dependencies
```bash
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Update apt again to pull latest docker.
```bash
sudo apt update
```
Check the cache and verify docker.
```bash
apt-cache policy docker-ce
```
You should see similar output to the screenshot below.

![img-description](https://s3.us-east-1.wasabisys.com/documentationpics/Docker-apt-cache.png)

Install Docker
```bash
sudo apt install docker-ce
```

Check docker status
```bash
sudo systemctl status docker
```

Add your user to the docker group so you do not need to use sudo before docker commands

```bash
sudo usermod -aG docker username
```

Install Docker Compose
```bash
sudo apt install docker-compose
```


