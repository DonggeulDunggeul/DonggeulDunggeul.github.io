---
title:  "[Database] 우리는 어떤 Database를 선택하는가? "
excerpt: "[Database] 우리는 어떤 Database를 선택하는가?"
author: 둥글

categories:
  - Database
tags:
  - [MySQL, PostgreSQL, Database, RDBMS, TAT]

toc: true
toc_sticky: true
 
date: 2022-08-08
last_modified_at: 2022-08-08
---

# 우리는 어떤 DataBase를 선택하는가 ?
### > 들어가기에 앞서..
>[[Side Project] TAT(This and That)](https://donggeuldunggeul.github.io/posts/Side_Project_TAT-What_is_TAT/)
- 사이드 프로젝트를 진행하며 현재 설계 단계에 있다. Database를 정하고 있는데, 어떠한 DB가 우리에게 맞을까? 라는 의문을 가지고 조사하였고 해당 글을 기재하게 되었다.


## 1. 데이터베이스의 정의

### > 관계형 데이터베이스란?
- 현재 가장 많이 사용되고 있는 데이터베이스의 한 종류입니다.  
테이블(table)로 이루어져 있으며, 이 테이블은 키(key)와 값(value)의 관계를 나타냅니다.  
이처럼 데이터의 종속성을 관계(relationship)로 표현하는 것이 관계형 데이터베이스의 특징입니다.

### > 관계형 데이터베이스의 특징
- 관계형 데이터베이스의 특징은 다음과 같습니다.

1. 데이터의 분류, 정렬, 탐색 속도가 빠릅니다.
2. 오랫동안 사용한 만큼 신뢰성이 높고, 어떤 상황에서도 데이터의 무결성을 보장해 줍니다.
3. 기존에 작성된 스키마를 수정하기가 어렵습니다.
4. 데이터베이스의 부하를 분석하는 것이 어렵습니다.

>출처: [관계형 데이터베이스](http://www.tcpschool.com/mysql/mysql_intro_relationalDB)

### > 비관계형 데이터베이스(NoSQL) 이란?
1. 관계형 데이터베이스 이외의 형식으로 데이터를 저장하는 데이터베이스라는 뜻입니다.  
즉, 관계데이터를 저장할 수 있으며 관계형 데이터베이스와 방식은 다릅니다.
2. 스토리지 비용이 급격히 상승하면서 저장 및 쿼리를 위해 필요한 데이터 애플리케이션의 양도  
증가했습니다. 이러한 데이터는 정형, 반정형, 다형적 등 모양의 크기가 모두 다르기 때문에 미리 스키마를 정의하는 것이 거의 불가능해졌습니다. NoSQL 데이터베이스는 개발자가 엄청난 양의 비정형 데이터를 저장할 수 있도록 지원하여 뛰어난 유연성을 제공합니다.

>출처: [NoSQL이란 무엇입니까?](https://www.mongodb.com/ko-kr/nosql-explained)

## 2. TAT(This and That) 에는 어떤 Database 가 적합할까?
- 우리의 기획배경은 이러하다.
  - 개발을 하는데 도움을 주는 간단한 기능들을 한 곳에 모아서 사용한다면 어떨까? 라는 생각에서 기획된 서비스이다.

![캡처](https://user-images.githubusercontent.com/109357459/183293607-05fa90e8-1900-4f4a-a821-41683e15d451.jpg)

- 그림과 같이 우리의 주제는 사용자가 올린 게시글 및 원하는 기능에 따라 상황과 경우마다 연관된 데이터가 복합적으로 연결되어 작용하는 구조이다.
- 결국 우리는 훗 날에 어떻게 될 지 모르겠지만 우리의 프로젝트에는 관계형데이터베이스가 더 적합해 보이며, 거대한 데이터라고는 추측되지 않기에 NoSQL 은 배제하였다.
- 그렇다면 어떤 관계형 데이터베이스를 써야할까?

## 3. 어떤 RDS DB를 사용하는것이 적절할까?
- 많은 RDS 에는 MySQL, MariaDB, PostgreSQL 중에 고르는 것이 좋으며, 이유는 두 가지라고 한다.

1. 가격
2. Amazonn Aurora(오로라) 교체 용이성

- RDS의 가격은 라이센스 비용 영향을 받는다. 상용 DB인 MySQL이 오픈소스인 MariaDB, PostgreSQL 보다는 동일 사양 대비 가격이 더 높다. 프리티어 기간이 지나면 비용을 지불하면서 RDS를 사용해야하므로  미리 비용에 관련해 생각해 볼 필요가 있다.

- 두 번째로는 Aurora 교체 용이성이다. Amazon Aurora 는 AWS에서 MySQL과 PostgreSQL을 클라우드 기반에 맞게 재구성한 데이터베이스이다. 공식 자료에 의하면 RDS MySQL 대비 5배, PostgreSQL 보다 3배의 성능을 제공한다. 더군다나 AWS에서 직접 엔지니어링하고 있기 떄문에 계속 발전하고 있다.
이렇게 보면 Aurora가 좋아보이지만 최소 월 10만 이상이기 때문에 가격에서 부담스럽다.

>출처: [MySQL과 MariaDB 무엇이 더 좋을까?](https://loosie.tistory.com/366)

## 4. 우아한형제들 기술 블로그를 접하며.. PostgreSQL 로 정하다.

- 우리는 해당 고민에 대한 답을 우아한형제들 기술 블로그를 접하며 답을 내릴 수 있었다.

<h3>기능비교</h3>
<table>
<thead>
<tr>
<th></th>
<th><strong>Aurora MySQL(5.7)</strong></th>
<th><strong>Aurora PostgreSQL(11)</strong></th>
<th><strong>comment</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td><strong>DB특성</strong></td>
<td>RDBMS</td>
<td>ORDBMS</td>
<td>PostgreSQL은 객체관계형 DBMS로 개발자는 기존 데이터 type에서 확장된 type형태를 자유롭게 정의하여 사용할 수 있다. 또한 테이블 상속기능이 제공되어 자식 테이블은 부모 테이블로부터 열을 받아 사용할 수 있다.</td>
</tr>
<tr>
<td><strong>방식</strong></td>
<td>멀티쓰레드</td>
<td>멀티프로세스</td>
<td></td>
</tr>
<tr>
<td><strong>사용환경</strong></td>
<td>OLTP에 적절</td>
<td>OLTP, OLAP에 적절</td>
<td>단순 CRUD시에는 MySQL에 비해 PostgreSQL의 성능이 조금 떨어진다. <br /> PostgreSQL은 복잡한 쿼리를 요구하고 대규모 서비스인 경우에 특화되어 있다.</td>
</tr>
<tr>
<td><strong>MVCC지원</strong></td>
<td>Undo Segment 방식</td>
<td>MGA(Multi Generation Architecture) 방식</td>
<td>&#8211; Undo segment 방식: update 된 최신 데이터는 기존 데이터 블록의 레코드에 반영하고 변경 전 값을 undo 영역이라는 별도의 공간에 저장하여 갱신에 대한 버전관리를 하는 방식이다. <br /> &#8211; MGA 방식: 튜플을 update할 때 새로운 값으로 replace 처리하는 것이 아니라, 새로운 튜플을 추가하고 이전 튜플은 유효 범위를 마킹하여 처리하는 방식이다.</td>
</tr>
<tr>
<td><strong>UPDATE 방식</strong></td>
<td>UPDATE</td>
<td>INSERT &amp; DELETE (check)</td>
<td>PostgreSQL UPDATE시 내부적으로는 새 행이 INSERT되고 이전 데이터는 삭제 표시가 된다. 모든 인덱스에는 행의 실제 위치값에 대한 링크가 표기되어 있는데, 행이 업데이트되면 변경된 위치값에 대한 인덱스 정보도 업데이트가 필요하다. 이런 과정 때문에 UPDATE시에는 MySQL보다 성능이 떨어진다.</td>
</tr>
<tr>
<td><strong>지원되는 JOIN</strong></td>
<td>NL JOIN <br /> HASH JOIN (5.7 2.06 ~)</td>
<td>NL JOIN <br /> HASH JOIN <br /> SORT JOIN</td>
<td></td>
</tr>
<tr>
<td><strong>Parallel Query for SELECT</strong></td>
<td>지원됨 (5.7 2.09.2~)</td>
<td>지원됨 (9.6 ~)</td>
<td></td>
</tr>
<tr>
<td><strong>Default Transaction Isolation</strong></td>
<td>REPEATABLE READ</td>
<td>READ COMMITTED</td>
<td></td>
</tr>
<tr>
<td><strong>테이블 기본 구성 인덱스</strong></td>
<td>CLUSTERD INDEX</td>
<td>NON-CLUSTERED INDEX</td>
<td></td>
</tr>
</tbody>
</table>

- "성능 개선의 key 가 될 수 있는 두 항목이 보입니다. 바로 'Join'과 'Parallel query' 기능입니다. MySQL 8.0 에서 정식 지원되는 hash join, parallel query 기능이 저희가 사용하는 Aurora MySQL 5.7 버전에서 일부(또는 조금 다른 방식으로) 지원 되지만, 그마저도 지원불가 조건에 포함되어 활용이 쉽지 않았다고 합니다. 반면 PostgreSQL은 오래전부터(9버전~) 대부분의 select 쿼리에서 parallel 기능이 지원되고 있고 다양한 join 방식을 지원하기 때문에 개선이 필요한 정산 쿼리의 성능적인 이점을 기대해 볼 수 있을 것 같습니다. " 라는 내용을 보았을 때 물론 우리의 프로젝트가 MariaDB 및 MySQL에서도 충분히
해결해 나갈 수 있을거라고 보이지만, 좀 더 큰 이상향과 목적을 생각했을 때에는 postgreSQL이 이점이 커 보인다.

- 더 나아가서 1000만 건 데이터의 조인쿼리를 HASH JOIN 으로 비교 실행한 결과를 보았을 때,
Aurora MySQL 에서는 22초 정도 소요되며, Aurora PostgreSQL 에서는 3초로 7배 이상 빠른 속도를 보여주었다고 한다. 따라서 개발해가며 공유데이터를 연관짓고 쿼리를 만들어가야 할 필요가 보이는 우리 프로젝트에서는 PostgreSQL 이 적합해 보인다. ( 물론, 저 정도의 데이터를 다룰 정도에 개발 인프라를 만들지는 아직 미지수이다. )

>출처: [Aurora MySQL vs Aurora PostgreSQL](https://techblog.woowahan.com/6550/)