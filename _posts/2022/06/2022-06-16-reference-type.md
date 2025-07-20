---
title: Reference Type
description: Reference Type에 대하여.
author: bright-flare
date: 2022-06-16
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/reference-type/thumbnail.png
---

## Reference Type ?

- Reference Type(참조 자료형)은 기본 자료형 8개를 제외한 나머지 모두를 말한다.

- Primitive Type 기본 자료형
  - 정수형 : `byte`, `short`, `int`, `long`, `char`
  - 소수형 : `float`, `double`
  - 기타 : `boolean`

<br>

## 할당

- 기본 자료형은 값 자체를 할당한다.

```java
int number = 100;
```

- 참조 자료형은 memory를 참조하는 address. 주소값을 할당한다.

```java
User someUser = new User(); // 변수 someUser에 User객체를 생성한 다음 그것을 참조하는 주소값을 할당.
User user = someUser; // someUser객체가 참조하는 주소값을 user 변수에 할당.
```

## 연산자

 `+` 연산자는 일반적인 참조 자료형에서 사용할 수 없다. 하지만 **String Class**는 특수하게 사용할 수 있다.

<br>

## new. 생성

- 참조 자료형은 `new` 키워드를 통해 생성할 수 있다.

- 예외적으로 **String Class**는 `new` 키워드 없이 객체를 생성할 수 있다.
    1. **생성자를 통한 String 객체 생성** : `new` 키워드를 통해 객체를 생성하는 것도 당연히 가능하다.
    2. **리터럴 방식으로 String 객체 생성** : 마치 기본 자료형에 값을 할당하듯이 문자열 값을 할당하는 것처럼 보인다. 하지만 실제로는 생성된 String 객체의 address를 할당하는 것이다.


```java
String str = "문자열 !"; // 이렇게 쌍따옴표로 감싸 String 객체를 생성하는것을 리터럴 생성 방식이라고 한다.  

String newStr = new String("문자열 !"); // String 생성자를 통해 String 객체 생성
```

<br>

## Constructor. 생성자

> **Java**는 개발자가 생성자를 만들지 않아도 `Compiler`가 컴파일 시점에 다른 생성자가 없을 경우 기본 생성자를 만들어준다.
> 

생성자는 **Class**와 이름이 **동일**해야 한다 !

```java
public class User {
  // constructor
  public User () {
  
  }
}
```

### 생성자는 왜 필요할까 ?

- **Java**의 생성자는 어떤 **Class**의 객체(인스턴스)를 생성하기 위해서 존재한다.

### 생성자의 return 타입

- 생성자도 method인데 return 타입이 왜 없을까 ?
- 생성자에 return 타입이 없는 이유는 생성자의 return 타입은 해당 클래스의 객체이기 때문이다.