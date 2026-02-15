---
title: "Custom Serializer 문제"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Custom Serializer 문제.md"
---
# 변경에 취약하다.

```java
public class Customer {  

    Long id;  
    
    String name;  
  
    public Long getId() {  
        return id;  
    }  
  
    public String getName() {  
        return name;  
    }  
}

```

만약 Customer Type을 직렬화 해줄 수 있는 `CustomerSerializer` 를 만들었다고 생각해보자.
처음에는 `CustomerSerializer` 를 통해 직렬화한 메세지를 Producer를 통해 `send()` 했을 것이다.
하지만 비즈니스가 복잡해짐에따라 Customer에 startDate 필드를 추가해야 하는 경우, 구 버전과 신 버전 사이의 호환성을 유지해야 하는 심각한 문제를 안게된다.

## 대안

JSON, Apache Avro 등의 범용 라이브러리를 사용한다면 Message의 변경 가능성을 최소화 할 수 있다.


# Apache Avro 사용 이유

- 아파치 에이브로는 언어 중립적인 데이터 직렬화 형식이다. 
- 에이브로 데이터는 언어에 독립적인 스키마의 형태로 기술된다.
- 보통 JSON 형식으로 정의되며, 주어진 데이터를 스키마에 따라 직렬화 하면 이진 파일 형태로 결과물이 뽑혀 나오는 것이 보통이다.
- 메세지를 쓰는 Application이 새로운 스키마로 전환하더라도 기존 스키마와 호환성을 유지하는한, 데이터를 읽는 Application은 일체의 `변경`이나 `업데이트` 없이 `계속`해서 메세지를 처리할 수 있다.

```json
# 예전 스키마
{
  "namespace": "customer.avro",  
  "type": "record",  
  "name": "Customer",  
  "fields": [  
    {"name": "id", "type": "int"},  
    {"name": "name",  "type": "string"},  
    {"name": "faxNumber",  "type": ["null","string"], "default": null}  
  ]  
}
```

위와 같은 스키마의 경우 id, name 필드는 필수값이고, faxNumber는 선택사항이며 default 값이 null이다.
이 스키마의 version이 upgrade되어 faxNumber가 email 이라는 field로 바뀐다면

```json
# 새로운 스키마
{
  "namespace": "customer.avro",  
  "type": "record",  
  "name": "Customer",  
  "fields": [  
    {"name": "id", "type": "int"},  
    {"name": "name",  "type": "string"},  
    {"name": "email",  "type": ["null","string"], "default": null}  
  ]  
}
```

faxNumber를 가진 레코드를 처리하는 이전 버전의 application과 
email을 가진 레코드를 처리하는 새로운 버전의 application 모두 카프카에 저장된 이벤트를 처리할 수 있도록 해줘야 한다. (호환성을 보장해야한다.)


![[avro.png]]
데이터를 읽는 쪽 application을 전부 변경하지 않고 Schema를 변경하더라도 어떠한 예외나 에러가 발생하지 않으며, 기존 데이터를 새 Schema에 맞춰 업데이트하는 엄청난 작업을 할 필요도 없다.


## 주의사항

- 데이터를 쓸 때 사용하는 Schema와 읽을 때 기대하는 Schema가 호환되어야 한다. Avro 문서에 기술된 호환성 규칙을 참고하자.
- 역직렬화 할 때에는 데이터를 쓸 떄 사용했던 Schema에 접근이 가능해야한다.

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

