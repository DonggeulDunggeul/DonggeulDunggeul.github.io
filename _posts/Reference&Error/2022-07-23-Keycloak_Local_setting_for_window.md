---
title:  "[Reference & Error] Keycloak local setting(Window)"
excerpt: "Keycloak local setting(Window)"
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
[Grafana Keycloak 연동](https://donggeuldunggeul.github.io/posts/Grafana_keycloak_setting/)

### 1. Reference  

&nbsp;  

**1.1 Docker desktop 설치**  
> [Docker Desktop 설치 및 파이썬 스크립트 이미지 생성](https://donggeuldunggeul.github.io/posts/Docker_Desktop_install_for_Window/)

&nbsp;  

**1.2 Keylcoak Image Pull**  
> [https://hub.docker.com/r/bitnami/keycloak/](https://hub.docker.com/r/bitnami/keycloak/)  

```
docker pull bitnami/keycloak
```


&nbsp;  

**1.3 Docker compose yaml 실행**  
> [참고](https://github.com/bitnami/bitnami-docker-keycloak)  

- Docker-compose.yaml  

```yaml
version: '2'
services:
  postgresql:
    image: docker.io/bitnami/postgresql:11
    environment:
      # ALLOW_EMPTY_PASSWORD is recommended only for development.
      - ALLOW_EMPTY_PASSWORD=yes
      - POSTGRESQL_USERNAME=bn_keycloak
      - POSTGRESQL_DATABASE=bitnami_keycloak
    volumes:
      - 'postgresql_data:/bitnami/postgresql'

  keycloak:
    image: docker.io/bitnami/keycloak:18
    depends_on:
      - postgresql
    ports:
      - "{port}:8080"

volumes:
  postgresql_data:
    driver: local
```  

&nbsp;  

**1.4. keycloak 접속**  
- **URL**: localhost:{port}
- **ID**: user
- **PW**: bitnami



