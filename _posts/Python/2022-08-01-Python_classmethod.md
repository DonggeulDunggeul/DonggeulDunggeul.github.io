---
title:  "[Python] classmethod. instanceMethod 메모리값 차이"
excerpt: "[Python] classmethod. instanceMethod 메모리값 차이"
author: 둥글

categories:
  - Python
tags:
  - [classmethod, Python]

toc: true
toc_sticky: true
 
date: 2022-08-01
last_modified_at: 2022-08-01
---

파이썬 @classmethod, @staticmethod, instancemethod 에 대한 이해는
해당 블로그의 글을 추천드립니다.  
>출처: [@classmethod, @staticmethod, instancemethod](https://dodonam.tistory.com/122)

- @classmethod 로 정의한 매서드는 클래스를 인스턴스화 하여, 매서드를 호출 시
@classmethod 의 변수값을 최우선으로 고정 시켜 작동하는 것 같음을 확인하여 공유해 봅니다.

# class method 일 때

```python
class Test:
    a = 0

    @classmethod
    def test(cls):
        cls.a = 1

t1 = Test()
print(t1.a)
t2 = Test()
print(t2.a)
print(Test.a)

t1.test()
print(t1.a)
print(t2.a)
print(Test.a)
```

# 결과
```python
0
0
0
1
1
1
```

- 마지막 print(t2.a) 의 값도 1 로 출력이 된다. 여기서 유추해볼 수 있듯이, t1, t2는 같은 클래스를 공유하고 있으며,

객체이며 상속관계에 속해있다. 따라서 t1.test() 로 인스턴스화 시킨 후 classmethod 로 정의된 
test 매서드를 실행할 때에

Test 클래스에서 가리키는 a 의 메모리값이 1로 고정되도록 변경되기 때문에 t2.a 의 값도 1 을 출력하게 되는 것으로 보여집니다.

정리하자면 python 에서 @classmethod 는 호출 시 그 클래스내에서의 python 문법 상 일반적으로 많이 쓰이는 cls(=self=Test) 로 받게되는 매개변수(본문에서 변수 a) 를 메모리에서 고정값으로 바꿔버리는 역할을 하는 듯 하다.

# instance method 일 때

```python
class Test:
    a = 0

    def test(self):
        self.a = 1

t1 = Test()
print(t1.a)
t2 = Test()
print(t2.a)
print(Test.a)

t1.test()
print(t1.a)
print(t2.a)
print(Test.a)
```

# 결과2
```python
0
0
0
1
0
0
```

- print(t2.a) 의 값은 0으로서 classmethod 와 달리 변하지 않는다. 

- t2.test()를 실행해줘야 바꿀 수 있다.

# 정리하자면..
- @classmethod 로 정의된 매서드를 실행할 때에는 클래스내부에 있는 변수 값의 메모리값 자체가 고정적으로
변하기 때문에 다른 변수로 클래스를 인스턴스화 하더라도 같이 고정된 값을 바라보게 됩니다.
즉, @classmethod 는 클래스 내부에서 클래스 자체적으로 참조하는 메모리 값을 변경한다는 점을 확인할 수 있습니다.

- 이와 달리 Instance method 는 클래스를 인스턴스화 할 시 각각의 인스턴스로 인해 Instance method 를 
불러올 때에만 변수값이 변하는 것을 확인할 수 있습니다.
즉, instanceMethod 는 인스턴스 를 통해서 호출 될 시에는 메모리에 변수에 대한 새로운 참조값을 만든다는 점을 확인할 수 있습니다.








