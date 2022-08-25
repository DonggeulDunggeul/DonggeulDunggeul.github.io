---
title:  "[Java] 문자열(String)을 비교하는 방법 (==, equals, compare)"
excerpt: "[Java] 문자열(String)을 비교하는 방법 (==, equals, compare) "
author: 둥글

categories:
  - Java
tags:
  - [Java, String, equals]

toc: true
toc_sticky: true
 
date: 2022-08-25
last_modified_at: 2022-08-25
---

## 1. Java 에서는 == 가 아닌, equals를 사용하여 문자열이 동일한지 확인합니다.
- 다른 언어와는 달리 == 로 확인하지 않습니다.
- == 는 object 가 동일한지를 체크한다. 그렇기 때문에 해당 object가 갖고 있는 문자열이 동일하다는 것을 보장할 수 없기 떄문입니다.
- compare 매소드를 통해서도 문자열을 비교할 수 있습니다.

- equals() 는 모든 객체의 부모 클래스인 Object 에 정의되어있는 매소드입니다. 

```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```


- String 클래스는 다음과 같이 equals()를 오버라이드하여 인자로 전달된 String의 문자열을 비교하고 있습니다.
- 간단히 코드를 보면 == 키워드로 객체가 갖다면 더 확인하지 않고 true를 리턴합니다, 객체가 다른 경우 인자가 String 이라면 문자열을 비교하여 동일한지 결과를 리턴합니다.

## 2. equals() 를 사용하면서 NullPointerException 피하기
- equals() 를 사용하면서 주의할 점은 다음과 같이 비교할 변수가 null 이라면 NullPointerException이 발생합니다.
``` java
String first = null;
String object = "test";
logger.info("result : " + first.equals(object)); // NullPointException 
```

- 반대로 인자로 전달되는 객체(String object)는 null 이라도 NullPointerException 이 발생하지 않습니다.
```java
String first = "test";
String object = null;
logger.info("result : " + first.equals(object));
```

- 그리고 문자열을 비교할 때 대소문자도 비교합니다. 대소문자를 구분하지 않고 알파벳이 같은지만 보려면 equalsIgnoreCase()를 사용합니다.

## 3. compareTo()를 이용하여 문자열 비교
- compareTo() 는 두개의 문자열을 비교합니다. equals() 와 다른점은 어떤 문자가 사전 (문자시작) 적인 순서로 앞에 있는지도 리턴해 줍니다.
- 따라서 compareTo() 를 이용하면 리스트를 오름차순, 내림차순으로 정렬할 수 있습니다.

- 리턴 값은 0, 음수, 양수의 int가 리턴되며 의미는 다음과 같습니다.
  - 0 : 두개의 문자열이 동일
  - 양수 : compareTo() 를 호출하는 객체가 비교대상으로 들어오는 object 보다 사전적으로 순서가 앞설 때
  - 음수 : compareTo() 를 호출하는 객체가 비교대상으로 들어오는 object 보다 사전적으로 순서가 뒤일 떄

- String.class
```java
public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }
```

## Ref.
[Java - 문자열(String)을 비교하는 방법 (==, equals, compare)](https://codechacha.com/ko/java-string-compare/)
