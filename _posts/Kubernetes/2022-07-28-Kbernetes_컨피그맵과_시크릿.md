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
last_modified_at: 2022-07-28
---

참고 Post: [Grafana Provisining -  ConfigMap 생성](https://donggeuldunggeul.github.io/posts/Grafana_datasource_dashboard_provisioning/#13-datasource-dashboard-configmap-%EC%83%9D%EC%84%B1)

참고: [쿠버네티스 레퍼런스 - 컨피그맵](https://kubernetes.io/ko/docs/concepts/configuration/configmap/), [쿠버네티스 레퍼런스 - 시크릿](https://kubernetes.io/ko/docs/concepts/configuration/secret/), [GKE - ConfigMap](https://cloud.google.com/kubernetes-engine/docs/concepts/configmap?hl=ko), [GKE - Secret](https://cloud.google.com/kubernetes-engine/docs/concepts/secret?hl=ko),  ORELLY 쿠버네티스 시작하기    


## 1. 컨피그맵(ConfigMap) 과 시크릿(Secret)

![콘피그맵](https://user-images.githubusercontent.com/109357459/181524399-722cac3f-e06f-4279-aa36-5b078e712797.jpg)

컨테이너 이미지는 가능한 한 재사용할 수 있게 만드는 것이 좋습니다.  
동일한 이미지를 개발환경, 테스트환경, 운영환경에 사용할 수 있어야 합니다.   

컨피그맵(ConfigMap) 과 시크릿(Secret)을 이용하면 컨테이너 이미지에서 애플리케이션 코드와 환경별 구성을 분리할 수 있어 컨테이너 이미지를 보다 범용적으로 사용할 수 있습니다.

> **컨피그맵(ConfigMap)** 은 보안 또는 암호화를 제공하지 않으므로, 비밀번호, 키, 토큰과 같은 민감한 정보를 클러스터에서 사용하려면 **시크릿(Secret)** 을 사용

&nbsp;  


### 1.1 컨피그맵(ConfigMap)  
컨피그맵(ConfigMap)은 키-값 쌍으로 기밀이 아닌 데이터를 저장하는 데 사용하는 API 오브젝트로 컨테이너 환경 또는 커맨드 라인을 정의할 때 사용할 수 있는 변수의 집합으로 생각할 수 있습니다.  

>컨피그맵은 파드(Pod)가 실행되기 직전에 파드와 결합이되기 때문에 컨피그맵을 이용하여 많은 어플리케이션에서 컨테이너 이미지 및 파드에 대한 정의 자체를 재사용할 수 있다.  

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

- **파일 시스템**  
파드 내에 새로운 볼륨을 생성하고 해당 볼륨을 컨피그맵 볼륨으로 정의하고 마운트할 컨피그맵을 가리킵니다. 

```
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


```
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

>컨피그맵을 사용하는 세 가지 주요 방법  
>
>- **파일 시스템** : 키 이름에 따라 각 항목에 대한 파일 생성
>
>- **환경 변수** : 환경 변수 값 동적 설정
>
>- **커맨드라인 동적 생성** : 컨테이너에 대한 커맨드 라인 동적 생성  


&nbsp;  

### 1.2 시크릿(Secret)  

220729 추가 예정  

### 1.3 컨피그맵(ConfigMap), 시크릿(Secret) 관리  

220729 추가 예정  

