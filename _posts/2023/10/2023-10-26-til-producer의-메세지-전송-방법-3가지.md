---
title: "Producer의 메세지 전송 방법 3가지"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Producer의 메세지 전송 방법 3가지.md"
---
# Fire and forget 던지고 잊어버리기



```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");  
try {  
    kafkaProducer.send(record);  
} catch (Exception e) {  
    e.printStackTrace();  
}
```


메세지를 서버에 전송만 하고 성공, 실패 여부에는 신경쓰지 않는다.
전송에 실패한 메세지는 재전송을 시도하기 때문에 대부분의 메세지는 성공적으로 전달된다.

하지만, 재시도 할 수 없는 에러가 발생하거나 Timeout이 발생한 경우 메세지는 유실되며,
`send()` 메서드에 callback() 메서드를 전달하지 않기 때문에 Application 실패 case에 대한 아무런 정보나 예외를 전달받지 못함..


# Synchronous send 동기 전송

```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");  
try {  
	//recordMetadata 활용
    RecordMetadata recordMetadata = kafkaProducer.send(record).get();    
} catch (Exception e) {  
    e.printStackTrace();  
}
```

- 응답이 올 때까지 대기하기위해 `Future.get()` 메서드를 사용한다.
	- 에러가 발생하지 않을 경우 `RecordMetadata` 객체를 return한다.
	- 에러가 발생할 경우 예외를 발생시킨다.
- 동기적으로 메세지를 전송할 경우 전송을 요청하는 스레드는 브로커가 쓰기 요청에 응답하기까지 걸리는 2ms ~ 최대 몇초 까지 기다려야 한다.
- 메세지를 전송하는 동안 다른 메세지를 전송할 수도 없다.
- 처리량이 낮아진다 -> 성능이 낮아진다.
- 실제로 사용되는 Application에서는 잘 사용되지 않는다.


# 👍 Asynchronous send  비동기 전송

```java
ProducerRecord<String, String> record = new ProducerRecord<>("topic", "key", "value");  
kafkaProducer.send(record, (recordMetadata, e) -> {  
    if (e != null) {  
        e.printStackTrace();  
    }  
    // do something
});
```

- callback 함수와 함께 send() 메서드를 호출하면 카프카 브로커로부터 응답을 받는 시점에서 자동으로 callback 함수가 호출된다.
- 메세지를 비동기적으로 전송하고 error를 처리하는 경우를 위해 producer는 record를 전송할 떄 callback을 지정할 수 있도록 한다.
- callback을 사용하려면 `org.apache.kafka.clients.producer.Callback` interface를 구현하는 Class가 필요하다.
	- 간단한 함수라면 lambda 표현식으로 처리해도 될듯

💡💡 callback은 Producer의 main thread에서 실행된다. 그렇기때문에 callback 안에서 blocking 작업을 수행하는 것은 권장되지 않는다. 대신 blocking 작업을 동시에 수행하는 다른 thread를 사용하도록 하자.
그렇지 않으면 Producer가 지연될 수 있다 !!

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

