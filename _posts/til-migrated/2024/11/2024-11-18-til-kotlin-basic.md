---
title: "kotlin-basic"
date: 2024-11-18 00:00:00 +0900
categories: [kotlin]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kotlin/kotlin-basic.md"
---
# 아토믹 코틀린 학습중에 기록하고 싶은 것들.

- 프로그램 진입점은 fun main() 이다.
  - java의 public static void main(String[] args)와 같다.
- 세미콜론 생략해서 사용하는게 10분만에 적응될 정도로 편함
- Any Type은 java의 Object와 같은 역할을 한다. equals, hashcode 구현되어있음.
    - 같은 역할이지 Object는 아닌듯.
    - 최상위 객체인건 맞음.

- 기본 접근 지시어가 public이다.
- 상속을 구현할 때 단순하게 표현 가능하다.

- Unit 타입은 java의 void와 같다.
    - The type with only one value: the Unit object. This type corresponds to the void type in Java.
- primitive type과 reference type을 구분하지 않는다.
- void 생략 가능

- 타입을 콜론 이후에 선언해준다.

- kt 파일에 function을 마구마구 생성할 수 있음.
- val, var java final 키워드를 생략할 수 있어서 편하네.
- 배열이 아닌 List객체도 [] 를 사용해 인덱스에 접근 가능하다.
- in keyword로 값이 어떤 범위에 포함되는지 검사 가능하다
  - 10 in 1..10 // true
  - 0 <= num && num <= 10 // java애서는 이렇게 체크해야 한다.

- When expressions.
  - java의 switch와 비슷하다.
  - java의 경우 14 버전부터 정식으로 switch expression 기능을 사용할 수 있다.
  - java의 switch case문에서 default 대신에 else를 사용함.
  - when + enum 조합으로 간편하게 enum분기처리에 대한 작업을 편하게 할 수 있다.
    - enum을 사용할 경우 else문을 사용하지 않고 상수 전체에 대한 분기처리를 할 수 있다.
    - 컴파일타임에 추가,삭제된 enum 상수에 대한 분기처리 오류를 방지할 수 있다.

- java의 instanceof -> is로 간편비교 가능 !
- try-catch with resources 대신 use로 resource를 사용 가능하다.
- safe call 오마이갓...... 미쳤음. null일 경우 호출하지 않는 기능.
- `==` 연산자로 참조타입 비교시 내부적으로 equals를 호출한다.
  - 참조타입의 주소값 비교를 하고싶으면 `===` 연산자를 사용하면 된다.

- string template은 편하다
```kotlin
val name = "test"
println("캬 ~ ${name}")
```

- raw string은 편하다 
- " 같은 문자열을 escape하지 않아도 된다 
```kotlin

  // raw string !
  val lines: String = """ 
    첫
    둘
    셋 줄 테스트 => ${words}
  """.trimIndent()

```

- 한줄짜리 함수를 만들 수 있따.. 내기준 충격
    - expression body라고 한다. 
    - 식본문
    - lambda expression 처럼 return 생략 가능함 !!
    - 식 본문만 반환 타입 추론 가능하다. 와우
```kotlin
fun multiplyByThree(number: Int): Int = number * 3

fun multiplyByFour(number: Int) = number * 4 // 식 본문만 반환 타입 추론 가능
```
- 함수 본문이 중괄호로 둘러싸인 경우 block body 블록 본문이라고 한다.


- if문이 식으로 활용 가능. if 자체를 return할 수 있다. 와우 !
- 주의 !! if를 식으로 활용할 때에는 반드시 else가 필요하다.
- 심지어 function body 자체를 생략할수도 있다.
```kotlin
fun oneOrTheOther(exp: Boolean): String =
  if (exp) {
    "True!"
  } else {
    "False!"
  }
```
- for loop 에서의 in, range expression 활용 너무 편하다 
- kotlin에서는 IntRange 라는 정수 `0..10` 과 같은 정수 범위 타입이 존재함 .. !
- range 말고도 Progression 이라는 타입이 있다. `0..10 step 2` 표현식과 같이 0부터 10인데 2tep씩 증가와 같은 Progression의 타입이다


