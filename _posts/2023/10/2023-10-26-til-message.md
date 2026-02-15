---
title: "Message"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/Message.md"
---
> Kafka의 message, 메세지란 ?

- 카프카에서 데이터의 기본 단위.
- 메세지에 포함된 데이터에는 특정한 형식이나 의미가 없음.
- key 라고 불리는 meta data를 포함할 수도 있다.
	- key 역시 kafka 입장에서는 특별한 의미가 없는 byte 배열일 뿐이다.
	- key는 메세지를 저장할 파티션을 결정하기 위해 사용된다.
	- 같은 key값은 가진 메세지는 `파티션 수가 변하지 않는 한` 항상 같은 파티션에 저장된다.

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

