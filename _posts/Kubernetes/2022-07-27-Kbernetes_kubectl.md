---
title:  "[Kubernetes] Kubernetes - Kubectl"
excerpt: "Kubernetes Kubectl"
author: 동글

categories:
  - Kubernetes
tags:
  - [Kubernetes]

toc: true
toc_sticky: true
 
date: 2022-07-27
last_modified_at: 2022-07-27
---



참고: [Kubernetes 레퍼런스 - 명령줄 도구 (Kubectl)](https://kubernetes.io/ko/docs/reference/kubectl/), ORELLY 쿠버네티스 시작하기


## 1. Kubectl  
Kubectl은 쿠버네티스 API와 상호작용하기 위한 커맨드라인 도구입니다.  
Kubectl을 사용해 Pod, ReplicaSet 등 대부분의 쿠버네티스 객체를 관리하고, 클러스터의 전반벅인 상태를 탐색하고 확인할 수 있습니다.

&nbsp;  

### 1.1 클러스터 상태 확인

> **참고) 쿠버네티스 클러스터란?** 
> 
> ![그림1](https://user-images.githubusercontent.com/109357459/181258364-73597659-418b-4dc5-844c-e8a3f5b16813.jpg)
> 컨테이너화 된 어플리케이션을 실행하는 워커노드의 집합

&nbsp;  

**1.1.1 클러스터 버전 확인**  
쿠버네티스 API, kubectl 버전 확인이 가능합니다.

```
$ kubectl version
```

**1.1.2 클러스터 진단**  
클러스터를 구성하는 [컴포넌트](https://kubernetes.io/ko/docs/concepts/overview/components/#%EC%BB%A8%ED%8A%B8%EB%A1%A4-%ED%94%8C%EB%A0%88%EC%9D%B8-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8)들과 클러스터의 상태를 확인 할 수 있습니다.  

```
$ kubectl get componentstatuses
```

>**참고) 쿠버네티스 컴포넌트**  
>
> **컨트롤 플레인(control Plane) 컴포넌트**
>- **kube-apiserver** : 쿠버네티스 클러스터로 들어오는 요청(kubectl 명령 등)을 가장 앞에서 전송받아 요청의 처리 흐름에 따라 적절한 컴포넌트로 요청을 전달  
>
>- **etcd** : 쿠버네티스 클러스터가 동작하기 위해 필요한 클러스터 및 리소스의 구성정보, 상태 정보 등을 키-값(key-value)형태로 저장하는 저장소  
>
>- **kube-scheduler** : 새로 생성된 파드(기본적인 작업단위)를 감지하여 어떤 노드로 배치할지 결정하는 작업인 스케줄링을 담당  
>
>- **kube-controller-manager** : kube-controller-manager는 다운된 노드가 없는지, 파드가 의도한 복제(Replicas) 숫자를 유지하고 있는지, 서비스와 파드는 적절하게 연결되어 있는지, 네임스페이스에 대한 기본 계정과 토큰이 생성되어 있는지를 확인하고 적절하지 않다면 적절한 수준을 유지하기 위해 조치하는 역할  
>
> **노드(Node) 컴포넌트**  
> - **kubelet** : 노드에서 컨테이너가 동작하도록 관리해주는 핵심 요소로 쿠버네티스 파드를 관리하기위해 작성한 YAML을 kubectl 명령어로 적용하면, kubelet은 YAML을 통해 전달된 파드를 생성, 변경하고 YAML에 명시된 컨테이너가 정상적으로 실행되고 있는지 확인  
>
> - **kube-proxy** : 쿠버네티스 클러스터 내부에서 네트워크 요청을 전달하는 역할로 파드의 IP는 매번 변하지만 kube-proxy가 파드에 접근할 수 있는 방법을 그때마다 관리하고 갱신하며, 서비스 오브젝트는 이 정보를 사용하여 파드가 외부에서 접근할 수 있는 경로를 제공
>
> - **[container runtime](https://kubernetes.io/ko/docs/setup/production-environment/container-runtimes/)** : 쿠버네티스가 컨테이너를 제어하기 위해 제공하는 표준 규약인 컨테이너 런타임 인터페이스(**CRI**)를 준수하여 쿠버네티스와 함께 사용할 수 있는 외부 애플리케이션들을 의미
>
> _**CRI?** : 기본적으로 쿠버네티스는 파드 관리의 역할을 하는 kubelet이 명령을 받으면 도커 런타임을 통해 컨테이너가 생성되거나 되는데 쿠버네티스 내 컨테이너 기술로 도커 이외의 것들도 사용되다보니 컨테이너 런타임에 대한 이슈가 생겼고 해당 컨테이너 런타임마다 kubelet을 수정해야 하는 문제가 생겼다. 그래서 kubelet과 컨테이너 런타임과의 표준을 정하게 되었는데 이것이 CRI(Container Runtime Interface)이다._

&nbsp;  

### 1.2 쿠버네티스 워커 노드 조회

&nbsp;  

**1.2.1 노드 목록 조회**  
쿠버네티스 클러스터의 모든 노드 목록을 조회할 수 있습니다.

```
$ kubectl get nodes
```

> 쿠버네티스에서 노드는 API 서버 스케줄러 등과 같이 클러스터를 관리하는 master 노드와 컨테이너가 실행되는 worker 노드로 구분되며, 사용자 작업부하(workload)가 클러스터 전체 운영에 영향을 주지 않도록 master 노드에 스케줄링을 수행하지 않는다.

**1.2.2 노드 상세정보 조회**  
특정 노드에 대한 상세 정보를 조회할 수 있습니다.

- 실행중인 도커, 쿠버네티스, 리눅스 커널 등 노드에 위치한 소프트웨어 정보
- 노드상에 위치한 파드 정보
- 요청된 전체 리소스
- 각 파드가 노드에 요청하는 CPU 및 메모리 정보

```
$ kubectl describe nodes {node name}
```


&nbsp;  

### 1.3 공통 kubectl 커맨드

&nbsp;  

**1.3.1 네임 스페이스(namespace)**  
네임스페이스는 객체들의 집합을 담고있는 폴더로 쿠버네티스의 클러스터 객체들을 관리하기 위해 사용합니다.

```
// 특정 namespace 참조
$ kubectl {command} --namespace={namespace}
```

```
// 모든 namespace 참조
$ kubectl {command} --all-namespaces
```

>**참고) 초기 네임스페이스**
> 
> - **default** : 기본 네임스페이스
> 
> - **kube-system** : 쿠버네티스 시스템에서 생성한 오브젝트를 위한 네임스페이스
> 
> - **kube-public** : 모든 사용자(인증받지 않은 사용자도 포함)가 읽기 권한으로 접근할 수 있으며, 관례적으로 만들어져 있지만 아무 리소스도 없고, 꼭 사용해야하는 것은 아니다.
> 
> - **kube-node-lease** : kubernetes node의 가용성을 체크하기 위한 네임스페이스


&nbsp;  

**1.3.2 컨텍스트(context)**  
기본 네임스페이스를 영구적으로 변경하고 싶다면, 사용할 수 있습니다.  

>_Config 파일을 이용하여 여러 개의 클러스터에 쉽게 접근할 수 있도록 하는 것으로 주로 $HOME/.kube/config 경로에 존재하는 kubectl config 파일에 클러스터를 찾고 인증하는 방법등이 기록되어있다._


```
// 기본 네임스페이스를 가진 새로운 context 생성
$ kubectl config set-context {context name} -namespace={namespace}
```

```
// 생성한 context 사용
$ kubectl config user-context {context name}
```



&nbsp;  

**1.3.3 쿠버네티스 객체 조회**  
쿠버네티스 객체는 고유의 HTTP 경로에 존재하며, kubectl 커맨드는 해당 경로에 존재하는 쿠버네티스 객체에 접근하기 위해 HTTP 요청을 보냅니다.

```
// 특정 pod 객체 조회
$ kubectl get pods {pod name} --o

// 전체 pod 조회
$ kubectl get pods
```

```
// 특정 객체 상세정보 조회
$ kubectl describe {resource name} {객체 name}
```

> **-o** 플래그를 추가하면 객체의 좀더 많은 정보를 확인할 수 있다.  



&nbsp;  

**1.3.4 쿠버네티스 객체 생성, 수정, 삭제**  
쿠버네티스 객체들은 JSON 또는 YAML 파일로 표현되며 해당 파일들을 사용해 쿠버네티스 서버에 객체를 생성, 수정, 삭제 할 수 있습니다.

```
// 객체 생성
$ kubectl apply -f {yaml}
$ kubectl create -f {yaml}
```

```
// 객체 수정 - 기존 객체와 다른 부분만 수정
$ kubectl apply -f {yaml}
```

```
// 대화식 편집 - 객체의 최신 상태를 내려받고 정의되어 있는 상태 편집
$ kubectl edit {resource name} {객체 name}
```

```
// 이전 이력 기록
$ kubectl apply -f {yaml} view-last-applied
```

> **_apply 커맨드는 애노테이션(annotation)을 사용해 이전 config 이력을 객체안에 기록할 수 있다._**
> 
> - **edit-last-applied** : 마지막 변경 상태
> 
> - **set-last-applied** : 마지막 적용된 설정
> 
> - **view-last-applied** : 마지막 적용된 상태 

```
// 객체 삭제  
$ kubectl delete -f {yaml}
$ kubectl delete {resource name} {객체 name}
```  


&nbsp;  

**1.3.5 쿠버네티스 객체 라벨링**  
라벨은 쿠버네티스 클러스터에서 객체를 식별하고 선택적으로 그룹화 하는데 사용됩니다.

```
// 라벨 추가 - 라벨은 "key=value"의 형태
$ kubectl label pods {pod name} {label}
```

```
// 라벨 덮어쓰기
$ kubectl label pods {pod name} {label} --overwrite
```

```
//라벨 삭제
$ kubectl label pods {pod name} {label key}-
```



&nbsp;  

**1.3.6 쿠버네티스 디버깅 커맨드**  
$ kubectl 은 디버깅 할 수 있도록 여러가지 커맨드를 제공합니다.

```
// 현재 동작중인 컨테이너 로그 확인
$ kubectl logs {pod name}

// pod 안에 여러 컨테이너가 있을 경우
$ kubectl logs {pod name} -c {container}
```

```
// 현재 실행중인 컨테이너에 커맨드 실행 - 좀 더 자세한 디버깅을 수행할 수 있도록 대화형 셸 제공
$ kubectl exec -it {pod name} --bash
```

```
// 실행 중인 프로세스에 접근하여 프로세스에 입력값을 보낼 수 있다.

$ kubectl attach -it {pod name}
```

```
// 컨테이너에 파일 붙혀넣기, 가져오기

// 컨테이너 - 로컬
$ kubectl cp {pod name}:{path/to/remote/file} {path/to/local/file}

// 로컬 - 컨테이너
$ kubectl cp {path/to/local/file} {pod name}:{path/to/remote/file}
```  

```
// 네트워크를 통해 파드에 접근 - local port에서 container port로 트래픽을 전달하는 연결 생성
$ kubectl port-forward {pod name} {local port}:{container port}
```  

```
// 클러스터의 리소스 사용 현황

// 절대 단위(코어), 가용 리소스의 백분율(전테 코어 수)을 사용해 노드들의 CPU 및 메모리 사용량을 보여준다.
$ kubectl top nodes

// 모든 파드의 목록 및 리소스 사용량을 보여준다. - "--all-namespaces" 플래그를 추가하면 클러스터 내 모든 파드의 리소스 사용량을 확인할 수 있다. 
$ kubectl top pods
```



&nbsp;  

**1.3.7 kubectl 도움말**

```
$ kubectl help

$ kubectl help {command name}
```



