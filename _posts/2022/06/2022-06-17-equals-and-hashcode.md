---
title: equals() and hashCode()
description: Object 클래스의 메소드인 equals() 와 hashCode() 에 대해서 정리한다.
author: bright-flare
date: 2022-06-17
categories: [Language, Java]
tags: [Java]
image:
  path: /assets/img/post/2022/equals-and-hashcode/thumbnail.png
---

## 비교

Java에서 비교를 할 때에는 `==` 연산자와 Object 클래스의 `equals` 메소드를 이용하여 비교할 수 있다.

비교를 한다는 것은 아래와 같이 두가지로 비교할 수 있다.

1. 해당 객체 또는 값이 **동일**한가 ?
2. 해당 객체가 **동등**한가 ?

### 동일성

동일성 이란 A라는 값 또는 객체와 B라는 값 또는 객체가 완전하게 같다고 말할 수 있는 것을 뜻한다.

**primitive 타입**일 경우 값만 가지고 있으니 값이 같다는 뜻이 될 것이고, 

**Reference 타입**일 경우 객체의 메모리 주소값을 가지고 있으니 참조하고 있는 메모리 주소값이 같다는 뜻이 될 것이다.

즉, 두가지 타입 모두 `==` 연산자를 통해 비교했을 경우 **true**이면 동일성을 만족한다고 할 수 있다.

### 동등성

동등성이란 어떤 클래스의 A 객체와 B 객체 두 객체에 대하여 참조하고 있는 메모리 주소값은 다르지만 두 객체가 가지고 있는 정보가 동일하여 **논리적으로 같다고 말할 수 있는 상태**를 말한다.

**논리적으로 같다고 말할 수 있다면 논리는 어떻게 정의할 수 있을까 ?** 

Java에서는 **Object 클래스의 equals() 메소드**를 통해 객체 간에 동등성 비교를 할 수 있다.

<br>
<hr>

## equals 메소드 재정의

`Member` 클래스는 **id, username, age** 필드를 가지고 있는데 3개의 필드가 가지고 있는 정보가 모두 같다면 true를 return하는 **equals** 메소드를 재정의 하였다.

- 아래 예제 코드는 Intellij IDEA를 통해 만든 **equals** 메소드와 **hashCode** 메소드이다.

```java
public class Member {

  private Long id;
  private String username;
  private int age;
  
  @Override
  public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    Member member = (Member) o;
    return getAge() == member.getAge() && Objects.equals(getId(),
            member.getId()) && Objects.equals(getUsername(), 
            member.getUsername());
  }
  
  @Override
  public int hashCode() {
    return Objects.hash(getId(), getUsername(), getAge());
  }
}
```

### equals 메소드를 재정의 하였기 때문에 테스트는 성공이다.

```java
class MemberTest {

  @DisplayName("동등성 비교 테스트")
  @Test
  public void memberEqualsTest() {
  
    Member memberA = Member.builder()
          .id(1L)
          .username("sseob")
          .age(29)
          .build();
  
    Member memberB = Member.builder()
          .id(1L)
          .username("sseob")
          .age(29)
          .build();
  
  assertEquals(memberA, memberB); // test passed
  }
}
```

<br>
<hr>

## equals(), hashCode() 메소드를 재정의 할 때 유의할 점

<br>

### equals()

**equals()** 메소드는 객체와 객체가 동일한지에 대한 여부를 반환하는 메소드 이다.

객체와 객체를 비교할 때 사용한다. **equals()** 메소드를 재정의 할 때에는 몇가지 유의점을 고려하여 재정의 하여야 한다.

1. **반사성(reflexivity)** : null이 아닌 모든 참조 값 x에 대해, x.equals(x) 는 true다. 
    - 즉, 객체는 자기 자신과 같아야 한다.
2. **대칭성(symmentry)** : null이 아닌 모든 참조 값 x, y에 대해, x.equals(y) 가 true면 y.equals(x)도 true여야 한다.
3. **추이성(transitivity)** : null이 아닌 모든 참조 값 x, y, z에 대해, x.equals(y) 가 true이고 y.equals(z)도 true이면 x.equals(z)도 true이다.
4. **일관성(consistency)** : null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환해야 한다.
5. **null과 같지 않아야 한다** : null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false다.
    - 모든 객체는 null과 같지 않아야 한다는 뜻.

### hashCode()

> Javadoc.
> 
> 객체에 대한 해시 코드 값을 반환합니다. 이 메소드는 `java.util.HashMap`에서 제공하는 것과 같은 해시 테이블의 이점을 위해 지원됩니다.
> 

**equals** 를 재정의한 클래스에서 **hashCode** 도 재정의해야 한다. 그렇지 않으면 **hashCode** 일반 규약을 어기게 되어 해당 클래스의 인스턴스를 `hashMap`이나 `HashSet`같은 컬렉션의 원소로 사용할 때 문제를 일으킨다.

1. **equals** 비교에 사용되는 정보가 변경되지 않았다면, Application이 실행되는 동안 그 객체의 **hashCode** 메소드를 몇 번이고 호출해도 일관되게 항상 같은 값을 반환해야 한다. 단, Application을 다시 실행한다면 이 값은 달라져도 무방하다.
2. **equals(Object)** 가 두 객체를 같다고 판단했다면, 두 객체의 **hashCode**는 똑같은 정수값을 반환해야 한다.
    - 논리적으로 같은 객체는 같은 **hashCode** 를 반환해야 한다.
    - **equals** 비교에 사용되지 않은 필드는 반드시 **hashCode** 를 생성할 때 제외해야 한다.
3. **equals(Object)** 가 두 객체를 다르다고 판단했더라도, 각각의 두 객체에 대해 **hashCode** 메서드를 호출하면 고유한 정수값을 반환할 필요는 없다. 그러나 같지 않은 객체에 대해 고유한 정수 결과를 생성하면 해시 테이블의 성능이 향상된다.