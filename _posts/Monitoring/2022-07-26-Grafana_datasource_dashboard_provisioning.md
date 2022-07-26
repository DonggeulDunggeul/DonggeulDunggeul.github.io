---
title:  "[Monitoring] Grafana-datasource,dashboard provisioning"
excerpt: "Grafana datasource,dashboard provisioning"
author: 동글

categories:
  - Monitoring
tags:
  - [Monitoring, Grafana]

toc: true
toc_sticky: true
 
date: 2022-07-26
last_modified_at: 2022-07-26
---

Grafana 8.5.0 

provisioning을 통해 datasource와 dashboard를 미리 설정해 둘 수 있으며, 동일한 dashboard를 여러 환경에 적용할 때에 용이합니다.   

아래의 방법은 kubernetes 환경에서 datasource,dashboard를 provisioning하여 grafana를 구성하는 방법입니다.


&nbsp;  


## 1. Grafana provisioning - datasource, dashboard 
>참고: [Provision Grafana](https://grafana.com/docs/grafana/latest/administration/provisioning/)

&nbsp;  


### 1.1 폴더생성, 권한 변경

```
// 폴더의 권한을 변경해주지 않으면 권한 오류가 발생합니다.
mkdir /monitoring/grafana/conf
chmod -R 777 /monitoring/grafana/conf

mkdir /monitoring/grafana/data
chmod -R 777 /monitoring/grafana/data
```

&nbsp;  

### 1.2 pv,pvc 생성
- grafana-conf-pv.yaml  

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: grafana-conf-pv
spec:
  storageClassName: ''
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /monitoring/grafana/conf
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-conf-pvc
spec:
  storageClassName: ''
  capacity:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

- garfana-data-pv.yaml  

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: grafana-data-pv
spec:
  storageClassName: ''
  capacity:
    storage: 50Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /monitoring/grafana/data
  persistentVolumeReclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: grafana-data-pvc
spec:
  storageClassName: ''
  capacity:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Gi
```  

- kubectl create

```
kubectl create -f grafana-conf-pv.yaml
kubectl create -f grafana-data-pv.yaml
```  


&nbsp;  

### 1.3 datasource, dashboard configmap 생성
- config-grafana.yaml  

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: grafana-datasources
data:
  prometheus.yaml: |-
    {
        "apiVersion": 1,
        "datasources": [
            {
               "access":"proxy",
                "editable": true,
                "name": "prometheus",
                "orgId": 1,
                "type": "prometheus",
                "url": "{url}",
                "version": 1
            }
        ]
    }
```

- config-grafana-dashboard.ymal

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: grafana-dashboard-config
data:
  dashboard.yaml: |-
    {
        "apiVersion": 1,
        "providers": [
            {
               "name": "admin",
               "type": "file",
               "folder": "",
               "disableDeletion": "true",
               "allowUiUpdates": "true",
               "updateIntervalSeconds": 10,
               "editable": "true",
               "options": 
                   {
                     "path": "/etc/grafana/provisioning/dashboards/dashboard.json",
                     "foldersFromFilesStructure": "true"
                   }
            }
        ]
    }
```

> **참고) 옵션 설명**
> 
> **disableDeletion** : provisioning된 dashboard를 삭제할 수 있도록 하는 옵션
>
> **allowUiUpdates** : provisioning된 dashboard를 수정할 수 있도록 하는 옵션  

- config-grafana-dashboard-template.ymal

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: grafana-dashboard-template
  namespace: {namespace}
  labels:
    grafana_dashboard: "true"
data:
  dashboard.json: |-
    {dashboard json}
```  

>참고) [grafana dashbaord export&import](https://grafana.com/docs/grafana/latest/dashboards/export-import/)

- kubectl create

```
kubectl create -f config_grafana.yaml
kubectl create -f config-grafana-dashboard.yaml
kubectl create -f config-grafana-dashboard-template.yaml
```

&nbsp;  

### 1.4 deployment, svc 생성
- grafana-deploy.yaml  

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      name: grafana
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana:8.5.0
        ports:
        - name: grafana
          containerPort: 3000
        resources:
          limits:
            memory: "2Gi"
            cpu: "1000m"
          requests:
            memory: "1Gi"
            cpu: "500m"
        volumeMounts:
          - mountPath: /var/lib/grafana
            name: grafana-data-pv
          - mountPath: /etc/grafana
            name: grafana-conf-pv
          - mountPath: /etc/grafana/provisioning/datasources
            name: grafana-datasources
          - mountPath: /etc/grafana/provisioning/dashboards
            name: grafana-dashboard-config
          - mountPath: /etc/grafana/provisioning/dashboards
            name: grafana-dashboard-template
            readOnly: false
      volumes:
        - name: grafana-data-pv
          persistentVolumeClaim:
            claimName: grafana-data-pvc
        - name: grafana-datasources
          configMap:
              defaultMode: 420
              name: grafana-datasources
        - name: grafana-dashboard-config
          configMap:
              defaultMode: 420
              name: grafana-dashboard-config
        - name : grafana-dashboard-template
          configMap:
              defaultMode: 420
              name: grafana-dashboard-template
        - name: grafana-conf-pv
          persistentVolumeClaim:
            claimName: grafana-conf-pvc
```

- grafana-svc.yaml  

```
apiVersion: v1
kind: Service
metadata:
  name: grafanas
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/port:   '3000'
spec:
  selector:
    app: grafana
  type: ClusterIP
  ports:
    - port: 3000
      targetPort: 3000
```

- kubectl create

```
kubectl create -f grafana-svc.yaml
kubectl create -f grafana-deploy.yaml
```

&nbsp;  

### 1.5 home dashboard 설정

- grafana.ini

```
default_home_dashboard_path = /etc/grafana/provisioning/dashboards/dashbaord.json
```
