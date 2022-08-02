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
last_modified_at: 2022-08-02
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

- 임시볼륨
- 영구볼륨
- Cloudstorage 볼륨
- 파일 공유 볼륨
- 분산 파일 시스템





---
https://www.educba.com/kubernetes-volume/ 링크 참고

https://cloud.netapp.com/blog/cvo-blg-5-types-of-kubernetes-volumes-and-how-to-work-with-them

두개 링크 참고하여 https://bcho.tistory.com/1259 이거처럼 표로 정리. 



내용정리 TO-DO
- []볼륨
- []pv, pvc
- []정적 볼륨 프로비저닝
- []동적 볼륨 프로비저닝 - storageClass
