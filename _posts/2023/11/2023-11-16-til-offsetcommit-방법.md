---
title: "OffsetCommit 방법"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "OffsetCommit 방법 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 1) 자동 커밋

자동 커밋은 가장 간단한 방식이다.
- `enable.auto.commit=true`
- `auto.commit.interval.ms` 주기로 진행 상태를 커밋

구현은 쉽지만, 장애 시 중복 처리 범위가 예상보다 커질 수 있다.

## 2) 수동 커밋

수동 커밋은 애플리케이션이 커밋 시점을 제어한다.
- `enable.auto.commit=false`
- 처리 완료 시점 기준으로 커밋

정합성 제어는 좋아지지만, 코드 복잡도와 운영 책임이 늘어난다.

### `commitSync()`

```java
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));
    for (ConsumerRecord<String, String> record : records) {
        // ... do something
    }

    try {
        consumer.commitSync();
    } catch (CommitFailedException e) {
        // ...
    }
}
```

- 성공/실패를 명확히 확인 가능
- 실패 시 재시도 로직이 상대적으로 단순
- 브로커 응답 대기로 인해 블로킹 비용 발생

### `commitAsync()`

```java
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));
    for (ConsumerRecord<String, String> record : records) {
        // ... do something
    }
    consumer.commitAsync();
}
```

- 블로킹이 적어 처리량에 유리
- 순서 역전/재시도 꼬임 문제로 단순 재시도 전략이 어렵다
- 콜백은 주로 에러 관측/메트릭 수집 용도로 사용한다

## 3) 종료 시 조합 패턴

실무에서 자주 쓰는 패턴은 "평소 비동기 + 종료 직전 동기"다.

```java
try {
    while (!closing) {
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));
        for (ConsumerRecord<String, String> record : records) {
            // ... do something
        }
        consumer.commitAsync();
    }
    consumer.commitSync();
} finally {
    consumer.close();
}
```

마지막 커밋만큼은 성공 여부를 확인해 종료 안정성을 높인다.

## 특정 오프셋 커밋

대량 배치 중간에 진행 상태를 세밀하게 저장하려면 파티션별 오프셋을 명시적으로 커밋할 수 있다.  
이 방식은 리밸런스나 장애 상황에서 재처리 범위를 줄이는 데 유용하다.
