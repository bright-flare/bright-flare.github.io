---
title: "Consumer 생성하기"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Consumer 생성하기 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Consumer 생성 기본

Consumer는 다음과 같이 설정 객체를 구성한 뒤 생성한다.

```java
kafkaProps.put("group.id", groupId);
kafkaProps.put("bootstrap.servers", servers);
kafkaProps.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
kafkaProps.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
kafkaProps.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);

KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(kafkaProps);
```

Producer와 비슷해 보이지만, Consumer는 "어디까지 읽었는가"를 관리해야 하므로 그룹/오프셋 설정 의미가 더 크다.

## 주요 설정 포인트

- `group.id`
  - 동일 그룹 내 Consumer들이 파티션을 분담한다.
- `key.deserializer`, `value.deserializer`
  - Producer의 직렬화 포맷과 정확히 맞아야 역직렬화 실패를 피할 수 있다.
- `enable.auto.commit`
  - 자동 커밋 여부를 결정한다. 정합성이 중요하면 수동 커밋을 검토한다.

## 토픽 구독 방법

```java
consumer.subscribe(Collections.singletonList("customerCountries"));
consumer.subscribe(Pattern.compile("test.*"));
```

- 명시적 토픽 목록 구독
- 정규식 패턴 구독

패턴 구독은 운영 편의성이 높지만, 대규모 토픽 환경에서는 메타데이터 조회 비용이 커질 수 있다.

## 실무 포인트

- 구독 대상 토픽이 많아질수록 리밸런스 영향 범위도 함께 커진다.
- 특히 정규식 기반 대규모 구독은 브로커/클라이언트/네트워크에 추가 부담을 줄 수 있으므로 모니터링이 필요하다.
