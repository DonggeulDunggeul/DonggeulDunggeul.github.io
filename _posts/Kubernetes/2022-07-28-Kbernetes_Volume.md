---
title:  "[Kubernetes] Kubernetes - Volume"
excerpt: "Kubernetes - Volume"
author: 동글

categories:
  - Kubernetes
tags:
  - [Kubernetes, Volume]

toc: true
toc_sticky: true
 
date: 2022-08-01
last_modified_at: 2022-08-03
---

참고: [MS - Kubernetes 스토리지 관리](https://docs.microsoft.com/ko-kr/azure/databox-online/azure-stack-edge-gpu-kubernetes-storage),  
[Kubernetes Document - 볼륨](https://kubernetes.io/ko/docs/concepts/storage/volumes/),  
[RedHat - stateful-vs-stateless](https://www.redhat.com/ko/topics/cloud-native-apps/stateful-vs-stateless),  
[GKE - 볼륨](https://cloud.google.com/kubernetes-engine/docs/concepts/volumes?hl=ko)

컨테이너는 이식성이 높고, 유연하다는 특성으로 인해 스테이트리스(stateless)로 구축되었습니다. 따라서 컨테이너가 내려가거나 재시작 될 경우 기존의 데이터가 사라집니다.   
경우의 따라 데이터를 보존해야할 때에는 **볼륨(Volume)** 을 사용하면 컨테이너가 내려가거나 재시작되어도 기존의 데이터는 유지됩니다.  

>**스테이트풀(stateful)?**
>
>상태 정보를 저장한다.  
>스테이트리스 트랜잭션은 단일 요청에 대해 하나의 응답이 나오므로, 자동판매기와 비슷하다.

>**스테이트리스(stateless)?**
>
>상태 정보를 저장하지 않는다.
>스테이트풀 트랜잭션은 같은 사람과 주기적으로 지속하는 대화와 비슷하다.

&nbsp;  

## 1. 볼륨(Volume)  
쿠버네티스에서의 볼륨은 쿠버네티스 리소스가 아닌 Pod의 여러 컨테이너에서 액세스할 수 있는 데이터가 있는 디렉터리를 의미합니다.  
컨테이너 단위가 아닌 Pod 단위이기 때문에 Pod에 여러개의 컨테이너가 공유해서 사용할 수 있습니다.  
> **도커, 쿠버네티스 볼륨 차이**
>
> _**도커의 볼륨은 디스크에있는 디렉터리 디렉터리이거나 다른 컨테이너에 있다. 볼륨 드라이버를 제공하지만 기능이 다소 제한된다.**_
>
> _**쿠버네티스는 다양한 유형의 볼륨을 지원한다.**_  
> _파드가 더 이상 존재하지 않으면, 쿠버네티스는 임시(ephemeral) 볼륨을 삭제하지만, 퍼시스턴트(persistent) 볼륨은 삭제하지 않는다. 볼륨의 종류와 상관없이, 파드 내의 컨테이너가 재시작되어도 데이터는 보존된다._


&nbsp;  

### 1.1 볼륨의 유형  

**1.1.1 임시볼륨**  
- **emptyDir**  
Pod가 노드에 할당될 때 생성되며, 생성 당시에는 아무 내용이 없기 때문에 emptyDir라고 합니다. Pod의 컨테이너가 읽고 쓸 수 있는 빈 디렉터리를 제공합니다.
Pod 안에 생성되기 때문에 Pod가 내려가거나 재시작하게되면 데이터가 없어지므로 일시적인 사용목적을 가진 데이터에 사용하기 적합합니다.  

> 컨테이너 단위가 아닌 Pod 단위이므로 컨테이너가 내려가거나 재시작 되어도 계속해서 사용 가능

&nbsp;  

**1.1.2 로컬볼륨**  
- **hostPath**  
노드의 로컬 파일시스템을 Pod에 마운트 해서 사용합니다.  

> 특정 노드의 파일시스템에 저장되므로 Pod가 다른 노드로 스케쥴링 되면 이전 데이터를 볼 수 없다.  

&nbsp;  

**1.1.3 Cloudstorage 볼륨**   
클라우드 서비스의 전용 스토리지를 마운트합니다. gcePersistentDisk(GCE Persistent Disk), awsElasticBlockStore(AWS EBS Volume), azureDist(MS Azure Disk Volume) 등이 있습니다.

&nbsp;  

**1.1.4 파일 공유 볼륨**   
- **NFS**  
기존 NFS (네트워크 파일 시스템) 볼륨을 파드에 마운트 할수 있습니다.  
Pod가 제거되어도 볼륨의 내용은 유지되고, NFS 볼륨에 데이터를 미리 채울 수 있으며, Pod 간에 데이터를 공유할 수 있습니다.

>사용하려면 NFS 서버를 실행해야 한다.   
>[NFS 예시](https://github.com/kubernetes/examples/tree/master/staging/volumes/nfs)  

&nbsp;  

**1.1.5 분산 파일 시스템**  
- **glusterfs**  
glusterfs 볼륨을 사용하면 Glusterfs (오픈 소스 네트워크 파일시스템) 볼륨을 파드에 마운트할 수 있습니다.

> **gluster?**  
> Gluster / glusterfs 는 확장 가능한 분산 파일 시스템으로
> 여러 서버의 스토리지 리소스를 단일 글로벌 네임스페이스로 활용 가능  
> 
> [glusterfs 예시](https://github.com/kubernetes/examples/tree/master/volumes/glusterfs)  

&nbsp;  

**1.1.6 configMap**  
configMap에 저장된 데이터는 볼륨에서 마운트되어 Pod에서 실행되는 컨테이너화 된 어플리케이션에서 사용됩니다.

> configMap을 먼저 생성해야 사용할 수 있다.  


&nbsp;  

### 1.2 PV(PersistentVolume), PVC(PersistentVolumeClaim)  
PV와 PV는 Pod와 별개의 쿠버네티스 리소스로, Pod의 생명주기와 별개로 작동 합니다.  
PV는 스토리지의 연결을 담당하는 리소스이고, PVC는 PV와 Pod를 연결하기 위한 리소스 입니다.   

**1.2.1 PV와 PVC의 라이프 사이클**  
- Provisioning  
- Binding  
- Using  
- Reclaiming  

**1.2.2 PV 접근 모드**  

**1.2.3 PV, PVC 생성**


