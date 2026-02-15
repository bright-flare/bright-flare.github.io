---
title: "Kafka broker의 쿼터, 스로틀링"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Kafka broker의 쿼터, 스로틀링.md"
---
카프카 브로커에는 read/write 속도를 제한할 수 있는 기능이 있다. 한도 (quota)를 설정해 주면 된다.


# 쿼터 종류

1. 쓰기 쿼터 (produce quota)
	1. 데이터를 전송하거나 받는 속도를 초방 바이트 수 단위로 제한.
2. 읽기 쿼터 (consume quota)
	1. 데이터를 전송하거나 받는 속도를 초방 바이트 수 단위로 제한.
3. 요청 쿼터 (request quota)
	1. 브로커가 요청을 처리하는 시간 비율 단위로 제한

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

