---
title: "polling loop 벗어나기"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "polling loop 벗어나기 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 왜 `wakeup()`이 필요한가

Consumer가 `poll()`에서 오래 대기 중이면 종료 신호를 받아도 즉시 루프를 빠져나오기 어렵다.  
이때 다른 스레드에서 `consumer.wakeup()`을 호출하면 poll 대기를 깨고 종료 흐름으로 진입할 수 있다.

## ShutdownHook과 함께 쓰는 패턴

```java
final Thread mainThread = Thread.currentThread();
Runtime.getRuntime().addShutdownHook(new Thread(() -> {
    consumer.wakeup();
    try {
        mainThread.join();
    } catch (InterruptedException e) {
        // ...
    }
}));

try {
    while (true) {
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));
        for (ConsumerRecord<String, String> record : records) {
            // ... do something
        }
        consumer.commitAsync();
    }
} finally {
    consumer.close();
}
```

핵심은 종료 이벤트가 왔을 때 polling을 즉시 멈추고, 커밋/리소스 정리 후 `close()`를 수행하는 것이다.

## 실무 포인트

- 강제 종료 대신 graceful shutdown을 구현하면 리밸런스 충격과 중복 처리를 줄일 수 있다.
- 종료 루틴에서도 마지막 커밋 전략(동기 커밋 여부)을 명확히 정해두는 것이 좋다.
