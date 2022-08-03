---
title:  "[Kubernetes] Kubernetes - 컨피그맵, 시크릿"
excerpt: "Kubernetes - ConfigMap, Secret"
author: 동글

categories:
  - Kubernetes
tags:
  - [Kubernetes]

toc: true
toc_sticky: true
 
date: 2022-07-28
last_modified_at: 2022-08-01
---

참고 Post: [Grafana Provisining -  ConfigMap 생성](https://donggeuldunggeul.github.io/posts/Grafana_datasource_dashboard_provisioning/#13-datasource-dashboard-configmap-%EC%83%9D%EC%84%B1)

참고: [쿠버네티스 레퍼런스 - 컨피그맵](https://kubernetes.io/ko/docs/concepts/configuration/configmap/),   
[쿠버네티스 레퍼런스 - 시크릿](https://kubernetes.io/ko/docs/concepts/configuration/secret/),  
[GKE - ConfigMap](https://cloud.google.com/kubernetes-engine/docs/concepts/configmap?hl=ko),  
[GKE - Secret](https://cloud.google.com/kubernetes-engine/docs/concepts/secret?hl=ko),  
ORELLY 쿠버네티스 시작하기    


## 1. 컨피그맵(ConfigMap) 과 시크릿(Secret)  
컨테이너 이미지는 가능한 한 재사용할 수 있게 만드는 것이 좋습니다.  
동일한 이미지를 개발환경, 테스트환경, 운영환경에 사용할 수 있어야 합니다.   

컨피그맵(ConfigMap) 과 시크릿(Secret)을 이용하면 컨테이너 이미지에서 애플리케이션 코드와 환경별 구성을 분리할 수 있어 컨테이너 이미지를 보다 범용적으로 사용할 수 있습니다.

![콘피그맵](https://user-images.githubusercontent.com/109357459/181524399-722cac3f-e06f-4279-aa36-5b078e712797.jpg)

> **컨피그맵(ConfigMap)** 은 보안 또는 암호화를 제공하지 않으므로, 비밀번호, 키, 토큰과 같은 민감한 정보를 클러스터에서 사용하려면 **시크릿(Secret)** 을 사용

&nbsp;  


### 1.1 컨피그맵(ConfigMap)  
컨피그맵(ConfigMap)은 키-값 쌍으로 기밀이 아닌 데이터를 저장하는 데 사용하는 API 오브젝트로 컨테이너 환경 또는 커맨드 라인을 정의할 때 사용할 수 있는 변수의 집합으로 생각할 수 있습니다.  

>**컨피그맵은 파드(Pod)** 가 실행되기 직전에 파드와 결합이되기 때문에 컨피그맵을 이용하여 많은 어플리케이션에서 컨테이너 이미지 및 파드에 대한 정의 자체를 재사용할 수 있다.  

&nbsp;  

**1.1.1 컨피그맵 생성**  

- **파일 시스템** - 파일  

```
kubectl create configmap {name} \
    --from-file {file_path_1} \
    --from-file {file_path_2}
```

- **파일 시스템** - 디렉터리   

```
kubectl create configmap {name} \
    --from-file {directory_path}
```

- **literal (문자)**  

```
kubectl create configmap {name} \
    --from-literal={key_1}={value_1} \
    --from-literal={key_2}={value_2} 
```  

&nbsp;  

- **생성된 컨피그맵 출력**

```
// 생성한 컨피그맵 객체에 해당하는 YAML 출력
kubectl get configmap {name} -o yaml
```


&nbsp;  

**1.1.2 컨피그맵(ConfigMap) 사용**  


>컨피그맵을 사용하는 세 가지 주요 방법  
>
>- **파일 시스템** : 키 이름에 따라 각 항목에 대한 파일 생성
>
>- **환경 변수** : 환경 변수 값 동적 설정
>
>- **커맨드라인 동적 생성** : 컨테이너에 대한 커맨드 라인 동적 생성  


- **파일 시스템**  
파드 내에 새로운 볼륨을 생성하고 해당 볼륨을 컨피그맵 볼륨으로 정의하고 마운트할 컨피그맵을 가리킵니다. 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: {pod name}
spec:
  containers:
  - name: {pod name}
    image: {image name}
    volumeMounts:
    - name: {name}
      mountPath: "{파일이 마운트될 path}"
      readOnly: true
  volumes:
  - name: {name}
    configmap:
      name: {configmap name}
```  

- **literal (문자)**  
env 부분에 환경변수를 정의하면 키를 참조하여 데이터를 읽어올 수 있습니다.


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: {pod name}
spec:
  containers:
    - name: {pod name}
      image: {image name}
      command: 
        - "$(PARAMETER_NAME_1)"
      env:
        - name: {PARAMETER_NAME_1}
          valueFrom:
            configMapKeyRef:
              name: {configmap name}
              key: {key_1}
        - name: {PARAMETER_NAME_2}
          valueFrom:
            configMapKeyRef:
              name: {configmap name}
              key: {key_2}
  restartPolicy: Never
```

> _커맨드 라인 인수는 환경변수를 기반으로 동작하는데  **$({환경변수 이름})** 구문으로 인수를 추가할 수 있다_ 

&nbsp;  

### 1.2 시크릿(Secret)  

시크릿은(Secret) 컨피그맵에 담기 어려운 비밀번호, 보안 토큰등 민감한 데이터를 클러스터에 저장하는 보안 객체입니다.  

>시크릿은 클러스터의 etcd스토리지에 평문으로 저장되므로 클러스터 관리 권한이 있는 사람은 클러스터에 위치한 모든 시크릿에 접근할 수 있다. 따라서 요구사항의 수준에 따라 쿠버네티스 시크릿은 충분한 보안성을 제공하지 않을 수 있다. 

> 최신 버전의 쿠버네티스에서는 일반적인 클라우드 키 저장소와의 통합을 통해 사용자가 제공한 키로 시크릿을 암호화할 수 있는 기능이 추가됐다.

> 대부분의 클라우드 키 저장소는 쿠버네티스의 유연한 볼륨과 통합되어 있어, 클라우드 제공업체의 키 저장소를 사용할 수 있다.


&nbsp;  

**1.2.1 시크릿 생성**  
시크릿은 하나 이상의 데이터 요소를 키/값 쌍의 모음으로 유지합니다.  

>시크릿 생성 방법  
>- [kubectl으로 시크릿 생성](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/)   
>- [구성파일에서 시크릿 생성](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-config-file/)  
>- [사용자 커스텀 시크릿 생성](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kustomize/)

```
kubectl create secret {Secret_Type} {Secret_Name} {Data}
```

>- **Secret_Type(시크릿 유형)** 
> 
> >- **generic** : 주로 사용되는 유형으로 로컬 파일, 디렉토리 또는 리터럴 값에서 시크릿 생성
>>
>> - **docker-registry** : Docker 레지스트리에 사용하기 위한 dockercfg 시크릿 생성, Docker 레지스트리로 인증을 수행하기위해 사용
>>
>>  - **tls** : 지정된 공개 키/비공개 키 쌍에서 TLS 시크릿 생성, 공개 키/비공개 키 쌍이 이미 존재해야하며 공개 키 인증서는 PEM으로 인코딩되고 지정된 비공개 키와 일치해야 함

>- **Data** 
>
>>- **--from-file, --from-env-file** : 표시된 하나 이상의 구성 파일을 포함하는 디렉터리의 경로
>>- **--from-literal** : 지정되는 키/값 쌍  

- **파일 시스템** - 파일  

```
kubectl create secret {Secret_Type} {Secret_Name} \
    --from-file {file_path_1} \
    --from-file {file_path_2}
```

- **파일 시스템** - 디렉터리   

```
kubectl create secret {Secret_Type} {Secret_Name} \
    --from-file {directory_path}
```

- **literal (문자)**  

```
kubectl create secret {Secret_Type} {Secret_Name} \
    --from-literal={key_1}={value_1} \
    --from-literal={key_2}={value_2} 
```  

&nbsp;  

- **생성된 시크릿 출력**

```
// 생성한 시크릿에 해당하는 YAML 출력
kubectl get secret {Secret_Name} -o yaml
```


&nbsp;  

**1.2.2 시크릿 사용**  

>- [기존 도커 자격증명을 기반으로 시크릿 생성 및 사용](https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/#registry-secret-existing-credentials)
>- [Pod에서 파일로 시크릿 사용](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-files-from-a-pod)  
>- [볼륨에서 시크릿 사용](https://kubernetes.io/docs/concepts/configuration/secret/#consuming-secret-values-from-volumes)


>  API를 직접 호출하는 애플리케이션은 쿠버네티스 REST API를 통해 시크릿을 사용할 수 있다. 그러나 애플리케이션의 이식성을 최대한 유지하는 것을 목표로 해야하며, 쿠버네티스에서 잘 실행될 뿐만 아니라 다른 플랫폼에서도 별도의 수정 없이 실행되어야 한다.

&nbsp;  

### 1.3 컨피그맵(ConfigMap), 시크릿(Secret) 관리  
컨피그맵과 시크릿은 쿠버네티스 API를 통해 관리됩니다.

**1.3.1 조회**

```
// configmap
kubectl get configmaps
```

```
// secret
kubectl get secrets
```

```
// 자세한 정보
kubectl describe configmap {configmap name}
```

&nbsp;  

**1.3.2 생성**  

```
- --from-file=<파일 이름> : 파일 이름과 동일한 시크릿 데이터 키를 사용해 파일에서 적재
- --from-file=<키>=<파일 이름> : 명시적으로 지정된 시크릿 데이터 키를 사용해 파일에서 적
- --from-file=<디렉토리> : 지정된 디렉토리 내에서 키 이름으로 사용할 수 있는 모든 파일을 적
- --from-literal=<키>=<값> : 지정된 키/값 쌍을 직접 적용
```
