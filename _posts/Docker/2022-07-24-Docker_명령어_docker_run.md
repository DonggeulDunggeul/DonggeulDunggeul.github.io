---
title:  "[Docker] Docker 명령어 - docker run"
excerpt: "Docker 명령어 - docker run"
author: 동글

categories:
  - Docker
tags:
  - [Docker]

toc: true
toc_sticky: true
 
date: 2022-07-24
last_modified_at: 2022-07-25
---

>참고: ORELLY 제대로 배우는 도커

## 1. Docker 명령어  

### 1.1 run 명령어

```
docker run {옵션} {이미지} {명령어} {인자}
```

컨테이너 시작과 관련된 명령어로 지원되는 인자를 이용하여 이미지 실행, 도커파일, 네트워크, 컨테이너의 권한과 리소스 설정 등의 기능을 수행할 수 있습니다.  

&nbsp;  

**1.1.1&nbsp; -a, --attach**

옵션이 정해지지 않으면 stdout과 stderr가 연결됩니다. 옵션이 지정되지 않고 컨테이너가 대화형 모드(-i)로 시작되었다면 stdin도 연결됩니다.  


>**참고)  stdin, stdout, stderr?**  
>
>![standard-stream](https://user-images.githubusercontent.com/109357459/180648344-b36b4a76-8f67-4c6d-a657-8ae34d7b6929.png)
>
>stdin, stdout, stderr은 Linux 명령을 시작할 때 생성되는 세 개의 데이터 스트림입니다.  
>
>**stdin (표준 입력)** - 텍스트를 입력으로 받아들임   
>**stdout (표준 출력)** - 명령에서 셸로의 텍스트 출력  
>**stderr (표준 오류)** - 명령의 오류 메시지  
>
>좀 더 쉽게 풀어보자면,  
>
>- ls와 같은 명령어는 프로그램을 실행하고 그결과를 **stdout (표준 출력)** 으로 보냅니다.  
>
>- 프로그램 실행 중 오류가 발생하면 오류 내용을 **stderr (표준 오류)** 로 보냅니다.  
>
>- **stdin (표준 입력)** 으로부터 입력받은 내용을 가져옵니다.


&nbsp;  

**1.1.2&nbsp; -d, --detach**  
컨테이너를 "분리(detached)" 모드로 실행합니다. 데몬모드라고도 불리우며, 컨테이너를 백그라운드로 실행시키고 컨테이너 ID를 반환합니다.  
컨테이너가 일반 프로세스가 아닌 데몬프로세스로 실행되어 프로세스가 끝나도 유지됩니다.


&nbsp;  

**1.1.3&nbsp; -i, --interactive**  
연결되지 않은 상태라도 stdin을 열어 둔 상태로 유지합니다. (사용자가 입출력 할 수 있는 상태)   
일반적으로 컨테이너 세션을 시작하기 위해서 -t와 같이 사용됩니다.


&nbsp;  

**1.1.4&nbsp; -t, --tty**  
가상-TTY(일반 CLI 콘솔)를 할당합니다.  
일반적으로 대화형 컨테이너를 시작하기 위해서 -i 와 같이 사용됩니다.


&nbsp;  

**1.1.5&nbsp; --restart**   

```
## 예시) 컨테이너를 시작하고, 0이 아닌 코드로 종료된 경우에 재시작을 n번 시도한다.
docker run --restart onfailure:{n} {container}
```

docker 가 종료된 컨테이너가 어떤 경우에 재시작할지 구성합니다. 
- no: 컨테이너를 재시작 하지 않는다.  
- always: 종료 상태에 상관업이 항상 재시작을 시도한다.  
- on-failure: 컨테이너가 0이 아닌 상태로 종료된 경우에만 재시작을 시도한다.  

>**참고) docker 종료 code**  
>  
>- **exit code 0** : 성공적으로 종료된 경우  
>
>- **exit code 125** : 도커 명령어 자체가 실패한 경우  
>
>- **exit code 126** : 컨테이너의 커맨드가 실패한 경우  
>
>- **exit code 127** : 컨테이너의 커맨드가 존재하지 않는 경우  
>
>- **exit code 128+n** : 리눅스 시그널 n에 해당하는 오류가 발생한 경우 
>

