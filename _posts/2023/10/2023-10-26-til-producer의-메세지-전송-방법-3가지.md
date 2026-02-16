---
title: "Producer의 메시지 전송 방법 3가지"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Producer의 메시지 전송 방법 3가지 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 1) Fire-and-forget

```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");
try {
    kafkaProducer.send(record);
} catch (Exception e) {
    e.printStackTrace();
}
```

가장 단순한 방식이다. 전송 요청만 하고 결과를 기다리지 않는다.  
코드가 간단하고 호출 스레드가 오래 블로킹되지 않는 장점이 있지만, 전송 실패를 세밀하게 감지하기 어렵다.

## 2) 동기 전송 (Synchronous)

```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");
try {
    RecordMetadata metadata = kafkaProducer.send(record).get();
} catch (Exception e) {
    e.printStackTrace();
}
```

`Future.get()`으로 브로커 응답을 기다리는 방식이다.  
성공/실패를 호출 지점에서 즉시 판단할 수 있어 단순한 정합성 검증에는 유리하다.

하지만 응답 대기 동안 스레드가 블로킹되어 처리량이 떨어질 수 있다.  
고트래픽 실서비스에서는 병목이 되기 쉽다.

## 3) 비동기 전송 (Asynchronous)

```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");
kafkaProducer.send(record, (metadata, e) -> {
    if (e != null) {
        e.printStackTrace();
    }
    // success handling
});
```

대부분의 실무 환경에서 기본 선택지가 되는 방식이다.  
전송 결과를 콜백에서 처리하므로 호출 스레드는 빠르게 다음 작업을 진행할 수 있다.

주의할 점은 콜백에서 무거운 블로킹 작업을 직접 수행하지 않는 것이다.  
필요하면 별도 워커 스레드나 큐로 넘겨 Producer 지연을 막아야 한다.

## 어떤 방식을 선택할까

- 정합성 확인이 매우 중요하고 처리량이 낮은 배치성 작업: 동기 전송 검토
- 일반적인 온라인 서비스: 비동기 전송 + 실패 콜백 + 재시도 정책
- 단순 로그성 이벤트: fire-and-forget도 가능하지만 유실 허용 범위를 명확히 해야 함
