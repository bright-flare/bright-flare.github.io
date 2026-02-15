---
title: "Custom Serializer"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Custom Serializer.md"
---
# 개요
Producer를 설정할 때에는 반드시 serializer를 지정해주어야 한다.
기본 Serializer인 `StringSerializer`, `IntegerSerializer` 이외에 다른 Serializer를 알아본다.


# Custom Serializer

크게 두가지 방법으로 커스텀 serializer를 사용한다. 

1. Serializer를 구현하는 Custom serializer를 만든다.
2. serializer 라이브러리를 사용한다.
	1. apache avro 아파치 에이브로
	2. Thrift 스리프트
	3. Protobuf 프로토버프



##  Custom Serializer 만들기

- Serializer를 구현하는 Customer Serializer를 만든다.
- ProducerRecord를 생성할 떄 CustomerSerializer를 지정할 수 있다.
```java
public class CustomerSerializer implements Serializer<Customer> {  
  
    @Override  
    public void configure(Map<String, ?> configs, boolean isKey) {  
    }  
  
    @Override  
    public byte[] serialize(String topic, Customer customer) {  
        // ... customer to byte  
    }  
  
    @Override  
    public void close() {  
    }  
}
```