>
>**참고) Linux 시그널**  
>
> _$ kill -l_  
>
>|NO|signal|설명|
>|---|-----|-----|
>|1	|SIGHUP(HUP)	|연결 끊기. 프로세스의 설정파일을 다시 읽는데 사용된다.|
>|2	|SIGINT(INT)	|인터럽트|
>|3	|SIGQUIT(QUIT)|	종료|
>|4	|SIGILL(ILL)	|잘못된 명령|
>|5	|SIGTRAP(TRAP)|	트렙 추적|
>|6	|SIGIOT(IOT)	|IOT 명령|
>|7	|SIGBUS(BUS)	|버스 에러|
>|8	|SIGFPE(FPE)	|고정소수점 예외|
>|9	|SIGKILL(KILL)|	죽이기. 이 시그널은 잡히지 않는다.|
>|10	|SIGUSR1(USR1)|	사용자 정의 시그널1|
>|11	|SIGSEGV(SEGV)|	세그멘테이션 위반|
>|12	|SIGUSR2(USR2)|	사용자 정의 시그널2|
>|13	|SIGPIPE(PIPE)|	읽을 것이 없는 파이프에 대한 시그널|
>|14	|SIGALRM(ALRM)|	경고 클럭|
>|15	|SIGTERM(TERM)|	소프트웨어 종료 시그널, 일반적으로 kill 시그널이 전송되기 전에 전송된다. 잡히는 시그널이기 때문에 종료되는 것을 트랙할 수 있다.|
>|16	|SIGTKFLT	|코프로세서 스택 실패|
>|17	|SIGCHLD(CHLD)|	자식 프로세스의 상태변화|
>|18	|SIGCONT(CONT)|	STOP 시그널 이후 계속 진행할 때 사용.|
>|19	|SIGSTOP(STOP)|	정지. 이 시그널 역시 잡을 수 없다.|
>|20	|SIGTSTP(TSTP)|	키보드에 의해 발생하는 시그널로 Ctrl+Z로 생성된다.|
>|21	|SIGTTIN	|백그라운드에서의 제어터미널 읽기|
>|22	|SIGTTOU	|백그라운드에서의 제어터미널 쓰기|
>|23	|SIGURG	|소켓에서의 긴급한 상태|
>|24	|SIGXCPU	|CPU 시간 제한 초과 setrlimit(2) 메뉴얼 패이지 참조|
>|25	|SIGXFSZ	|파일 크기제한 초과 setrlimit(2) 메뉴얼 패이지 참조|
>|26	|SIGVTALRM	|가상 시간 경고 setitimer(2) 메뉴얼 패이지 참조|
>|27	|SIGPROF	|프로파일링 타이머 경고. setitimer(2) 메뉴얼 페이지 참조|
>|28	|SIGWINCH	|윈도우 사이즈 변경|
>|29	|SIGIO	|기술자에서 입출력이 가능함. fcntl(2) 메뉴얼 참조|
>|30	|SIGPWR	|전원 실패|
>|31	|UNUSED	|사용 안함|
>


&nbsp;  

**1.1.6&nbsp; -e --env**   
예시 환경변수 docker image: [bitnami/keycloak](https://github.com/bitnami/bitnami-docker-keycloak#admin-credentials)

```
## 예시) 
docker run -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=password
```

컨테이너 내부의 환경 변수들을 설정할 수 있습니다.


&nbsp;  

**1.1.7&nbsp; --rm**  
컨테이너를 일회성으로 실행할 때 자주 쓰이며, 컨테이너가 종료되면 자동으로 컨테이너와 관련된 리소스(파일 시스템, 볼륨)까지 삭제합니다.


&nbsp;  

**1.1.8&nbsp; -h --hostname**  
컨테이너의 호스트 이름을 설정합니다.
> hostname은 네트워크로 연결된 서버, 컴퓨터들을 구분하기 위한 이름입니다.  


&nbsp;  

**1.1.9&nbsp; --name**  
컨테이너에 이름을 할당하여 다른 도커 명령어에서 해당 컨테이너를 찾을 때 사용합니다.  


&nbsp;  

**1.1.10&nbsp; -v, --volume**   
볼륨을 컨테이너에 마운트 할 때 사용합니다.

>**참고) Docker의 데이터 관리 - 볼륨의 종류**  
>
>
>**1. Bind Mount**  
>![다운로드 (1)](https://user-images.githubusercontent.com/109357459/180802982-d9dcfe37-dea4-48fc-baa8-1095980a5534.png)  
>호스트 환경의 특정 경로를 컨테이너 내부 볼륨 경로와 연결하여 마운트합니다.
>```
>// 예시 - 컨테이너 내부의 /data디렉터리가 볼륨으로 만들어지고, 해당 디렉터리 내부에서 가지고 있는 모든 파일은 볼륨으로 복사됩니다.
>
>$ 
>```
>
>
>&nbsp;  
>
>**2. Volume(Docker에서 권고하는 방식)**  
> ![다운로드](https://user-images.githubusercontent.com/109357459/180801044-09822118-d638-415a-9a6e-0e1900004321.png)
> Docker 엔진이 관리하는 도커 스토리지 디렉토리에 새 디렉토리를 생성하여 컨테이너 내부의 볼륨 데이터를 저장하는 방식 입니다.
>
>
>
>```
>// 예시 - 컨테이너 내부의 /data디렉터리가 볼륨으로 만들어지고, 해당 디렉터리 내부에서 가지고 있는 모든 파일은 볼륨으로 복사됩니다.
>
>$ docker run -it --name container-test -h CONTAINER -v /data debian /bin bash
>```
>
>```
>// 호스트에서 볼륨 위치 확인
>$ docker inspect -f {{.MOUNTS}} container-test
>```
>
>&nbsp;  
>
>**3. Tmpfs Mount**  
> 컨테이너 내부에 저장된 볼륨을 호스트의 파일 시스템이 아닌, 메모리에 저장하는 방식 입니다.  
> 컨테이너가 살아있는 동안에만 메모리에 저장되어 있기 때문에 데이터를 유지하지 않으려는 경우에 적합합니다.
>


