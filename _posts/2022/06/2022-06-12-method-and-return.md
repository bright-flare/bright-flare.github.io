---
title: Method와 return
description: Method는 언제 종료되는가 ?
author: bright-flare
date: 2022-06-12
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/method-and-return/thumbnail.png
---

## Method(메소드)가 언제 종료되는가

Java에서 메소드가 종료되는 조건은 아래와 같다.

1. 메소드의 모든 문장이 실행되었을 때
2. **return** 문장에 도달했을 때
    1. **return** 타입이 **void**일 경우에는 메소드의 모든 문장이 수행되면 메소드가 종료된다.
3. 예외가 발생했을 때

<br>

## return 키워드 이후

- **return** 키워드 이후에는 어떠한 문장도 있으면 안된다.
- 만약 **return** 키워드 이후에 어떤 문장이 있다면 컴파일  에러가 발생한다.
- 예외 !!

```java
public int getSomeNumber(){
  int number = 0;
  if(number == 0){
    return number;
  } else {
    return --number;
  } 
}
```

- 예외적으로 `if`문 안에 **return** 키워드가 있을 경우 `if` 조건에 해당하는 조건 이외에 다른 경우가 있기 때문에 하나의 메소드 안에 두개 이상의 **return**이 있어야 한다.
- `if`문이 `else`문 없이 하나만으로 되어있는 경우 `else`문에 **return**이 없다면 컴파일 에러가 발생한다.
- 결국 **return** 타입이 선언되어 있다면 무조건 **return**하는 상황을 만들어 줘야하는 것이다.

<br>

## 메소드 수행 종료 시키기

메소드 수행을 강제로 종료시키고 싶으면 아래와 같이 하면된다.

**return** 타입이 **void**여도 문제 없다.

```java
public void foo(){

  // some code...
  
  return;
}
```