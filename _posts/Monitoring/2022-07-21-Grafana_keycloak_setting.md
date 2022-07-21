---
title:  "[Monitoring] Grafana Keycloak 연동"
excerpt: "Grafana Keycloak 연동"
author: 동글

categories:
  - Monitoring
tags:
  - [Monitoring, Keyclaok, Grafana]

toc: true
toc_sticky: true
 
date: 2022-07-21
last_modified_at: 2022-07-21
---
![구조같은거 png](https://user-images.githubusercontent.com/109357459/180237305-03a3b1be-933b-441f-8a60-8c9961fa488f.jpg)

## 1. Keycloak
keycloak은 계정관리 및 access 관리를 제공합니다. Redhat에서 개발한 SSO(single-sign-on) 이 가능한 오픈소스입니다.

### 1.1 기능
- **SSO**
> 참고) SSO란?
>Single Sign-On의 약자로 여러 개의 사이트에서 한번의 로그인으로 여러가지 다른 사이트들을 자동적으로 접속하여 이용하는 방법

- **ID 중개 와 소셜 로그인 (OpenID, SAML, GitHub, Google 등)**

- **관리자 / 계정관리 콘솔 제공**

- **표준 프로토콜 지원(OpenID Connect + OAuth2.0, SAML)**

- **Client Adapters (다수의 플랫폼과 프로그래밍 언어가 사용가능한 adapter를 가짐)**

### 1.2 용어
- **OIDC**
> OAuth 가 권한 부여만 다루는 것이라면 OIDC 는 OAuth 를 포함하여 인증과 권한부여를 모두 포함한 것이다. SSO 의 구현을 위한 수단으로 사용된다.

- **Realm**
>인증, 권한 부여가 적용되는 범위를 나타내는 단위이다. SSO 를 적용한다고 했을때 해당 SSO 가 적용되는 범위는 Realm 단위이다.

- **Client**
>인증, 권한 부여 행위를 대행하도록 맡길 어플리케이션을 나타내는 단위이다. 그 단위는 웹사이트 혹은 REST API 를 제공하는 서비스도 될 수 있다. 하나의 Realm 에 n개의 Client 를 생성, 관리할 수 있다.

- **User**
>Client 에 인증을 요청할 사용자를 나타낸다. 하나의 Realm 에는 Realm 에 종속된 n개의 User 를 생성하고 관리할 수 있다. 기본적으로 User 는 *Username, Email, FirstName, LastName* 으로 구성되어 있지만 Custom User Attribute 를 사용하면 사용자가 원하는 속성을 추가할 수 있다.

- **Role**
>User 에게 부여할 권한 내용을 나타낸다. 여기에는 Keycloak 의 REST API 를 사용할 권한을 부여할 수 있고 사용자가 정의한 권한을 부여할 수도 있다.

&nbsp;
## 2. Grafana
Grafana는 시계열데이터를 저장하고 보기 위한 오픈소스 모니터링 툴입니다.

&nbsp;
## 3. Grafana - Keycloak 연동

### 3.1 Grafana DB변경(SQLite → PostgreSQL)
Grafana는 기본적으로 sqlite3를 데이터베이스로 사용한다. SQLite는 “database is locked”오류가 자주 발생하므로 변경하는 것이 좋습니다.

>Grafana 참고 및 오류 해결 post 추가하여 해당 오류에 링크 연결

1. PostgreSQL에 grafana DB를 생성합니다.

```
# psql 접속
psql -U postgres -d postgres

# db생성
=========================================
CREATE USER grafana PASSWORD 'grafana' SUPERUSER;
CREATE DATABASE grafana;
ALTER DATABASE grafana OWNER TO grafana;
\password grafana
=========================================
```

2. Grafana.ini의 DB설정을 변경합니다.

- /etc/grafana/grafana.ini  - [database]