- default parameter 너무 편하다 기본값이 적용되어 있는 메서드, 값을 전달 받는 메서드 두 부류로 overloading하지 않아도 된다.
- named argument 너무 편하다 2222 함수에 paremeter를 넘길 때 parameter name을 지정하여 넘길 수 있다. builder 패턴을 대체 가능하다.
- throws 생략 가능. Checked을 kotlin에서는 취급하지 않는다. 언어 호환성을 위해 표현할 수는 있지만 생략 가능하다.
- 생성자 이용시 new 키워드 생략 가능하다 ! 간결해서 좋음.
- varargs. 가변인자를 java에서는 String... 이와같이 표현하지만 kotlin에서는 varargs키워드를 사용하여 더 간결하고 편하게 사용 가능하다.(스프레드 연산자를 곁들여서 사용 가능.)

## Kotlin의 Class

- Class 선언시 public은 생략 가능하다.
- field를 만들면 getter, setter를 compile 단계에서 자동으로 생성해준다.
  - val feild는 getter만 생성
  - var field는 getter, setter 모두 생성
- 생성자 선언과 필드선언을 동시에 할 수 있다.

- init 블럭을 활용하여 객체 생성시 초기화관련 코드를 넣을 수 있다.
```kotlin
  init {
    if (age < 0) {
      throw IllegalArgumentException("나이는 0보다 작을 수 없습니다.")
    }
  }
```

- Class를 선언하면서 동시에 생성자를 선언한다.
  - 이것을 주 생성자 primary constructor라고 한다.
  - 주 생성자는 반드시 존재해야 한다.
  - Class body가 없으면 생략해도 된다.
```kotlin
// 생성자
// getter, setter
class Person(val name, var age){

}
```
- 생성자에 default parameter를 적용 가능하다.
```kotlin
class Person(
  val name: String = "default_현섭",
  var age: Int = 110
)
```

- Custom getter를 만들 수 있다.
```kotlin
class Person(
  val name: String = "default_현섭",
  var age: Int = 110
){ 
  val isAdult get() = this.age >= 20
}

fun main(){
  val person = Person()
  person.isAdult // getter처럼 호출 가능
}
```

- kotlin singleton Class를 만드는 방법이 매우 간단한다.
- 문법이 매우 간결하다.
```
object Singleton {
  var test: String = "test"
}
```

- inner class를 작성할 때 기본으로 static inner class로 만들어진다.
  - java에서 inner class는 static으로 만드는 것을 권장한다. 의도치않은 메모리 누수, 바깥 클래스에 대한 참조 문제 등
  - 때문에 항상 static 키워드를 붙여주어야 하는데 kotlin에서는 기본적으로 내부 클래스를 생성할 때 inner 키워드를 붙여주지 않는 한 static class로 생성된다.

- Data class
  - equals, hashcode, toString을 모두 자동으로 만들어준다.
  - 생성자에 named argument 를 사용하면 builder패턴도 필요없다 !
  - 조쉬롱 형님이 lombok 말고 data class 활용하라고 말씀하심

- collection
  - Collection을 생성할 때 불변, 가변 Collection을 처음부터 지정하여 생성할 수 있다.

- List
  - listOf(1, 2, 3), mutableListOf(1, 2, 3)
- Set
  - setOf(100, 200, 300), mutableSetOf(100, 200, 300)
- Map
  - mapOf(1 to "test", 2 to "test2"), mutableMapOf(1 to "test", 2 to "test2")

- 확장함수

- lambda
  - kotlin에서는 function이 일급시민이다. 
  - function을 parameter로 넘길 수 있고, 변수에 넣을 수 있다. 와우 ......
  - function이 마지막 paranmeter에 위치하는 경우 () 밖에 labda를 작성하여 넘겨줄 수 있다.
  - java에서 람다식 밖에 영역에 있는 변수를 사용할 경우 final이어야만 한다. 하지만 kotlin에서는 사용가능하다 !!
    - 이부분은 final 변수가 아닌경우 캡처링 관련 복잡한 문제가 있어서 language level에서 제한하고 있다.
    - kotlin에서는 Closure덕분에 해당 final이 아닌 변수도 lambda 식에서 문제 없이 사용 가능하다

- kotlin collections package에는 수많은 매우 편리한 확장함수들이 많다 !!
  - filter, map, mapNotNull, all, none, any, sortedBy, firtst, firstOrNull, last, lastOrNull 등등 와우..

- takeIf, takeUnless
  - if else return 문법을 축약하여 표현할 수 있다.

- scope function
  - 람다를 사용해서 임시 범위를 만들어 사용할 수 있다.

|function|Object reference|반환값|확장함수 여부|
|:---:|:---:|:---:|:---:|
|let|it|Lambda result|O|
|run|this|Lambda result|O|
|apply|this|Context object|O|
|also|it|Context Object|O|
|with|this|Lambda result|X|
