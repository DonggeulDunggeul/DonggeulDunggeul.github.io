---
title:  "[Docker] Docker install"
excerpt: "Docker install"
author: 동글

categories:
  - Docker
tags:
  - [Docker]

toc: true
toc_sticky: true
 
date: 2022-07-22
last_modified_at: 2022-07-22
---
> 관련 Post : 

Ubuntu - 20.04  
docker : 19.03.8  

### 1. Docker 설치

&nbsp;  

**1.1 Stop Firewalld**

```
sudo ufw disable
```

**1.2 uninstall old version docker**

```
sudo apt-get remove docker docker-engine [docker.io](http://docker.io/) containerd runc
sudo apt-get update
```

**1.3 SETUP the repository**

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) $(lsb_release -cs) stable"
sudo apt-get update
```

**1.4 Install Docker**

```
//설치가능 도커버전 확인
//apt-cache madison docker-ce
//apt-cache madison docker-ce-cli

//Ubuntu18.04
sudo apt-get install docker-ce=5:19.03.8~3-0~ubuntu-bionic docker-ce-cli=5:19.03.8~3-0~ubuntu-bionic [containerd.io](http://containerd.io/)

//Ubuntu20.04
sudo apt-get install docker-ce=5:19.03.12~3-0~ubuntu-focal docker-ce-cli=5:19.03.12~3-0~ubuntu-focal containerd.io
```

**1.5 Check Docker version**

```
sudo docker version
```

### 2. Docker 설정

> [참고](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker)

```
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

```
sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 3. Remocve Docker

```
systemctl stop containerd.service
sudo apt-get purge [containerd.io](http://containerd.io/)
sudo apt-get purge docker-ce
sudo apt autoremove docker-ce
sudo apt-get purge docker-ce-cli

sudo rm -rf /var/lib/docker /etc/docker 
sudo rm /etc/apparmor.d/docker 
sudo groupdel docker 
sudo rm -rf /var/run/docker.sock
```