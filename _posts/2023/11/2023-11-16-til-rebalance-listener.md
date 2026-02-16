---
title: "Rebalance Listener"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Rebalance Listener 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Listener를 왜 쓰는가

리밸런스는 파티션 소유권이 바뀌는 이벤트다.  
이 시점에 상태 저장, 커밋, 캐시 정리 같은 후처리를 하지 않으면 중복 처리나 상태 불일치가 발생할 수 있다.

## 주요 콜백

### `onPartitionsAssigned()`

```java
void onPartitionsAssigned(Collection<TopicPartition> partitions);
```

- 파티션 할당 완료 후, 실제 읽기 시작 직전에 호출된다.
- 초기화 로직(로컬 상태 준비, 시작 오프셋 확인)에 적합하다.

### `onPartitionsRevoked()`

```java
void onPartitionsRevoked(Collection<TopicPartition> partitions);
```

- 현재 파티션 소유권이 회수되기 전에 호출된다.
- 보통 여기서 마지막 오프셋 커밋/상태 저장을 수행한다.

### `onPartitionsLost()`

```java
default void onPartitionsLost(Collection<TopicPartition> partitions) {
    onPartitionsRevoked(partitions);
}
```

- 예외적 상황에서 이미 소유권을 잃은 파티션 정리를 위한 콜백이다.

## 실무 포인트

- 리밸런스 콜백은 빠르고 실패에 강하게 작성해야 한다.
- 콜백 안에서 외부 의존 호출이 길어지면 전체 소비 지연으로 이어질 수 있다.
