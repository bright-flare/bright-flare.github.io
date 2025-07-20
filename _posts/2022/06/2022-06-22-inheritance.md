---
title: Inheritance
description: inheritance(상속)에 대하여 알아보자.
author: bright-flare
date: 2022-06-22
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/inheritance/thumbnail.png
---

**상속(확장)에 대해** 알아보자.

## 예제 코드

```java
public class Child extends Parent {

}
```

상속은 **extends** 키워드를 통해 받을 수 있다. **extends** 는 **“확장하다”**라는 뜻인데 단어의 뜻 처럼 코드를 읽으면 **Child 클래스는 Parent클래스를 확장한다.** 라고 생각하면 된다.

예제의 **Parent 클래스**를 확장하면 **Parent** 클래스의 선언되어 있는 **public, protected**로 선언 되어 있는 모든 변수와 메소드를 사용할 수 있다.

**package-private, private**으로 선언 되어 있는 변수나 메소드는 상속받지 못하기 때문에 사용할 수 없다.

다중 상속이 불가능하다. 하나의 클래스만 상속받을 수 있다.

## 부모 클래스의 기본 생성자

```java
public class Parent {

  // default constructor
  public Parent() {
  
  }
}
  
public class Main {
  public static void main(String[] args) {
      Child child = new Child();
  }
}
```

**Child 클래스가 Parent클래스를 상속(확장)받으려면 Parent 클래스에 기본 생성자가 정의 되어 있어야 한다.**

**자식(Child)** 클래스를 컴파일할 때 컴파일러는 자식 클래스의 생성자에 `super()`가 추가하여 실행 시키는데 이는 **부모 클래스의 기본 생성자**를 뜻한다. (**super keyword**는 상위 타입의 객체를 뜻한다.)

`super()` 는 반드시 자식 클래스의 생성자에서 가장 첫줄에 선언 되어야 한다. 부모 클래스의 생성자가 먼저 실행이 되어야 한다.

결국 **Child** 클래스의 생성자를 호출하게 되면, **먼저 Parent 클래스의 기본생성자를** 호출하는 것이기 때문에 부모 클래스에는 기본 생성자가 꼭 정의 되어 있어야 한다.

### 만약 부모 클래스가 String을 매개변수로 갖는 생성자만 갖고 있다면 ?

부모 클래스의 기본 생성자는 자동으로 생성되지 않기 때문에, **기본 생성자를 명시적으로 만들어 주어야 한다.**

```java
public class Parent {

  private String str;
  
  // 추가로 기본 생성자를 만들어준다 !
  public Parent() {
  
  }
  
  // 원래 존재하고 있던 생성자.
  public Parent(String str) {
    this.str = str;
  }
}
```

자식 클래스의 생성자에 **부모 클래스의 생성자를 명시적으로 실행시켜 준다.**
이 경우에는 기본적으로 부모 클래스의 기본 생성자를 호출하려고 하기 때문에 컴파일 에러가 발생하니까, 명시적으로 String 매개변수를 갖는 부모 생성자를 호출 시키는 것이다.

```java
public class Parent {

  private String str;
  
  public Parent(String str) {
    this.str = str;
  }
}

public class Child extends Parent {

  public Child(){
    super("str");
  }
}
```