---
title:  "[Reference & Error] Keycloak API, helm install, custom"
excerpt: "Keycloak API, helm install, custom"
author: 동글

categories:
  - Reference & Error
tags:
  - [Keycloak]

toc: true
toc_sticky: true
 
date: 2022-07-23
last_modified_at: 2022-07-23
---

> 관련 Post : 
[Keycloak local setting(Window)](https://donggeuldunggeul.github.io/posts/Keycloak_Local_setting_for_window/)

### 1. Reference  

&nbsp;  

**1.1 Keycloak API For Python**  

>[Keycloak API - Postman](https://documenter.getpostman.com/view/7294517/SzmfZHnd)  
>[Keycloak API - doc](https://www.keycloak.org/docs-api/15.0/rest-api/)

```python
import requests
import json
```

- **Keycloak admin token 발행**  
_(Keycloak API를 호출하기 위해서는 Keycloak admin 계정의 Token이 필요합니다.)_  

```python
def create_token():
    url = "{}/auth/realms/{}/protocol/openid-connect/token".format(keycloak_url, realm)

    payload='grant_type=password&username={}&password={}&client_id=admin-cli'.format(keycloak_admin, keycloak_password)
    headers = {
    'Content-Type': 'application/x-www-form-urlencoded'
    }

    response = requests.request("POST", url, headers=headers, data=payload)
    token=json.loads(response.text)["access_token"]

    return token
```

- **realm 생성**  

```python
def create_realm(token):
    url = "{}/auth/admin/realms".format(keycloak_url, realm)

    auth = token
    payload = json.dumps({
    "realm": "{}".format(realm),
    "enabled": "true",
    "displayNameHtml": '<div class="kc-logo-text"><span>Keycloak</span></div>',
    "defaultRoles": [ "offline_access" , "uma_authorization", "default-roles-{}".format(realm) ],
    "userManagedAccessAllowed": "true",
    "registrationAllowed": "true",
    "resetPasswordAllowed": "true",
    "editUsernameAllowed": "false",
    "loginWithEmailAllowed": "true",
    "users": [{
                "username": "{}".format(keycloak_admin),
                "enabled": "true",
                "email": "{}@email.com".format(keycloak_admin),
                "credentials": [{
                    "type": "password",
                    "value": "{}".format(keycloak_password)
                }],
                "realmRoles": ["offline_access", "uma_authorization", "default-roles-{}".format(realm) ],
                "clientRoles": {
                    "realm-management": ["realm-admin", "create-client", "manage-clients", "manage-realm", "view-clients", "view-realm", "view-users", "manage-users"]
                },
            }]
    })
    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json'
    }

    response = requests.request("POST", url, headers=headers, data=payload)

    return response.text
```  

- **realm 삭제**

```python
def delete_realm(token):
    """
    realm 생성
    """
    url = "{}/auth/admin/realms/{}".format(keycloak_url, realm)

    auth = token

    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json'
    }

    response = requests.request("delete", url, headers=headers)

    return response.text
```  

- **client 생성**

```python
def create_sso_client(token):
    url = "{}/auth/realms/{}/clients-registrations/default".format(keycloak_url, realm)
    auth = token

    payload = json.dumps({
    "clientId": "sso",
    "rootUrl": "{url}".format(application_URL),
    "adminUrl": "{url}".format(application_URL),
    "redirectUris": [ "{url}".format(application_URL) ],
    "webOrigins": [ "{url}".format(application_URL) ],
    "implicitFlowEnabled": "true",
    "bearerOnly": "false",
    "directAccessGrantsEnabled": "true",
    "serviceAccountsEnabled": "true",
    "attributes": {
        "backchannel.logout.session.required": "true"
        }
    })
    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json'
    }

    response = requests.request("POST", url, headers=headers, data=payload).json()

    return response
```  

- **get client secret**

```python
def generate_secret(token):
    url = "{}/auth/admin/realms/{}/clients/{}/client-secret".format(keycloak_url, realm, client_id)
    auth = token

    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json'
    }

    response = requests.request("POST", url, headers=headers).json()
    secret = response.get('value')

    return secret
```

- **user 생성, 정보 업데이트**

```python
def add_user_keycloak(token):
    url = "{}/auth/admin/realms/{}/users".format(keycloak_url, realm)

    auth = token
    payload = json.dumps({
        "username": "{}".format(user_id),
        "firstName": "{}".format(name),
        "email": "{}".format(email),
        "enabled": "{}".format(keycloak_user_enable),
        "credentials":[{
            "type": "password",
            "value": "{}".format(password),
            "temporary": "false"
        }],
    })
    
    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json'
    }
    
    """user 생성"""
    response = requests.request("POST", url, headers=headers, data=payload)

    """정보 업데이트"""
    response = requests.request("PUT", url, headers=headers, data=payload)
```  

- **user 삭제**  

```python
def delete_user_keycloak(token):
    url = "{}/auth/admin/realms/{}/users/{}".format(keycloak_url, realm, user_uid)
    auth = token

    headers = {
    'Authorization': 'bearer {}'.format(auth)
    }

    response = requests.request("DELETE", url, headers=headers)

    return response
```

- **get user uid**  

```python
"""keycloak 전체 사용자 정보 리스트"""
def keycloak_users_info(token):
    url = "{}/auth/admin/realms/{}/users".format(keycloak_url, realm)
    auth = token

    headers = {
    'Authorization': 'bearer {}'.format(auth)
    }

    response = requests.request("GET", url, headers=headers).json()

    return response
    
def get_keycloak_user_uid(token):
    user_list = keycloak_users_info(token)
    for user in user_list:
        id = user.get('username')
        email = user.get('email')
        
        if id == user_id and email == user_origin_eamil:
            response = user.get('id')
            
    return response
```

- **role mapping (client-role)**  

```python
"""get client uid"""
def get_client_uid(token):
    url = "{}/auth/admin/realms/{}/clients?clientId=realm-management".format(keycloak_url, realm)
        auth = token

        headers = {
        'Authorization': 'bearer {}'.format(auth)
        }

        client_info = requests.request("GET", url, headers=headers).json()
        
        for client_uid in client_info:
            response = client_uid.get('id')
        
        return response

"""get role info"""
def get_role_info(token):
    url = "{}/auth/admin/realms/{}/clients/{}/roles?".format(keycloak_url, realm, client_uid)
    auth = token
    headers = {
    'Authorization': 'bearer {}'.format(auth)
    }

    role_list = requests.request("GET", url, headers=headers).json()
    
    for role_info in role_list:
        role_name = role_info.get('name')
        if role_name == "{}".format(role_name):
            response = role_info
            
    return response

"""role mapping"""
def update_keyclaok_user_role_mapping(token, user_uid, user_admin_yn):
    client_uid = get_client_uid(token)
    role_info = get_role_info(token, client_uid)

    url = "{}/auth/admin/realms/{}/users/{}/role-mappings/clients/{}".format(kc_url, kc_realm, user_uid, client_uid)
    
    auth = token
    
    list = []
    list.append(role_info)
    
    payload = json.dumps(list)
    
    headers = {
    'Authorization': 'bearer {}'.format(auth),
    'Content-Type': 'application/json',
    }
    
    """role mapping"""
    response = requests.request("POST", url, headers=headers, data=payload)
        
    """role mapping 삭제"""
    response = requests.request("DELETE", url, headers=headers, data=payload)
    
    return response
```  


&nbsp;  

**1.2 Keylock helm 설치**
- **1.2.1** keycloak helm install

```
## 설치(Helm)
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ git clone https://github.com/bitnami/charts.git
$ cd charts/bitnami/keycloak
$ vi values.yaml
auth:
  ## @param auth.createAdminUser Create administrator user on boot
  ##
  createAdminUser: true
  ## @param auth.adminUser Keycloak administrator user
  ##
  adminUser: admin
  ## @param auth.adminPassword Keycloak administrator password for the new user
  ##
  adminPassword: "password"
$ kubectl create namespace keycloak
$ helm install keycloak bitnami/keycloak -f values.yaml --namespace keycloak

## uninstall
$ helm uninstall keycloak --namespace keycloak
```

- **1.2.2** values.yaml - docker image 변경

```
## @section Keycloak parameters
image:
  ##registry: docker.io
  ##repository: bitnami/keycloak
  ##tag: 16.1.1-debian-10-r60
  registry: {registry}
  repository: {repository}
  tag: {tag}
```

- **1.2.3** values.yaml - port 변경 
```
## @section Exposure parameters
service:
  nodePorts:
    http: "{port}"
    https: "{port}"
```

- **1.2.4** values.yaml - db 변경 

```
postgresql:
  enabled: false
  auth:
    username: bn_keycloak
    password: ""
    database: bitnami_keycloak
    existingSecret: ""
  architecture: standalone

externalDatabase:
  host: "{host}"
  port: {port}
  user: keycloak
  database: keycloak
  password: "{password}"
  existingSecret: ""
  existingSecretPasswordKey: ""
```  

>(참고) postgreSQL db 생성

```
# psql 접속
psql -U postgres -d postgres

# db생성
CREATE USER keycloak PASSWORD 'password' SUPERUSER;
CREATE DATABASE keycloak;
ALTER DATABASE keycloak OWNER TO keycloak;
\password keycloak
```

&nbsp;  

**1.3 Keylock Page Custom**
> 로그인 페이지의 이미지와 html을 변경
- **1.3.1** docker build

```dockerfile
FROM bitnami/keycloak:16.1.1-debian-10-r60

COPY keycloak-logo-text.png /opt/bitnami/keycloak/themes/keycloak/login/resources/img/keycloak-logo-text.png
COPY keycloak-bg.png /opt/bitnami/keycloak/themes/keycloak/login/resources/img/keycloak-bg.png
COPY login.ftl /opt/bitnami/keycloak/themes/base/login/login.ftl
```

```
docker build -t {name}:{tag} -f Dockerfile .
```

_참고) build가 아닌 cp로 파일을 변경하면 helm install 시 docker entrypoint 때문에 Log도 없이 오류 발생_

