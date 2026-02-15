---
title: "Consumer 생성하기"
date: 2023-11-16 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-4]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-4/Consumer 생성하기.md"
---
- Producer 인스턴스를 생성하는 것과 매우 비슷하다.
- 필수 속성도 3개로 동일하다.
	- bootstrap.servers
	- key.serializer
	- value.serializer
- 일반적으로 자주 사용하는 추가 1개 속성
	- 컨슈머 그룹을 지정하는 `group.id` 속성


## code

```java
kafkaProps.put("group.id",groupId);  
kafkaProps.put("bootstrap.servers",servers);  
kafkaProps.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");  
kafkaProps.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");  
kafkaProps.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);  
  
KafkaConsumer<String, String> consumer = new KafkaConsumer<String, String>(kafkaProps);
```


## Topic 구독하기

- N개의 토픽을 구독할 수 있다.
- 정규식을 사용해서 pattern과 일치하는 N개의 토픽을 구독할수도 있다.
	- 정규식을 사용하는 경우 정규식과 match되는 이름을 가진 새로운 토픽을 생성하는 경우, 거의 즉시 리밸런스가 발생하면서 컨슈머들은 새로운 토픽으로부터의 읽기 작업을 시작하게 된다.
```java
// collection을 넘겨 N개의 토픽 구독 가능
consumer.subscribe(Collections.singletonList("customerCountries"));

// 정규식을 통해 N개의 토픽 구독 가능.
consumer.subscribe(Pattern.compile("test.*"));
```

# 🔴 정규식으로 토픽 구독시 주의 !

- 전체 토픽의 일부를 구독할 때 정규식으로 지정하는 경우 컨슈머는 전체 토픽과 파티션에 대한 정보를 브로커에게 일정한 간격으로 요청.
- 패턴으로 찾아내는 토픽, 파티션의 크기가 클 수록 브로커, 클라이언트, 네트워크 오버헤드를 발생시킨다.
- 메세지 데이터를 주고받는데 사용되는 네트워크 대역폭보다 토픽의 메타데이터를 주고받는데에 사용되는 대역폭 크기가 더 큰 경우도 있다.
