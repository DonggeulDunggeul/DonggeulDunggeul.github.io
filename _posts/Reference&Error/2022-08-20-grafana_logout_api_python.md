---
title:  "[Reference & Error] Grafana logout API(Python)"
excerpt: "Grafana logout API(Python)"
author: 동글

categories:
  - Reference & Error
tags:
  - [Monitoring]

toc: true
toc_sticky: true
 
date: 2022-08-20
last_modified_at: 2022-08-20
---

>참고 Post : [Keycloak backchannel logout](https://donggeuldunggeul.github.io/posts/Keycloak_backchannel_logout/)

### 1. Reference  

**Grafana 로그아웃**

- admin계정 ID:PW base64 Encode

```
#admin:admin - default
YWRtaW46YWRtaW4=
```

1. **Grafana사용자 목록 API를 이용하여 원하는 사용자의 번호를 얻는다**

참고 링크: [https://grafana.com/docs/grafana/latest/developers/http_api/user/#search-users](https://grafana.com/docs/grafana/latest/developers/http_api/user/#search-users)

```python

url = "{gf_url}/api/users"

headers = {'Content-Type': 'application/json; charset=utf-8', 
           'Accept':'application/json', 
           'Authorization': 'Basic {admin계정 ID:PW base64 Encode}'
}

requests.request("GET", url, headers=headers).json()
# ==> response = Grafana 사용자 목록

```

1. **사용자 로그아웃**

참고 링크: [https://grafana.com/docs/grafana/latest/developers/http_api/admin/#logout-user](https://grafana.com/docs/grafana/latest/developers/http_api/admin/#logout-user)

```python

url = "{gf_url}/admin/users/{사용자 번호}/logout"

headers = {'Content-Type': 'application/json; charset=utf-8', 
           'Accept':'application/json', 
           'Authorization': 'Basic {admin계정 ID:PW base64 Encode}'
}

requests.request("POST", url, headers=headers).json()
# ==> response = {'message': 'User logged out'}

```