```
[database]

type = postgres
host = ip:port
name = grafana
user = grafana
password = grafana
```
&nbsp;
### 3.2 Grafana 설정 변경
Grafana는 인증을 위해 [OAuth](https://ko.wikipedia.org/wiki/OAuth)를 사용할 수 있으며 `grafana.ini`파일의 `auth.generic_oauth` 부분을 수정하여 연동할 수 있습니다.

아래의 내용과 같이 `grafana.ini` 의 설정을 변경합니다.

&nbsp;

---
- /etc/grafana/**grafana.ini**  - [server]

프록시 뒤에서 Grafana를 제공하는 경우 `/login/generic_oauth` 콜백 URL이 정확 하려면  `root_url` 옵션을 설정 해야 합니다.

```
// Edit setting
[server]
root_url = {grafana_url}
```

```
// default setting
[server]
;root_url = %(protocol)s://%(domain)s:%(http_port)s/
```

---

- /etc/grafana/**grafana.ini**  - [auth.generic_oauth]

```
// Edit setting
[auth.generic_oauth]
enabled = true
name = {sign in with {name}}
allow_sign_up = true
client_id = grafana
client_secret = {keycloak -> clients -> {생성한client} -> credentials -> Secret복사}
scopes = openid profile
auth_url = {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/auth
token_url = {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/token
api_url = {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/userinfo
tls_skip_verify_insecure = true
```

```
// default setting
[auth.generic_oauth]
;enabled = false
;name = OAuth
;allow_sign_up = true
;client_id = some_id
;client_secret = some_secret
;scopes = user:email,read:org
;auth_url = https://foo.bar/login/oauth/authorize
;token_url = https://foo.bar/login/oauth/access_token
;api_url = https://foo.bar/user**
;tls_skip_verify_insecure = false
```

- **enabled -** 사용 여부 flase/true
- **name** - “Sign in with {name}” 버튼에 들어갈 이름
- **allow_sign_up** - 사용자가 사용자 계정을 등록/생성할 수 없도록 설정
- **client_id -** {client name}
- **client_secret -** {keycloak -> clients -> {생성한client} -> credentials -> Secret복사}  **client 설정 시 Access Type을 ****“confidential”로 설정해야 확인 가능*
- **scopes -** 인증 범위
- **auth_url -** {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/auth
- **token_url -** {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/token
- **api_url -**  {keycloak URL}/auth/realms/{realm name}/protocol/openid-connect/userinfo
- **tls_skip_verify_insecure -** true인 경우 SSL/TLS는 서버에서 제공하는 모든 인증서와 해당 인증서의 호스트 이름을 수락

참고 링크 : [https://grafana.com/docs/grafana/latest/auth/generic-oauth/](https://grafana.com/docs/grafana/latest/auth/generic-oauth/)

---

- /etc/grafana/**grafana.ini**  - [auth]

```
// Edit setting
[auth]
signout_redirect_url = {Logout후 redirect 될 url}
```

```
// default setting
[auth]
;signout_redirect_url =
```

- **signout_redirect_url** - Logout후 redirect 될 url

---

- grafana.ini - 추가 설정(선택 사항)

```
[auth]
oauth_auto_login = true
disable_login_form = false
default_home_dashboard_path = {json 경로}

[security]
allow_embedding = true
```

- **oauth_auto_login** **-** True 변경 시 기존 로그인 화면을 뛰어넘고 keycloak 로그인 화면을 기본 화면으로 설정
- **disable_login_form** - True 변경 시 비밀번호 수정을 제한, oauth_auto_login = true와 함께 사용 하면 grfana 로그인 화면에서 keycloak id로만 로그인 가능
- **default_home_dashboard_path** - 지정한 경로에 있는 json 파일을 home dashboard로 지정(dashboard import와 다름)
- **allow_embedding -** 해당 설정을 true로 변경해야 dashboard embedding 가능

참고 링크: [https://grafana.com/docs/grafana/latest/administration/configuration/](https://grafana.com/docs/grafana/latest/administration/configuration/)
