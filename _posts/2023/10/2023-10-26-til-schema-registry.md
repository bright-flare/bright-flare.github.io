---
title: "Schema Registry"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Schema Registry.md"
---
# 개요

Avro 사용하여 CustomSerializer 사용시 데이터 형식에 변경이 있을 경우 발생하는 문제를 해결 가능하다.
하지만 Avro 파일은 파일안에 Schema를 저장함으로써 약간의 오버헤드가 생긴다.

# Schema Registry

Avro는 레코드를 읽을 때 스키마 전체를 필요로 한다.
Kafka 에서 Avro사용시 레코드 각각에 전체 스키마를 저장해야 하는데 그 경우 전체 레코드 사이즈는 2배 이상이 될 수 있다.

비효율적인 사이즈를 줄이기 위해 카프카에 데이터를 쓸 때 사용되는 모든 스키마를 스키마 레지스트리에 저장해 놓고, 카프카 레코드에는 사용된 스키마의 고유식별자만 심어주면, 

컨슈머는 이 식별자를 사용해서 스키마 레지스트리에서 스키마를 가져와서 데이터를 역직렬화 할 수 있다.

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

