---
title:  "[Docker] Docker Desktop 설치 및 파이썬 스크립트 이미지 생성"
excerpt: "Docker Desktop 설치 및 파이썬 스크립트 이미지 생성"
author: 동글

categories:
  - Docker
tags:
  - [Docker, Python]

toc: true
toc_sticky: true
 
date: 2022-07-23
last_modified_at: 2022-07-23
---
Window 10

### 1. Docker Desktop 설치

&nbsp;  
**1.1 Docker desktop 설치**  

**1.1.1** Docker Desktop on Windows 다운로드  

> [https://docs.docker.com/desktop/windows/install/](https://docs.docker.com/desktop/windows/install/)  

&nbsp;  

**1.1.2** Docker Desktop for Windows 버튼 클릭  
![캡처](https://user-images.githubusercontent.com/109357459/180602541-95104567-cfbf-458c-ac92-b296a74cfccd.PNG)  

> WSL 2 installation is incomplete 오류 발생 시 안내 메시지의 kernel update링크 클릭 후 WSL
파일 다운로드, 설치  

&nbsp;  

### 2. Python 파일 생성

**2.1  파이썬 파일 생성**
- test.py

```
Print(“Hello World!”) 
```


&nbsp;  

**2.2  Dockerfile 작성**
- Dockerfile

```
FROM python
 WORKDIR C:\project\t3q.ai\docker_study (py파일과 Dockerfile있는 위치)
 COPY . .
 CMD ["test.py"]
 ENTRYPOINT ["python3"] 
```  

&nbsp;  

**2.3   Docker이미지 생성**

```
cd py파일과 Dockerfile있는 위치

docker build -t 지정할도커이미지이름 .

docker run 지정한도커이미지이름
```  

&nbsp;  

**2.4 Docker이미지 생성 확인**  

```
docker images 
```



