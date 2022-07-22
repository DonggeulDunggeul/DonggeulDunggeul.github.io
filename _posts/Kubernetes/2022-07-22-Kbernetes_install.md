---
title:  "[Kubernetes] Kubernetes install"
excerpt: "Kubernetes install"
author: 동글

categories:
  - Kubernetes
tags:
  - [Kubernetes]

toc: true
toc_sticky: true
 
date: 2022-07-22
last_modified_at: 2022-07-22
---
> 관련 Post : [Docker install](https://donggeuldunggeul.github.io/posts/Docker_install/)

Ubuntu - 20.04  
kubernetes : 1.18.8  


## 1. Kubernetes 설치

&nbsp;  

### 1.Install Kubernetes

```
curl -s [https://packages.cloud.google.com/apt/doc/apt-key.gpg](https://packages.cloud.google.com/apt/doc/apt-key.gpg) | sudo apt-key add -
```

```
sudo bash -c 'cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb [https://apt.kubernetes.io/](https://apt.kubernetes.io/) kubernetes-xenial main
EOF'
```

```
sudo apt-get update
```

**1.1 Disable Security Linux**

```j
/sbin/setenforce 0
```

**1.2 Disable SWAP**

```
sudo /sbin/swapoff -a
```

**1.3 Set IP Forward**

```
echo 1 > sudo /proc/sys/net/ipv4/ip_forward
```

**1.4 Network Setting**

```
sudo bash -c 'cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF'
sudo sysctl --system
```

**1.5 Install Kubernetes**

```
sudo apt-get install -y kubelet=1.18.8-00 kubeadm=1.18.8-00 kubectl=1.18.8-00
sudo apt-mark hold kubelet=1.18.8-00 kubeadm=1.18.8-00 kubectl=1.18.8-00
```

**1.6 Check Kubernetes version**

```
sudo kubectl version –-client
```

**1.7 Start kubelet**

```
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl enable kubelet
```

### 2. 단일 클러스터 구성

**2.1 Setting Environment**

```-
swapoff -a
echo 1 > /proc/sys/net/ipv4/ip_forward
```

**2.2 Kubeadm init**

```
sudo kubeadm init --pod-network-cidr 10.244.0.0/16
export KUBECONFIG=/etc/kubernetes/admin.conf
```

**2.2 Enable Kubectl**

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**2.3 Install network(cni)**

```
//Canal
kubectl apply -f [https://docs.projectcalico.org/v3.8/manifests/canal.yaml](https://docs.projectcalico.org/v3.8/manifests/canal.yaml)

//위브넷
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

**2.4 Show Kubernetes Cluster**

```
// 마스터노드가 워커노드의 일도 할 수 있도록 해주는 명령어
kubectl taint nodes --all node-role.kubernetes.io/master-
// 라벨추가(nodeselecter를 지정해놓은것을 yaml에서 확인가능)
// nodeselecter - 해당 라벨을 가진 노드에서 실행하겠다
kubectl label nodes nodename nodetype=local
kubectl get node
kubectl get pods --all-namespaces
```

### 3. k9s

[최신버전 확인](https://github.com/derailed/k9s/releases)

```
K9S_VERSION=v0.24.15
curl -sL https://github.com/derailed/k9s/releases/download/${K9S_VERSION}/k9s_Linux_x86_64.tar.gz | sudo tar xfz - -C /usr/local/bin k9s

// 실행(root권한을 가진 계정으로 접속한게 아니면 sudo를 붙혀 실행해야지 pod, node들 확인 가능)
k9s
```

### 4. Remove Kubernetes

```
//kubeadm reset 안되면
//rm -rf /etc/kubernetes/*
//rm -rf /root/.kube/

sudo kubeadm reset
sudo systemctl stop kubelet
sudo systemctl stop docker
sudo rm -rf /var/lib/cni/
sudo rm -rf /var/lib/etcd/
sudo rm -rf /var/lib/kubelet/*
sudo rm -rf /etc/cni/
sudo rm -rf /etc/kubernetes
sudo rm -rf /root/.kube
sudo apt-get remove kubeadm
sudo apt-get remove kubelet
sudo apt-get remove kubectl
```

### 5. Error Tip

```
// Ubuntu재시작 시 pods, nod 확인이 안되면서 아래와 같은 오류가 날 경우
// did you specify the right host or port? 
sudo -i 
swapoff -a
exit

// strace는 애플리케이션들이 사용하는 system call과 signal 등을 추적해서 
// 성능 저하를 일으키는 부분은 없는지, 에러가 나는 부분은 없는지를 확인하는데 사용하는 디버깅 툴
strace -eopenat kubectl version
```