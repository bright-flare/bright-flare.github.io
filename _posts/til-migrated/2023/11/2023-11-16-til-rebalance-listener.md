---
title: "Rebalance Listener"
date: 2023-11-16 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-4]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-4/Rebalance Listener.md"
---
💡 컨슈머는 토픽 subscribe()를 호출할 때 ConsumerRebalanceListener를 전달해주면 리밸런싱을 리스닝할 수 있다.


## onPartitionsAssigned

- 파티션이 컨슈머에게 재할당된 후, 컨슈머가 메세지를 읽기 시작하기 전에 호출.
- 협력적 리밸런스 전략일 경우
	- 리밸런싱이 발생할 때마다 호출된다.
```java
void onPartitionsAssigned(Collection<TopicPartition> partitions);
```

## onPartitionsRevoked
- 리밸런스, 컨슈머의 종료 등의 이유로 컨슈머가 할당받았던 파티션이 할당 해제될 때 호출.
- 조급한 리밸런스 전략일 경우
	- 컨슈머가 메세지 읽기를 멈춘 뒤 리밸런스가 시작되기 전에 호출
- 협력적 리밸런스 전략일 경우
	- 리밸런스가 완료될 때, 컨슈머에서 할당 해제되어야 할 파티션들에 대해서만 호출되며, 이 때 offset을 커밋해주어야 이 파티션을 다음에 할당받는 컨슈머가 다시 읽기 시작할 지점을 알 수 있다.
```java
void onPartitionsRevoked(Collection<TopicPartition> partitions);
```

## onPartitionsLost

- 예외적인 리밸런스 상황에서 호출된다. 
- 이미 다른 컨슈머에게 할당된 파티션에 대한 리소스 정리 처리를 제공하기 위해 존재하는 메서드이다.
- 이 메서드를 구현하지 않았을 경우 `onPartitionsRevoked()` 가 대신 호출된다.

```java
default void onPartitionsLost(Collection<TopicPartition> partitions) {  
    onPartitionsRevoked(partitions);  
}
```
