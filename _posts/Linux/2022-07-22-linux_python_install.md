---
title:  "[Linux] Linux Python 설치"
excerpt: "Linux Python 설치"
author: 동글

categories:
  - Linux
tags:
  - [Linux, Python]

toc: true
toc_sticky: true
 
date: 2022-07-22
last_modified_at: 2022-07-22
---

Ubuntu - 20.04

들어가기에 앞서...

Ubuntu 20.04 에는 Python 3.8이 설치되어 있습니다.  
해당 Python을 삭제하거나 우선순위를 변경하면 터미널이 열리지 않습니다.  
~~절대로... 변경하거나 삭제하지 마세요...~~  

```
// 터미널 안열릴 경우 아래 명령어 입력 후 기존 파이썬 선택
sudo update-alternatives –config python3
```

또한
  
`make install`로 설치 시 `/usr/local/bin` 에 설치 되나 `make uninstall`도 없으니 그냥 `apt-get`으로 설치 하는 것이 건강에 좋습니다.  

```
// make install python 삭제(/usr/local/bin에 설치되었을 경우)
rm -f /usr/local/bin/python3.7
rm -f /usr/local/bin/pip3.7
rm -f /usr/local/bin/pydoc
rm -rf /usr/local/bin/include/python3.7
rm -f /usr/local/lib/libpython3.7.a
rm -rf /usr/local/lib/python3.7

rm -f /usr/local/share/man/python3.7.6
rm -rf /usr/local/lib/pkgconfig
rm -f /usr/local/bin/idle
rm -f /usr/local/bin/easy_install-3.7
```

&nbsp;  

### Ubuntu 20.04 Ptyon 설치  

&nbsp;  

**1. apt Python 설치**

```jsx
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python3.7
```  

&nbsp;  

**2. Python-dev 버전지정 설치(종속성 설치)**  

```jsx
sudo apt-get install -y python3.7-dev
```  

&nbsp;  

**3. Python 여러 버전 설치 후 버전 선택사용(윈도우 X)**

기존 `python3.8`이 깔려있으므로 앞에 `python3.7 -m` 안붙히면 `python3.8`을 사용합니다.

```jsx
python3.7 -m pip install
```

&nbsp;  

**4. pipenv 버전선택 사용**

`pipenv` 버전선택은 `—python 3.7` 처럼 띄어쓰기 해줘야합니다.

```
python3.7 -m pipenv —python 3.7 install
```  

&nbsp;  

**5. pipenv 환경변수**

```
// ~/.bashrc에 등록 
// 해당 환경변수 등록 후 가상환경 생성 할 프로젝트 폴더에 들어가서 pipenv install 하면 해당 폴더에 .venv 폴더 생성
// 기존은 /root/.local/share/virtualenvs/ 아래에 있음

export PIPENV_VENV_IN_PROJECT=1
```

```
// pipenv install 명령어 실행 후 아래와같은 오류가 나거나 환경변수를 등록했음에도 불구하고 기존의 경로에 생성된다면
// root에서 pipenv install 진행

No module named 'virtualenv.seed.embed.via_app_data'
```








