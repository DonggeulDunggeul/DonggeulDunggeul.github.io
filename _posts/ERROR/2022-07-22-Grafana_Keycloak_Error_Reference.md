---
title:  "[Reference & Error] Grafana - Keycloak 연동"
excerpt: "Grafana - Keycloak 연동"
author: 동글

categories:
  - Reference & Error
tags:
  - [Monitoring, Grafana, Keycloak]

toc: true
toc_sticky: true
 
date: 2022-07-22
last_modified_at: 2022-07-22
---

> 관련 Post : 
[Grafana Keycloak 연동](https://donggeuldunggeul.github.io/posts/Grafana_keycloak_setting/)

### 1. Reference  
&nbsp;  
**1.1 Yaml env 환경 변수로 설정**  
docker로 grafana를 띄운 경우yaml에 별도의 환경 변수를 추가하여 설정할 수 있습니다.   
>grafana.ini에서 설정한 값이 환경 변수에 추가되어있으면 환경 변수의 설정 값을 따릅니다.  

> https://grafana.com/docs/grafana/v7.5/administration/configuration/#configure-with-environment-variables

```
// example
- env:
    - name: GF_<SectionName>_<KeyName>
      value: <value>
```

&nbsp;  
**1.2 Grafana 접속 시 keycloak 로그인 화면만 보일 때 Grafana Admin 로그인 방법**  
grafana.ini의 관련 설정을 변경 합니다.

```
# Set to true to attempt login with OAuth automatically, skipping the login screen.
# This setting is ignored if multiple OAuth providers are configured.
# true일 경우 grafana 로그인 화면을 뛰어넘는 옵션
oauth_auto_login = false

# Set to true to disable (hide) the login form, useful if you use OAuth, defaults to false
# ture일 경우 grafana 로그인 form을 감추는 옵션(유저 정보수정도 함께 가려진다.)
disable_login_form = false
```

&nbsp;  
**1.3 Grafana Grafana home dashborad 설정 - grafana UI**  
> https://grafana.com/docs/grafana/latest/administration/preferences/change-home-dashboard/

**1.3.1** 좌측 메뉴에서 Dashboards → Browse Tab → 홈 대시 보드로 사용할 대시 보드 선택  

**1.3.2** 대시 보드 상단 이름 옆 별 모양 클릭하여 즐겨 찾기  (즐겨 찾기를 해야 Configuration → Preferences Tab → Home Dashboard 선택 가능)  
![Untitled](https://user-images.githubusercontent.com/109357459/180442852-3f8ed58f-dda0-4c39-ad0a-5ddb8ea78b91.png)  

**1.3.3** 좌측 메뉴에서 Configuration → Preferences Tab → Home Dashboard 변경  

&nbsp;  
**1.4 keycloak, grafana 조직 권한 mapping** 
> https://grafana.com/docs/grafana/next/administration/manage-users-and-permissions/about-users-and-permissions/#organization-users-and-permissions  

> https://www.serpensalbus.net/posts/2021/keycloak-and-grafana-integration/  

_조직의 admin 권한 부여는 가능하나 Grafana 전체의 admin권한은 UI에서만 부여 가능합니다._  

**1.4.1** grafana client mapper 생성  
- clients → {clinet} → Mappers → create  
![Untitled (1)](https://user-images.githubusercontent.com/109357459/180443783-94d7454e-ebbe-40ba-8c3c-c8bf2a1b30f0.png)  

**1.4.2** grafana client roles 추가  
> grafana role : https://grafana.com/docs/grafana/next/administration/manage-users-and-permissions/about-users-and-permissions/  

- admin, editor, viewer role 추가  
  - clients → {clinet} → Roles → add Role  

![Untitled (2)](https://user-images.githubusercontent.com/109357459/180444071-ef3ca852-991a-4b52-b91a-addf8321bc8e.png)  

**1.4.3** user 권한 추가  
- Users → View all users → {user} → Role Mappings Tab → Client Roles → {clinet}  
- 관리자 역할 할당  
![Untitled (3)](https://user-images.githubusercontent.com/109357459/180444436-d3e702d9-2761-430f-8d51-d38a1cc64bb9.png)  

**1.4.4** grafana.ini 수정  
- /etc/grafana/grafana.ini  

```
[auth.generic_oauth]
role_attribute_path = "contains(roles[*], 'admin') && 'Admin' || contains(roles[*], 'editor') && 'Editor' || 'Viewer'"
```

**1.4.5** 확인  
-  admin 권한 부여한 keycloak user로 grafana login → 오른쪽 하단 프로필 → Preferences → Organizations → 변경된 Role 정보 확인  

![Untitled (4)](https://user-images.githubusercontent.com/109357459/180444603-19d6875a-5298-4832-afac-8fc25e9d3e35.png)  


&nbsp;  
**1.5 Grafana Json Import**   
_버튼 작동 안 할 경우 Grafana 8.5.0 버전으로 변경_  

**1.5.1** 좌측 메뉴에서 Dashboards → Browse Tab → Import → Upload JSON file → Json 파일 선택  

> overwrite 대신 새로운 dashboard로 추가하려면 json파일 하단 내용을 아래와 같이 변경  

```
"title": "{New dashboard name}",  # 새로 추가하는 dashboard의 이름
"uid": "", # 빈값으로
"version": , # 기존 version +1
```  

&nbsp;  
**1.6 Grafana Table column 편집**   
![Untitled (5)](https://user-images.githubusercontent.com/109357459/180445566-d1900463-a9ae-41f4-97d6-c390dfd4c529.png)  

&nbsp;  
**1.7 Grafana Data Export CSV**   

![Untitled (6)](https://user-images.githubusercontent.com/109357459/180445835-af307e47-2239-4977-917a-2e8f945e717c.png)  

![Untitled (7)](https://user-images.githubusercontent.com/109357459/180445838-32424b81-3d0b-4dba-96bb-9b6cc3bed1b5.png)
  
&nbsp;  

---
&nbsp; 

### 2. Erorr  
&nbsp;  
**2.1 Invalid parameter:redirect_uri**  

**2.1.1** 발생 현상  
- keycloak 로그인 화면이 출력되지 않고 Invalid parameter:redirect_uri 오류 발생  
![Untitled (8)](https://user-images.githubusercontent.com/109357459/180446392-b22e9b69-3d86-4e1e-a9ce-8a996a2af33b.png)  

**2.1.2** 발생 원인  
- client 설정 시 Valid Redirect URIs에 grafana URL 미 입력  
- ROOT_URL 미 설정  

**2.1.3** 해결 방안  
- client 설정 시 Valid Redirect URIs에 grafana URL 입력  
- ROOT_URL 설정  

```
** grafana.ini를 수정할 경우 **
[server]
# The full public facing url you use in browser, used for redirects and emails
# If you use reverse proxy and sub path specify full url (with sub path)
root_url = {grafana URL}
```

```
** yaml을 수정할 경우 **
- env:
    - name: GF_SERVER_ROOT_URL
      value: {grafana URL}
```  

&nbsp;  
**2.2 grafana URL 접속 시 로그인 화면이 아닌 메인 화면으로 접속**  

**2.2.1** 발생 현상  
- grafana URL 접속 시 로그인 화면이 아닌 메인 화면으로 접속

**2.2.2** 발생 원인  
- grafana.ini auth.anonymous 미 설정 

**2.2.3** 해결 방안  
- auth.anonymous 설정  

```
** grafana.ini를 수정할 경우 **
#################################### Anonymous Auth ######################
[auth.anonymous]
# enable anonymous access
enabled = false
```

```
** yaml을 수정할 경우 **
- env:
    - name: GF_AUTH_ANONYMOUS_ENABLED
      value: "false"
```    

&nbsp;  
**2.3 Datasource provisioning error: database is locked**  

**2.3.1** 발생 현상  
- Datasource provisioning error: database is locked에러 발생, login 불가  

**2.3.2** 발생 원인  
- grafana db locked  

**2.3.3** 해결 방안  
- sqlite3 backup 명령어 사용  

```
# 백업 폴더 생성
mkdir -p /backup

# grafana.db 복사
cp -r /grafana/data/grafana.db /grafana/data/backup/grafana.db

# 경로 이동
/grafana/data

# grafana.db open (sqlite3 CLI)
sqlite3 grafana.db

# grafana.db .backup (sqlite3 CLI)
.backup grafana.db.backup

# grafana.db 권한 변경
chmod 644 grafana.db 

# grafana 재기동
```  

&nbsp;  
**Dashboard provisining error: Datasource named ${DS_PROMETHEUS} was not found**  

**2.4.1** 발생 현상  
- Datasource named ${DS_PROMETHEUS} was not found 에러 발생

**2.4.2** 발생 원인  
- https://github.com/grafana/grafana/issues/10786#issuecomment-1122173762

**2.4.3** 해결 방안  
- dashboard json configmap 작성 시  ${DS_PROMETHEUS} 로 되어있는  변수 전체를 prometheus로 변경  

```
## 변경 전

"datasource": {
"type": "prometheus",
"uid": "${DS_PROMETHEUS}"
```

```
## 변경 전

"datasource": {
"type": "prometheus",
"uid": "${DS_PROMETHEUS}"
```

