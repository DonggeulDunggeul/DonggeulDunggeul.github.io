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
last_modified_at: 2022-08-04
---

참고: [MS - Kubernetes 스토리지 관리](https://docs.microsoft.com/ko-kr/azure/databox-online/azure-stack-edge-gpu-kubernetes-storage),  
[Kubernetes Document - 볼륨](https://kubernetes.io/ko/docs/concepts/storage/volumes/),  
[Kubernetes Document - 퍼시스턴트 볼륨](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/)  
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

> **Kubernetes Pod에 대한 Storge 요구사항**  
> Kubernetes Pod는 스테이트리스 이지만 애플리케이션은 보통 스테이트풀입니다. Pod는 수며이 짧을 수 있고, 재시작, 이동등이 일어날 수 있으므로 Pod와 연결된 스토리지는 아래의 요구사항을 충족해야 합니다.  
>
> - Pod 외부에 존재해야 한다.
> - Pod의 수명주기에 독립적이어야 한다.
> - 모튼 Kuvernetes 노드에 엑세스 할 수 있다.

&nbsp;  


**1.2.1 PV와 PVC의 라이프 사이클**  
![pv](https://user-images.githubusercontent.com/109357459/182866552-3b1ea39c-8454-4d35-ba2f-2867378dc8af.jpg) 

- **Provisioning**  
PV를 프로비저닝할 수 있는 방법으로는 정적(static) 프로비저닝과 동적(dynamic) 프로비저닝이 있다.  

>**정적(static) 프로비저닝**  
> 
> ![정적정적 png](https://user-images.githubusercontent.com/109357459/182875356-fc876e5d-019a-4b6d-b58a-f295647a8ddd.jpg)  
>
>1. **Provision storage**  
>클러스터 관리자가 스토리지를 프로비전 합니다.
>
>2. **Claim storage**  
>스토리지를 요청하는 PVC를 배포합니다.
>
>3. **Mount PVC to container**  
>PVC가 PV에 바인딩 되면 PVC를 컨테이너의 경로에 마운트 할 수 있습니다.
>
>_PV types - [퍼시스턴트 볼륨의 유형](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/#%ED%8D%BC%EC%8B%9C%EC%8A%A4%ED%84%B4%ED%8A%B8-%EB%B3%BC%EB%A5%A8%EC%9D%98-%EC%9C%A0%ED%98%95)_


>**동적(dynamic) 프로비저닝**  
> 
> ![동적동적](https://user-images.githubusercontent.com/109357459/182879412-702bc1d9-2167-4f95-8a75-467bba544876.jpg)
>
>1. **Define Storage Class**  
>클러스터 관리자는 Kubernetes 클러스터의 운영 환경에 따라 스토리지 클래스를 정의합니다.
>
>2. **Claim storage**  
>스토리지 클래스를 참조하여 PVC를 만들면 프로비저너가 호출됩니다.
>
>3. **Provisions storage dynamically**  
>프로비저너는 새 볼륨을 생성한 후 생성한 볼륨을 나타내는 PV를 동적으로 생성합니다.
>
>4. **Mount PVC to container**  
>PVC가 PV에 바인딩 되면 PVC를 컨테이너의 경로에 마운트 할 수 있습니다. 
>
>>_**[주요 스토리지의 Privisioner]**_  
>>AWS EBS - provisioner: kubernetes.io/aws-ebs  
>>GCE PD - provisioner: kubernetes.io/gce-pd  
>>Glusterfs - provisioner: kubernetes.io/glusterfs  
>>NFS - provisioner: example.com/external-nfs  
>>OpenStack Cinder - provisioner: kubernetes.io/cinder  
>>vSphere - provisioner: csi.vsphere.vmware.com / provisioner: kubernetes.io/vsphere-volume  
>>Ceph RBD - provisioner: kubernetes.io/rbd  
>>Quobyte - provisioner: kubernetes.io/quobyte  
>>Azure Disk - provisioner: kubernetes.io/azure-disk  
>>Azure File - provisioner: kubernetes.io/azure-file  
>>Portworx Volume - provisioner: kubernetes.io/portworx-volume  
>>ScaleIO - provisioner: kubernetes.io/scaleio  
>>StorageOS - provisioner: kubernetes.io/storageos  
>>Local - provisioner: kubernetes.io/no-provisioner  


&nbsp;  

- **Binding**  
바인딩(Binding)은 프로비저닝으로 만든 PV를 PVC와 연결하는 단계입니다.

> PV와 PVC의 매핑은 1대1 관계입니다.


- **Using**  
PVC는 파드에 설정되고 파드는 PVC를 볼륨으로 인식해서 사용합니다.

- **Reclaiming**  
사용이 끝난 PVC는 삭제되고 PVC를 사용하던 PV를 초기화(reclaim)하는 과정을 거칩니다.  

> **Reclaim Policy(초기화 정책)**  
>
>- **Retain**  
>     - PVC 삭제 시 사용 중이던 PV는 단순히 사용 해제 상태이므로 PV안의 데이터는 그대로 유지가 된 채로 남아 있는 상태
>     - PV가 만약 외부 Storege와 연계되어 있었다면 PV는 삭제되더라도 외부 Storege의 볼륨은 그대로 남아 있는 상태
>     - 남아 있는 Storege의 볼륨을 삭제하거나 재 사용하려면 그 볼륨을 이용하는 PV 다시 생성
>- **Delete**  
>     - PVC삭제 시 PV 또한 삭제하고 연계되어 있는 외부 Storege 쪽의 볼륨도 삭제
>     - Provisioning할 때 동적볼륨할당으로 생성된 PV들은 기본 ****Reclaim 정책은 Delete
>- **Recycle**(deprecated)  
>     - PV의 데이터들을 삭제하고 다시 새로운 PVC에서 PV를 사용할 수 있는 상태
>
>_기본 PV 회수 정책은 Retain, AWS, Google Cloud등과 같은 Storege Class의 경우 Dynamic Provisioning된 PV의 기본 정책은 Delete_

&nbsp;  


**1.2.2 PV 접근 모드**  
- **ReadWriteOnce**  
하나의 노드에서 해당 볼륨이 읽기-쓰기로 마운트 될 수 있습니다. ReadWriteOnce 접근 모드에서도 파드가 동일 노드에서 구동되는 경우에는 복수의 파드에서 볼륨에 접근할 수 있습니다.  

- **ReadOnlyMany**  
볼륨이 다수의 노드에서 읽기 전용으로 마운트 될 수 있습니다.

- **ReadWriteMany**   
볼륨이 다수의 노드에서 읽기-쓰기로 마운트 될 수 있습니다.

- **ReadWriteOncePod**
볼륨이 단일 파드에서 읽기-쓰기로 마운트될 수 있습니다.

>전체 클러스터에서 단 하나의 파드만 해당 PVC를 읽거나 쓸 수 있어야하는 경우 ReadWriteOncePod 접근 모드를 사용한다. 이 기능은 CSI 볼륨과 쿠버네티스 버전 1.22+ 에서만 지원된다.