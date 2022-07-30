---
title:  "[Spring] Mybatis: ResultMap + LinkedHashMap"
excerpt: "[Spring] Mybatis: ResultMap + LinkedHashMap"
author: 둥글

categories:
  - Spring
tags:
  - [Spring, Mybatis]

toc: true
toc_sticky: true
 
date: 2022-07-30
last_modified_at: 2022-07-30
---

### HashMap ?
- Java Collections Framework 에 속한 구현체 클래스이면서, Map 인터페이스를 구현한 함수이다.
따라서 데이터의 저장은 key, value 형태가 된다. key 값의 hash Code를 index로 Array 에 값을 저장한다.
따라서 검색 속도는 매우 빠르다.
그리고 해싱(Hashing) 검색을 사용하기 때문에 대용량 데이터 관리에도 좋은 성능을 보여주고 있다.
key 값은 중복이 되지 않고, value 값은 허용이 된다.

하지만 해시 값은 순서를 보장 하지 않는다. 예를 들어 a, b 라는 'key' 값이 있을 때
a => index 0, b => index 1 이렇게 순서대로 저장하지 않는다.

더 자세히 보면 HashMap.keySet() 을 통하여 Set 을 꺼내게 되는데, 이 반환되는 Set 동작에서
HashMap 의 데이터 입력의 순서가 정확히 보장되지 않는다.

그렇기 때문에 나온 것이 LinkedHashMap 이다.

* 출처: https://hgserver.tistory.com/54
---
## LinkedHashMap ?
- 즉, Hash 를 통해 자료를 보관하지만 자료를 순서대로 가져와야 하는 경우가 존재한다. => LinkedHashMap
- 따라서 LinkedHashMap 은 Doubly-Linked List를 내부에 유지함으로써 입력된 자료의 순서를 보관한다.
- 물론 자료의 크기가 커지면 메모리 사용량이 늘어나지만, 간편하다.

* 출처: https://medium.com/@igniter.yoo/java-linkedhashmap-%EC%88%9C%EC%84%9C%EB%A5%BC-%EC%9C%A0%EC%A7%80%ED%95%98%EB%8A%94-%ED%95%B4%EC%8B%9C%EB%A7%B5-11a7846d8893

---

## MyBatis - ResultMap
- 복잡한 결과 매핑을 간편하게 해주기위해 만들어진 태그

---
## [실무] ResultMap + LinkedHashMap 을 이용하여 엑셀 다운로드를 위한 데이터 를 만들다.

- EX1) "우리는 데이터 결과를 엑셀데이터로 만들어 반환해줄 작업이 필요하다" 는 경우에
Mapper.xml 에서 

```
resultMap = animalMap
    SELECT
        TIER, NAME, GENDER
    FROM Member
 
    <resultMap id="animalMap" type="java.util.HashMap">
        <result column="TIER" property="등급" jdbcType="">
        <result column="NAME" property="이름" jdbcType="">
        <result column="GENDER" property="성별" jdbcType="">
    </resultMap>
```
라는 예시코드가 있다고 가정하고 실행후 결과를 본다면

```
    result = {이름, 등급, 성별} 
```
이렇게 순서를 지키지 않고 들어가있는 데이터를 볼 수 있다. 하지만 엑셀에 데이터가 들어가는 순서는
{등급, 이름, 성별} 순으로 들어가야 한다고 계획할 때에는 오류를 범하게 된다.

따라서 LinkedHashMap이 이러한 경우에 필요하며, 타입을 수정하면

```
resultMap = animalMap
    SELECT
        TIER, NAME, GENDER
    FROM Member

    <resultMap id="animalMap" type="java.util.LinkedHashMap">
        <result column="TIER" property="등급" jdbcType="">
        <result column="NAME" property="이름" jdbcType="">
        <result column="GENDER" property="성별" jdbcType="">
    </resultMap>
```

내가 의도한 결과 데이터처럼 엑셀에는
```
    result = {등급, 이름, 성별}
```
순으로 들어가는 것을 확인할 수 있다.




