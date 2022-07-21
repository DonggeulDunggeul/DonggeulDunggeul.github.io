---
title:  "[Monitoring] 쿠버네티스 모니터링 - Prometheus"
excerpt: "쿠버네티스 모니터링 - Prometheus"
author: 동글

categories:
  - Monitoring
tags:
  - [Monitoring, Kubernetes, Prometheus]

toc: true
toc_sticky: true
 
date: 2022-07-20
last_modified_at: 2022-07-20
---

## 1. Prometheus
![프로메테우스](https://user-images.githubusercontent.com/109357459/179994066-8bc2fda7-194b-49e6-a8ca-52a9c6fb5320.png)
Prometheus는 현재 쿠버네티스 상에서 가장 많이 사용되고 있는 오픈 소스 기반 모니터링 시스템이며, 쿠버네티스 클러스터 및 컨테이너들를 손쉽게 모니터링이 가능합니다.


>### PromQL 쿼리
>
>- PromQL 쿼리를 사용하여 외부 API나 프로메테우스 웹콘솔을 이용해 서빙 가능합니다.
>
>- 그라파나 등과 통합하여 대쉬보드 구성합니다.  
> &nbsp;

>### Alert manager
>
>- alert를 전달받아 이를 적절한 포맷으로 가공하여 notify
>
>- Email, slack 에 알람 가능합니다.  
> &nbsp;

>### Service Discovery
>
>- Pod증가, Worker Node 증가 등 모니터링 대상 변경 탐지
>
>- kubernetes를 service discovery로 사용할 경우 kube-apiserver를 통해 모니터링해야할 타겟 서비스의 목록을 받아옵니다.  
> &nbsp;

>### Push gateway
>
>- 필요한 경우에만 떠 있다가 작업이 끝나면 사라지는 배치, 스케줄 작업 등은 Push gateway를 통해 지표를 받아옵니다.
>
>- Metric Data를 보관하고 있다가 Prometheus 서버가 Pull 작업을 하면, 보관하고 있던 Metric Data를 리턴합니다.  
> &nbsp;

>### 매트릭 저장
>
>- 일정시간마다 매트릭 수집 HDD/SDD 에 저장합니다.  
> &nbsp;

>### Exporter
>
>exporter 는 Prometheus가 pull 방식으로 데이터를 수집할 수 있도록 매트릭을 노출하는 agent, export는 서버들로부터 매트릭을 수집해 /metrics HTTP endpoint를 제공하면 Prometheus 는 exporter 가 열어놓은 HTTP endpoint 로 GET 요청을 날려 메트릭을 수집(Pull 방식)
>
>- 모니터링 대상이 프로메테우스의 데이터 포맷을 지원하지 않는 경우 별도의 에이전트( Exporter)를 설치해야 지표를 얻어올 수 있습니다.
>
>- java나 node.js와 같은 사용자 애플리케이션의경우에는 Exporter를 사용하지 않고, 프로메테우스클라이언트 라이브러리를 사용하게 되면 바로 지표를 프로메테우스 서버로 보낼 수 있습니다.
>
>- exporter의 종류에는 node-exporter, mysql- exporter, nginx-exporter, redis-exporter 등이 있습니다.  
> &nbsp;

&nbsp;

### 1.1 Prometheus - 쿠버네티스 모니터링 시스템으로 많이 쓰이는 이유
Prometheus는 pull방식으로 매트릭을 수집하므로 쿠버네티스 환경처럼 어플리케이션의 단위가 작고 모니터링 대상도 동적으로 변경될때에는 pull 방식이 용이합니다.

&nbsp;

### 1.2 Prometheus - 모니터링 영역(시계열 데이터)

- 클러스터에 대한 정보


>Pod, Deployment, Ingress 정보 등 클러스터(Cluster)를 이루는 구성 요소의 정보


- 노드에 대한 정보


>클러스터를 이루는 각각의 서버, 노드(Node)의 실제 CPU, 메모리, 트래픽 등의 사용량



- 리소스 사용량에 대한 정보


>Node, Pod 등의 리소스 사용량


- 직접 만든 서비스에 대한 정보


>웹 서버의 페이지뷰, 회원가입 양 등 수치로 표현할 수 있는 모든 것


&nbsp;

### 1.3 Prometheus - 장/단점
**장점**


>- 설정파일을 프로메테우스 서버 설정파일만 변경한 후 node-exporter는 배포만 하면 되기 때문에 관리자 입장에서 시스템 운영이 용이
>
>- 일정 간격마다 데이터를 수집하기 때문에 저사양의 Spec. 으로도 모니터링 시스템 구축이 가능
>
>- CPU/메모리/파일시스템 현황, 컨테이너 현황 등 특정 데이터의 흐름을 파악하는데 용이


**단점**


>- 많은 이벤트가 발생하는 시스템을 모니터링 하는데는 적합하지 않음
>
>- 이벤트 수집, 배치 작업 등 단발적으로 발생하는 업무 모니터링을 할 때는 적합하지 않음


&nbsp;

## 2. Pull & Push
![pull](https://user-images.githubusercontent.com/109357459/179996808-130084bc-d6b3-4902-a131-fd48ba14789b.jpg)

- **push - 에이전트가 모니터링 서버에게 데이터를 보내는 것**


>- 일반적으로 push 방식은 Host -> Data Backend로 전송하기 때문에, Data Backend를 변경할 시 Host마다 설정 파일 모두 변경 필요
>
>- Data Backend에 장애가 발생했을 때에도 아무 소용없는 push가 이루어지기에 트래픽에 과부하


- **pull - 모니터링 서버가 데이터를 에이전트에게 접근하여 가져오는 것**


>- 대상 애플리케이션의 Exporter Endpoint로부터 데이터를 scrape 해오는 방식
>
>- 모니터링 설정이 Data Backend에서 관리되기 때문에 변경이 쉬움
>- Prometheus에 장애가 발생해도 애플리케이션 자체에 영향 없음

