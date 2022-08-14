---
title:  "[Reference & Error] Keycloak_backchannel_logout"
excerpt: "Keycloak_backchannel_logout"
author: 동글

categories:
  - Reference & Error
tags:
  - [Keycloak]

toc: true
toc_sticky: true
 
date: 2022-08-14
last_modified_at: 2022-08-14
---

### 1. Reference  

### Back channel Logout

참고 링크: [OpenID Connect Backchannel Logout](https://ashend.medium.com/openid-connect-backchannel-logout-144a3198d2a)

1. RP 또는 OP가 로그아웃 작업을 트리거하면 OP는 OP에서 동일한 사용자의 세션을 공유하는 모든 RP를 찾습니다.
2. 그런 다음 OP는 각 RP에 대한 관련 클레임을 포함하는 로그아웃 토큰이라는 특수 JWT 토큰을 생성하고 모든 RP의 로그아웃 끝점에 로그아웃 토큰과 함께 로그아웃 요청을 보냅니다.
3. 로그아웃 토큰을 받으면 RP는 토큰을 많이 검증하고 특정 사용자의 세션을 무효화합니다.

> **RP([Relying Party](https://en.wikipedia.org/wiki/Relying_party))**
> 

> **OP([OpenID](https://ko.wikipedia.org/wiki/%EC%98%A4%ED%94%88%EC%95%84%EC%9D%B4%EB%94%94) Provider)**

&nbsp;  

### Keycloak Back channel Logout

참고 링크 : [Keycloak Document - admin-url-configuration](https://www.keycloak.org/docs/latest/securing_apps/#admin-url-configuration)

![keycloak](https://user-images.githubusercontent.com/109357459/184535302-d2914cc5-7bb4-4c5b-8915-55eaf4423fe2.jpg)

- Keycloak Logout Token

```
[('logout_token', 'JWT token')])]
```

- Keycloak Logout Token - decode

```
header
{
"alg": "RS256",
"typ": "JWT",
"kid": ""
}
```

```
 payload

{
"iat": ,
"jti": "",
"iss": "{keycloakURL}/auth/realms/{Realm Name}",
"aud": "{Client Name}",
"sub": "{사용자 uid}",
"typ": "Logout",
"sid": "",
"events": {
"http://schemas.openid.net/event/backchannel-logout": {},
"revoke_offline_access": true
}
}
```

> `{keycloakURL}/auth/realms/{REALM}/protocol/openid-connect/logout`   
해당 URL은 KEYCLOAK의 로그아웃 
end-point이므로 로그아웃 시 해당 URL을 거쳐야 로그아웃 토큰을 생성합니다.  

> keycloak UI Client 설정에서 `backchannel Logout URL` 을 입력할 수 있습니다. 해당 URL에 커스텀 API주소를 입력하면 로그아웃 시 해당 API를 호출합니다.
>
> python flask로 api를 구성했을 경우 `request.values` 를 이용하여 logout token을 받아올 수 있습니다.
>

참고: [keycloak-documentation Logout](https://github.com/keycloak/keycloak-documentation/blob/main/securing_apps/topics/oidc/java/logout.adoc)
>