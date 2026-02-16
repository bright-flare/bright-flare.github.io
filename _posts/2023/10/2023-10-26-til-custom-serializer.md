---
title: "Custom Serializer"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Custom Serializer 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 왜 Custom Serializer가 필요한가

Kafka 네트워크로 전달되는 데이터는 결국 바이트 배열이다.  
애플리케이션 객체를 그대로 보낼 수 없기 때문에 직렬화 과정이 필수다.

기본 제공 직렬화기(`StringSerializer`, `IntegerSerializer`)로 충분한 경우도 있지만, 도메인 객체를 직접 전송하려면 커스텀 구현이 필요하다.

## 선택지 2가지

1. `Serializer<T>`를 직접 구현한 Custom Serializer를 만든다.
2. Avro/Protobuf 같은 검증된 직렬화 라이브러리를 사용한다.

초기 학습 단계에서는 직접 구현이 이해에 좋지만, 운영 단계에서는 스키마 진화와 호환성 관리까지 고려해야 한다.

## 기본 구현 예시

```java
public class CustomerSerializer implements Serializer<Customer> {

    @Override
    public void configure(Map<String, ?> configs, boolean isKey) {
    }

    @Override
    public byte[] serialize(String topic, Customer customer) {
        // ... customer to byte
    }

    @Override
    public void close() {
    }
}
```

핵심은 `serialize()`에서 도메인 객체를 안정적으로 바이트로 변환하는 것이다.

## 실무 포인트

- 직접 구현 직렬화는 변경에 취약할 수 있다.
- 필드 추가/삭제가 잦은 도메인이라면 스키마 기반 직렬화 도입을 먼저 검토하는 편이 안전하다.
