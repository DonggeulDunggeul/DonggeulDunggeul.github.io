---
title:  "[Side Project] TAT(This and That) 로컬 개발환경 세팅 및 버전 정보"
excerpt: "[Side Project] TAT(This and That) 로컬 개발환경 세팅 및 버전 정보"
author: 둥글동글

categories:
  - Side Project
tags:
  - [Side Project, TAT]

toc: true
toc_sticky: true
 
date: 2022-08-21
last_modified_at: 2022-08-21
---
# 로컬 개발환경 세팅 및 버전 정보


# OS: Window 10

## TAT개발 환경 버전 정보

> |Category|Name|Version|remarks|
> |:---|:---|:---|:---|
> |Programming language|JAVA|OpenJDK 11.0.1||
> |Programming language|Python|3.8.5||
> |IDE|sts4 for Eclipse|4||

## 프로젝트 디렉토리
- 개발환경 세팅 시 필요 파일들이 들어있는 디렉토리
```
C:\TAT\Common
```  
- TAT 소스 디렉토리
```
C:\TAT\Source
```


## TAT개발 환경 세팅(Local)
## 1.사전준비

### 1.1 Source 폴더, 사용자 지정 디렉토리 생성 후 해당 경로 이동
```
$ cd C:\TAT\Source\{사용자_지정_디렉토리}
```
### 1.2 Source git clone
```
$ git clone https://github.com/DonggeulDunggeul/this_and_that.git
```
### 1.3 Common 폴더 생성
```
C:\TAT\Common
```

### 1.4 Programming language 설치
- **1.4.1 JAVA 설치**
> [다운로드 링크 - 11.0.1 (build 11.0.1+13)](https://jdk.java.net/archive/)  
> 1. `Windows / x64` 오른쪽의 `zip` 클릭하여 `openjdk-11.0.1_windows-x64_bin.zip` 파일을 `C:\TAT\Common` 디렉토리에 다운로드  
> 2. `C:\TAT\Common` 디렉토리에 `openjdk-11.0.1_windows-x64_bin.zip` 파일 압축 해제  
> 3. 환경 변수 등록  
> 
> |Category|환경 변수|
> |:---|:---|
> |시스템 환경변수|C:\TAT\Common\jdk-11.0.1|  
> |Path|%JAVA_HOME%\bin|
> 
> 4. 확인  
> ```
> $ echo %JAVA_HOME%
> C:\TAT\Common\jdk-11.0.1
> 
> $ javac -version
> javac 11.0.1
> ```  
>  &nbsp;  

- **1.4.2 Python 설치**
> [다운로드 링크 - Python 3.8.5](https://www.python.org/downloads/windows/) 
> 0. `C:\TAT\Common\Python38` 디렉토리 생성
> 1. `Windows x86-64 executable installer` 다운로드 및 실행
> 2. `Add Python 3.8 to PATH` 체크
> 3. `Customize installation` 선택
> 4. `문서(Documentation)`, 파이썬 설치 툴인 `pip(Package Installer for Python)` 선택
> 5. `Advanced Options` 를 선택한 후 설치 위치를 지정(`C:\TAT\Common\Python38`)
> 6. install
> 7. 확인
> 
> ```
> $ python -V
> Python 3.8.5
> ```  
> &nbsp;

&nbsp;  

- **1.4.3 sts4 설치**
> [다운로드 링크 - sts4](https://spring.io/tools)
> 0. `C:\TAT\Common\sts4` 디렉토리 생성
> 1. `Spring Tools 4 for Eclipse - Windows 64-bit` 다운로드
> 2. `C:\TAT\Common\sts4` 디렉토리에 `spring-tool-suite-4-4.15.3.RELEASE-e4.24.0-win32.win32.x86_64.self-extracting.jar` 파일 압축 해제
> 3. `contents.zip` 파일 압축 해제(알집으로 압축해제 할 경우 반디집 사용 - 알집은 파일 경로와 파일명을 포함하여 최대 255자 이상일 경우 오류 발생)
> 4. `C:\TAT\Common\sts4\sts-4.15.3.RELEASE.exe` 실행 
> 
> |Workspace|
> |:---|
> |C:\TAT\Source\{사용자_지정_디렉토리}|  
