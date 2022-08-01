---
title:  "[Kubernetes] Kubernetes - Volume"
excerpt: "Kubernetes - Volume"
author: 동글

categories:
  - Kubernetes
tags:
  - [Kubernetes]

toc: true
toc_sticky: true
 
date: 2022-08-01
last_modified_at: 2022-08-01
---

참고: [MS - Kubernetes 스토리지 관리](https://docs.microsoft.com/ko-kr/azure/databox-online/azure-stack-edge-gpu-kubernetes-storage)  

컨테이너는 이식성이 높고, 유연하다는 특성으로 인해 스테이트리스(stateless)로 구축되었습니다. 따라서 컨테이너가 내려가거나 재시작 될 경우 기존의 데이터가 사라집니다.   
경우의 따라 데이터를 보존해야할 때에는 **볼륨(Volume)** 을 사용하면 컨테이너가 내려가거나 재시작되어도 기존의 데이터는 유지됩니다.  


내용정리 TO-DO
- pv, pvc
- storageClass
- 정적 볼륨 프로비저닝
- 동적 볼륨 프로비저닝
