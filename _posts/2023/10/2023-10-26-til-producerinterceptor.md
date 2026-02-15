---
title: "ProducerInterceptor"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/ProducerInterceptor.md"
---
kafka 클라이언트의 코드를 고치지 않으면서 그 작동을 변경해야 하는 경우에 사용한다.


```java
public class CustomerInterceptore implements ProducerInterceptor {  
  
    @Override  
    public ProducerRecord onSend(ProducerRecord record) {  
       ...
    }  
  
    @Override  
    public void onAcknowledgement(RecordMetadata metadata, Exception exception){        ...
  
    }  
    @Override  
    public void close() {  
    ...
    }  
    @Override  
    public void configure(Map<String, ?> configs) {  
    ...
    }}
```

## onSend()

- 프로듀서가 레코드를 broker에게 보내기전, 직렬화 되기 직전에 호출된다.
- 재정의할 때에는 보내질 레코드에 담긴 정보를 read, modify 할 수 있다.

## onAcknowledgement()

- broker가 보낸 응답을 받았을 때 호출된다.
- broker가 보낸 응답을 변경할 수는 없다.정보를 read만 가능하다.

## 사용 사례

- 모니터링
- 정보추적
- 표준 헤더 삽입
- 개인정보 삭제

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)
