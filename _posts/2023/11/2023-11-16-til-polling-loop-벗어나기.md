---
title: "polling loop 벗어나기"
date: 2023-11-16 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-4]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-4/polling loop 벗어나기.md"
---
컨슈머를 종료하고자 할 때, 컨슈머가 poll()을 오랫동안 기다리고 있더라도 즉시 루프를 탈출하고 싶다면 
다른 Thread에서 `consumer.wakeup()` 을 호출해 주어야 한다.

만약 main 스레드에서 컨슈머 루프를 돌고 있다면 `ShutdownHook`을 사용할 수 있다.

여기서 말하는 ShutdownHook 이란 ? 
- ShutdownHook은 JVM이 shutdown 될 때 종료 이벤트를 잡아주는 hook 입니다.
- 초기화는 되었지만 시작되지 않은 Thread.

컨슈머를 닫으면 offset을 커밋하고 그룹 코디네이터에게 컨슈머가 그룹을 떠난다는 메세지를 전송한다.
이 때 컨슈머 코디네이터는 즉시 리밸런싱을 싱핸한다.

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
} catch (Exception e) {  
    // ...  
} finally {  
    consumer.close();  
}

```

---

📚 **시리즈 목차:** [Kafka Consumer TIL 모음 (2023-11-16)](/posts/kafka-consumer-til-index/)

