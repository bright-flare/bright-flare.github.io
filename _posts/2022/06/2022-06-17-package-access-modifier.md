---
title: Package and Access Modifier
description: Package, Access Modifier에 대하여 알아보자.
author: bright-flare
date: 2022-06-17
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/package-access-modifier/thumbnail.png
---

**package와 Access Modifier (접근 제어자)에 대해 간단하게 정리한다.**

<br>
<hr>

## package

- package 선언은 java file 가장 위에 있어야 한다.
- 같은 package를 중복 선언할 수 없다.
- pakcage 이름과 Directory이름이 같아야만 한다. java file을 만들었는데 directory 이름과 package이름이 일치하지 않는 경우 컴파일이 되지 않는다.

### Convention

- package 이름은 모두 소문자로 지정한다.
- Java의 예약어를 사용하면 안된다. int, static, enum 등..

### 참고

| 패키지 시작 이름 | 내용 |
| --- | --- |
| java | 자바 기본 package이다. (Java 벤더사에서 개발) |
| javax | 자바 확장 package이다. (Java 벤더사에서 개발) |
| org | 일반적으로 비 영리단체 (오픈 소스)의 package이다. |
| com | 일반적으로 영리단체(회사)의 package 이다. |

<br>
<hr>

## Access Modifier (접근 제어자)

**Java에는 4가지 접근 제어자가 있다.**
 
1. **public** - 누구나 접근할 수 있다.
2. **protected** - 같은 package내에 있거나 **상속**받은 경우에만 접근 가능하다.
3. **package-private** - 아무런 접근 제어자를 적어주지 않을 때에 적용된다. 같은 패키지 내에 있을 때 접근할 수 있다.
4. **private** - 해당 Class 내에서만 접근 가능하다.

### 표로 보는 접근 제어자

|  | 해당 클래스 안에서 | 같은 package에서 | 상속받은 클래스에서 | import한 클래스에서 |
| --- | --- | --- | --- | --- |
| public | O | O | O | O |
| protected | O | O | O | X |
| package-private | O | O | X | X |
| private | O | X | X | X |