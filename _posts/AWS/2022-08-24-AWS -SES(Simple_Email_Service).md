---
title:  "[AWS] Amazon SES - 2022년 바뀐 UI위에서"
excerpt: "[AWS] Amazon SES - 2022년 바뀐 UI위에서"
author: 둥글

categories:
  - AWS
tags:
  - [AWS, SES, Domain, Email]

toc: true
toc_sticky: true
 
date: 2022-08-24
last_modified_at: 2022-08-24
---

## Amazon SES - 2022년 바뀐 UI위에서

- ## SES란?
  - Simple_Email_Service 로서 메일을 자동으로 보내주는 AWS 서비스이다.
  - API 를 통해 Email 을 자동으로 보낼 때 편리하듯 하다.

- ##  구축하면서 부딪힌 점
  - SES를 설정할 일이 생겨 구축 중에 서칭 및 인수인계 자료를 통해 마주치다 많이 바뀐 환경이라 당황스러웠다.   
  막상 다른 UI 를 마주치니, 구성되는 요소들을 이해하여 어떻게 적용해야 하는지 공부하는 시간을 가졌어야 했다.
  - 결국  SES 는 메일을 전송해주는 서비스인데, 필요한점은 Domain 과 email 주소 ( ID@Domain ) 이며,
  Domain 과 ID 두개 다 생성을 해야하며, 특히 Domain 은 자신이 DNS 호스트를 공급하는 방식에 따라 CNAME 값을 추가하여 AWS 에서 자동으로 72시간 내에 인증하여 사용할 수 있게 허가를 받아야 한다.

- 예전 SES UI 와 현재 SES UI 비교
  - 몇년도 부터 Amazon SES UI 가 변경된지는 모르겠지만, 내가 작업하는 22-08-23 날짜로 봤을 때는  
  용어가 많이 바뀌고 서비스를 이용하는 탭 정보도 바뀐 것 같아 보인다. 우선 예전 자료들을 보면은  
  보통 Domain 및 email 을 바로 등록하면서 진행하는데, 현재는 Verified identities 에서 인증을 통해 진행하는 듯하다. ( 아마 존재하지 않는 Domain 주소를 가지고 Fishing mail 을 예방하기 위해
  해당 로직으로 바뀐 ) 듯 해 보인다.  
  즉, Domain 값 (www.naver.com) 처럼 존재하는 도메인 으로 들어갔을 때 존재하는지 확인을 하고,
  email 은 해당 도메인값이 들어가있는 email 로 보내어 인증을 하는 방식이다.
  - ex) naver.com 도메인을 등록하고 인증이 되면 test@naver.com 메일을 등록하여 인증메일을 받아서 SES 서비스를 사용할 수 있다.

- => [구버전 SES UI방식](https://brunch.co.kr/@topasvga/567)

- ## 작업 순서
  - 나의 환경은 SES - Amazon Route 53 로 구성되어 있다.
  - AWS 접속 후 SES 를 검색하여 Amazon SES로 들어간 뒤 좌측메뉴바의 Configuration => Verified identities로 들어간다.
  
![image](https://user-images.githubusercontent.com/103028054/186319867-225ded6e-ab82-4e66-8bca-11b809d6cfbc.png)
- 주황색 버튼 Create identity 를 클릭


![image](https://user-images.githubusercontent.com/103028054/186320771-57c74c5b-9bc1-49ed-997b-f24df0d2abe4.png)

- Domain을 누른 후 지정할 도메인명 (ex: google.com ) 처럼 www는 빼고 넣는다.
- 그 후 각자에 맞는 설정을 한 뒤 Verifying your domain 을 진행한다.

![image](https://user-images.githubusercontent.com/103028054/186322649-a61a47e8-aa1a-4e1f-b3b6-bdbf64af891d.png)

- 나의 경우에는 Easy DKIM 으로 진행하였으며 해당 설정을 해주어야 CNAME 값이 생성되고,
해당 값으로 인증하기 위해 이 설정을 하는듯 하다.
- DKIM signatures 는 Enabled 하는 것을 추천한다. ( 시지가 전송 중에 위조되거나 변경되지 않았음을 확인하는 데 도움이 된다고 한다. )

![image](https://user-images.githubusercontent.com/103028054/186323190-aa9c9206-ca4f-4792-87d7-2ea65771938f.png)

- 그 후 생성된 Domain 을 들어가보면 CNAME 값이 생성되었음을 확인할 수 있다. 해당값으로 인증을 진행하는 듯 하다.
- 이 후에 Identity status 가 Verified 로 변경되면 이용이 가능하다. ( 최대 72시간 내에 인증이 완료된다고 한다.)
- 도메인 호스트를 Amazon Route 53 로 사용중이라면 (Domain 명이 있을 떄에) DKIM 셋팅이 자동으로  
도메인 호스트에 추가된다.
- Route 53 를 사용중이지 않다면 해당 CNAME 의 Name과 key 값을 Domain host 에 따로 설정해주는 작업이 필요하다.

## 정리 및 참고
- 1. 변경되기 전 SES 에서는 TXT 레코드를 가지고 사용자가 ID 소유자임을 확인했었는데,  
도메인의 DNS 설정(Route 53 또는 DNS HOST)에서 DKIM 을 구성하면 소유자임을 확인하여 필요없다고 합니다.
=> 시간이 부족하다면 TXT 레코드를 등록하여 72시간내에 시간을 절약할 수 있다.

- 2. 정리하자면 SES 를 사용방법이 등록 개념 보단 사용할 Id 값 ( domain 또는 email ) 을 등록하여,
DKIM 및 TXT 레코드를 가지고 인증절차를 통해 확인받아서 사용하는 개념으로 보입니다. 이러한 뒷 배경을 이해하기 위해서는 DNS 설정에 대한 개념이 필요해 보입니다.

## Ref
- https://docs.aws.amazon.com/ko_kr/ses/latest/dg/creating-identities.html#just-verify-domain-proc