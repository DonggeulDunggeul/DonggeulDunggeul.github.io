---
title:  "[Java] Java입문-Switch, While, do While, for"
excerpt: " Java입문-Switch, While, do While, for "
author: 동글

categories:
  - Java
tags:
  - [Java]

toc: true
toc_sticky: true
 
date: 2022-08-15
last_modified_at: 2022-08-15
---

> 강의 링크 : [자바 입문](https://programmers.co.kr/learn/courses/5)

## JAVA 입문 - Switch, While, do While, for

**Switch** - 어떤 변수의 값에 따라서 실행

- SwitchExam.java

```java
package javaStudy;

public class SwitchExam {

	public static void main(String[] args) {
		// switch, case, default, break
		
		int value = 1;
		
		// case는 맞는 break 없으면 case 부터 실행, break 넣으면 그 문장만 실행
		switch(value) { // JDK7이전은 정수만 가능, JDK7 이후부터는 정수, 문자열 가능
			case 1: // 변수 value가 1 이라면 먼저 실행
				System.out.println("1");
				break;
			case 2:
				System.out.println("2");
				break;
			case 3:
				System.out.println("3");
				break;
			default : // 1,2,3이 아닌 다른 숫자 일 경우 실행
				System.out.println("그 외 다른 숫자");
		}
		
		String str = "A";
		switch(str) {
		case "A":
			System.out.println("A");
			break;
		case "B":
			System.out.println("B");
			break;
		}
	}

}
```

**반복문** - 어떠한 것을 반복적으로 사용하고 싶을 때, 상황에 ?따라 continue, break 사용 가능

- while
- do while
- for

**while**

- WhileExam.java

```java
package javaStudy;

public class WhileExam {

	public static void main(String[] args) {
		int i = 0;
		
		// 0 ~ 9 까지 출력
		while(i<10) { // 조건이 그만 만족할 때 까지(i가 10보다 커질 때 까지) 반복
			System.out.println(i);
			i++; // i = i + 1;
		}
		
		// 1~100까지의 합
		int total = 0;
		int j = 1;
		while(j<=100) {
			total = total + j;
			j++; // j = j+1
		}
		System.out.println(total);
		
		// 아래와 같은 무한루프는 보통 조건문 추가해서 실행, 중지를 한다.
		while(true) {
			System.out.println("hello");
		}
		
	}

}
```

**do while**

- while문의 경우 조건이 만족하지 않으면 실행되지 않지만, do while문은 무조건 한번은 수행된다.

- DoWhileExam.java

```java
package javaStudy;

import java.util.Scanner;

public class DoWhileExam {

	public static void main(String[] args) {
		int value = 0;
		Scanner scan = new Scanner(System.in); // 키보드로부터 입력받을 거애요!
		
		do { // {}안에 있는 문장들이 실제로 반복할 문장들, 무조건 한번은 실행
			value = scan.nextInt(); // 입력한 값을 value에 넣을거에요!
			System.out.println("입력받은 값: " + value);
		}while(value != 10); // while의 조건이 만족하지 않을 때 까지(입력받은 값이 10일때까지) do부터 반복 
			
		System.out.println("반복문 종료!");
	}
}
```

**for**

- for 구문 자체에 변수 초기화, 조건식, 증감식이 한줄로 표현된다.

- ForExam.java

```java
package javaStudy;

public class ForExam {

	public static void main(String[] args) {
		int total = 0;
		
		// 1~100의 합
		for(int i = 1; i <= 100; i++) { // ;으로 구분 변수초기화, 조건식, 증감식
			// 조건이 만족하면 수행
			total = total + i;
		}
		System.out.println(total);
		
		// 1~100의 짝수합
		int totalEven = 0;
		for(int j = 1; j <= 100; j++) {
			if(j % 2 != 0) {
				continue; // 만약 j가 아니면 total에 j를 더하지 말고 j를 더한 후 다음 반복
			}
			totalEven = totalEven + 1;
		}
		
		int totalFif = 0;
		for(int a = 0; a <= 100; a++) {
			totalFif = totalFif +1;
			if (a == 50) {
				break; // a가 50이되면 반복문을 아예 빠져나와 50까지의 합만 출력
			}
		}
		
		System.out.println(total);
		System.out.println(totalEven);
		System.out.println(totalFif);
	}

}
```