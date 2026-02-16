---
title: "Custom Serializer 문제"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Custom Serializer 문제 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 왜 문제가 생기는가

직접 구현 Serializer는 "현재 모델 구조"에 강하게 결합된다.  
아래처럼 단순한 객체도 필드가 추가/변경되는 순간, 생산자/소비자 버전 호환성이 깨질 수 있다.

```java
public class Customer {

    Long id;
    String name;

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

예를 들어 `startDate` 같은 필드가 추가되면, 구버전 컨슈머가 신버전 메시지를 해석하지 못할 가능성이 생긴다.

## 대안: 스키마 기반 직렬화

JSON/Avro/Protobuf 같은 표준 형식을 사용하면 변경 부담을 크게 줄일 수 있다.  
그중 Avro는 Kafka 생태계에서 자주 선택되는 옵션이다.

## Avro를 많이 사용하는 이유

- 언어 중립적 스키마 정의(JSON 기반)
- 강한 타입 검증
- 스키마 호환성(상위/하위) 규칙 제공

즉, 생산자와 소비자가 완전히 동시에 배포되지 않아도, 호환 규칙 안에서 점진적 전환이 가능하다.

```json
# 예전 스키마
{
  "namespace": "customer.avro",
  "type": "record",
  "name": "Customer",
  "fields": [
    {"name": "id", "type": "int"},
    {"name": "name",  "type": "string"},
    {"name": "faxNumber",  "type": ["null","string"], "default": null}
  ]
}
```

```json
# 새로운 스키마
{
  "namespace": "customer.avro",
  "type": "record",
  "name": "Customer",
  "fields": [
    {"name": "id", "type": "int"},
    {"name": "name",  "type": "string"},
    {"name": "email",  "type": ["null","string"], "default": null}
  ]
}
```

## 실무 체크 포인트

- 스키마 호환성 규칙(Backward/Forward/Full)을 먼저 정한다.
- 읽는 쪽이 작성 시점 스키마를 참조할 수 있는 구조(예: Schema Registry)를 함께 마련한다.
