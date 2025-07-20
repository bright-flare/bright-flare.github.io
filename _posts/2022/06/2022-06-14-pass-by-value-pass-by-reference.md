---
title: Pass by value vs Pass by reference
description: 둘의 차이를 알아보자.
author: bright-flare
date: 2022-06-14
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/pass-by-value-reference/thumbnail.png
---

## Pass by value vs Pass by reference

**Pass by value : 값을 전달한다.  
Pass by reference : 참조(주소값)을 전달한다.**  
메소드가 **매개변수로** **Value(값 자체)**를 전달 받았을 때와 **Reference(참조 주소값)**를 전달 받았을 때를 살펴보자.  

<br>
<hr>

## 예제 코드

이론적인 설명보다는 코드로 먼저 확인한다.

`PassBy`라는 클래스에는 `passByValue()`, `passByReference()` 메소드가 있다. 이 두개의 메소드는 매개변수를 전달받아 값을 변경 시키는 행위를 한다.

```java
public class PassBy {
  
  public void passByValue(int number, String str) {
    number = 20;
    str = "변경";
  }
  
  public void passByReference(Member member) {
    member.setUsername("현섭");
  }
}
```

### 테스트

```java
public class Member {
  private String username;
  // getter, setter...
}

class PassByTest {
  
  @DisplayName("passByValue 메소드에서 값이 변하지 않는지 테스트.")
  @Test
  public void passByValueTest() {
  
    int number = 1;
    String str = "abc";
  
    PassBy passBy = new PassBy();
    passBy.passByValue(number, str);
  
    assertEquals(1, number);
    assertEquals("abc", str);
    
    // Test passed
  }
  
  @DisplayName("passByReference username의 값이 변하지 않는지 테스트")
  @Test
  public void passByReferenceTest() {
    Member member = new Member();
    member.setUsername("sseob");
  
    PassBy passBy = new PassBy();
    passBy.passByReference(member);
  
    assertEquals("sseob", member.getUsername());
    
    // Tests failed
  }
}
```

### passByReferenceTest()

`passByReferenceTest()` 메소드가 **member** 변수의 **username**을 **“현섭”** 으로 변경하여 테스트는 실패로 끝난다.

어떻게 보면 메소드가 문자열을 변경 하였으니 당연하다고 생각할 수 있다.

메소드는 참조 자료형을 매개변수로 전달받을 때에 해당 변수의 주소값을 전달받는다. 예제코드로 살펴보면 **member** 변수가 참조하는 주소값을 전달받는 것이다.

다시 말해서 메소드로 전달하는 변수의 주소값이 가리키고 있는 객체와 메소드가 전달받은 매개변수가 가리키고 있는 객체가 같다.

**이처럼 매개변수로 참조하는 주소값을 전달 하는 것을 “Pass By Reference” 라고 한다.**

### passByValueTest()

`passByValue()` 메소드가 **number** **변수**와, **str 변수**의 값을 변경하는데도 `passByValueTest()` 테스트는 **성공**으로 마무리 된다.

<br>
<hr>

## passByValueTest()는 왜 성공 ?

**passByReferenceTest()**의 예제와는 달리 잘 이해가 되지 않는다. 메소드로 인해 값을 바꿨는데 바뀌지가 않았다.

**int**는 값 자체를 가지는 **기본 자료형(primitive type)**이다.
메소드는 주소값을 가지고 있지 않은, 값 자체를 가지고 있는 “기본 자료형의 매개변수를 전달 받을 때에 값만을 전달받는다.”

예제 코드로 말하자면 **number**변수의 1이라는 값을 복사하여 `passByValue()` 메소드에서 처리하게 된다. 복사한 값을 가지고 무엇을 하든 **원래의 값인 1이라는 값에는 아무런 변화가 없다.**

**이처럼 값만을 복사 전달하여 원래의 값에는 영향을 미치지 않는 것을 “Pass By Value” 라고 한다.**

<br>
<hr>

## String은 기본 자료형이 아닌데도 문자열이 변하지 않았다.

String은 Reference 타입인데도 불구하고, 그러니까 객체를 가리키는 주소값을 전달 받았을텐데 왜 문자열이 변하지 않았을까 ?

String은 객체를 생성하는 방법이 두가지가 있다. <a href="https://bright-flare.github.io/posts/reference-type/" target="_blank"> (참고) Reference Type</a>

값을 변경시킨게 아니라 리터럴 방식으로 String 객체를 새로 생성한 것이다. 그래서 원래의 **“sseob”** 문자열을 가지고 있는 String 객체와 **“현섭”** 문자열을 가지고 있는 새로운 String 객체는 **서로 다른 객체인 것이다.**

그래서 결론적으로 문자열이 변경되지 않은 것이다.