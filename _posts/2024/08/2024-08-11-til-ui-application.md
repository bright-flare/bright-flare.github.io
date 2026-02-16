---
title: "ui,Application"
date: 2024-08-11 00:00:00 +0900
categories: [OOP, DDD]
tags: [TIL]
description: "ui,application의 핵심 개념과 실무 포인트를 정리한 학습 노트입니다."
author: bright-flare
---
## 한눈에 보기
- 표현 영역과 응용 영역
- 표현 영역, ui
- 사용자의 request를 해석한다.

## 표현 영역과 응용 영역

## 표현 영역, ui

- 사용자의 request를 해석한다.
- 사용자가 시스템을 사용할 수 있는 흐름을 제공하고 제어한다.
- 사용자의 요청을 알맞은 응용서비스에 전달하고 결과를 사용자에게 전달한다.
- ui 영역은 주로 request parameter, body 등의 값에서 필수값, 값의 형식, 범위 등을 검증한다.
    - Application 영역에서는 주로 정책적으로 맞지 않는 값이나 논리에 대한 검증을 한다. (id 중복, 정책별 검증 등)
- Application 영역의 서비스를 호출하기 전에 parameter를 가공하여 전달해주는 역할을 한다.
    - querystring이나 body 값을 java 객체로 변환하여 applcation service 로 전달한다.
    - querystring이나 body 값을 java 객체로 변환하는 것은 spring framework을 사용한다면 매우 편하게 지원받을 수 있다.

## 응용 영역, Application

- 실제 사용자가 원하는 기능을 제공한다.
- 사용자가 Web browser, REST API, TCP socket 을 사용하여 접근하는지 알 필요가 없다.
단지 기능 실행에 필요한 입력 값을 받고 실행 결과를 return한다.

### 응용 서비스, Application service

- 사용자의 요청을 처리하기 위해 repository에서 domain 객체를 가져와 사용함.
- transaction 처리를 담당한다.
- 응용 서비스는 표현 영역과 도메인 영역을 연결하는 매개체 역할을 하는데 이는 디자인 패턴에서 파사드(facade) 패턴과 같은 역할을 한다.
    - java에서는 대표적으로 slf4j, log4j, logback 을 찾아보면 예시를 확인해볼 수 있음.
- Application service에 domain logic 넣지 않는 것이 좋다.
    - 도메인 로직을 일부 구현하면 코드중복, 로직 분산 등 코드품질에 악영향을 끼칠 수 있다.
    - 도메인 로직이 한 영역에 위치하지 않고 서로다른 Application service에 존재하는 것은 도메인 로직을 파악하기 위해 여러 Application service 영역을 분석해야 한다는 것을 의미한다.
    - 초반에 DDD 이점에 대하여 공부할 때를 생각해보자.
        - Order 도메인을 통해서만 비즈니스 로직을 실행하므로써 유지보수에 유리한 코드를 작성할 수 있었다는 것을 떠올려보자
- Application service 크기가 커질 경우
    - 구현해야 하는 기능들이 많아서 MemberService가 매우 커진다면 기능별로 분리된 새로운 Class를 만들어 분리할 수 있다.
    - 한 클래스에 코드가 모이기 시작하면 분리하는 것이 좋은 상황임에도 습관적으로 기존에 존재하는 클래스에 억지로 끼워넣게 된다. -> 코드 품질의 저하가 발생함.
    - 예를들어 MemberService Class만 존재하던 것을 아래와 같이 나누어볼 수 있다.
        - MemberValidationService -> 멤버 비즈니스 정책 validation 서비스
        - MemberChangePasswordService -> 멤버 비밀번호 변경 서비스
        - MemberNotificationService -> 멤버 알림 담당 서비스
        - 내부적으로 같은 Member 도메인을 통해 비즈니스 로직을 실제로 사용하더라도 응용 서비스를 나누어 더 명확하게 관리할 수 있다.
- 인터페이스
    - 인터페이스가 명확하게 필요하기 전까지는 응용 서비스에 대한 인터페이스를 작성하는 것이 좋은 선택이라고는 볼 수 없다.
    - 필요할 때 기능을 추상화 하도록 하자.
    - TDD, 테스트 주도 개발을 한다면 인터페이스를 미리 만들어 mock 테스트를 하겠지만 그게 아니라면 미리 만들어 둘 필요가 없다.
- 표현 영역에서 필요한 데이터만 리턴하는 것은 기능 실행 로직의 응집도를 높이는 확실한 방법이다.
    - 도메인 객체(Member) 자체를 return한다면 ui 영역에서 도메인의 메서드를 실행시킬 수 있기 때문에 응용 서비스 관점에서 응집도가 낮아진다고 볼 수 있다.
    - 기존에 응용 서비스 영역에서 도메인 객체를 repository를 통해 loading하여 도메인 객체의 메서드를 실행시켜 비즈니스 로직을 실행하는데 이를 ui 표현 영역에서도 동일하게 한다면 응집도가 낮아지는 꼴이 된다.
- 표현 영역에 의존하지 않아야 한다.
    - 표현 영역에서 사용하는 HttpServletRequest, HttpSession 같은 클래스를 사용한다면 Application service 단독트로 테스트하기가 어려워진다.
        - 테스트할 때 HttpServletRequest, HttpSession 객체도 같이 만들어야 하니까 !
    - 표현 영역에 의존하면 표현 영역의 역할까지 대신하는 상황이 벌어질 수 있다.
