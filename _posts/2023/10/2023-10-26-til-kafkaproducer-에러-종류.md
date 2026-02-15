---
title: "KafkaProducer 에러 종류"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/KafkaProducer 에러 종류.md"
---
# 개요

KafkaProducer에는 두 종류의 에러가 있다.
1. 재시도 가능한 에러
2. 재시도가 없는 에러


# 재시도 가능한 에러

메세지를 다시 전송함으로써 해결 될 수 있는 에러를 말한다.
예를 들어 연결에러가 있다. 연결 에러는 연결이 회복되면 해결될 수 있다.
메세지를 전송받은 broker가 해당 파티션의 리더가 아닐경우 발생하는 에러는 해당 파티션에 새 리더가 선출되고 클라이언트 메타데이터가 업데이트되면 해결될 수 있다.


# 재시도 없는 에러 

메세지를 다시 전송해도 해결될 수 없는 에러를 말한다.
예를 들어 전송한 메세지의 크기가 너무 클 경우가 한 예다.
이러한 경우 몇번을 재시도 해도 message size에는 변함이 없으니 재시도 없이 바로 예외를 발생시킨다.

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

