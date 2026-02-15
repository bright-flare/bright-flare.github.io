---
title: "Partition"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Partition.md"
---
ProducerRecord 객체는 토픽, 키, 벨류 값을 포함한다.
이중 topic, value값만이 필수값이다. 대부분의 경우 key값이 지정된 record를 쓴다.

# key의 역할

key의 역할은  두가지가 있다.
1. value 값과 함께 저장되는 정보
2. 하나의 topic에 속한 여러개의 partition 중 message가 저장될 partition을 결정짓는 기준점 역할.
  1. 같은 key값을 같은 message는 같은 partition에 저장된다.



# key가 있을 때, 없을 때


## 없을 때
- 기본 파티셔너 사용중에 키값이 null인 record가 주어질 경우 ,  record는 현재 사용가능한 토픽의 파티션중 하나에 `랜덤`하게 저장된다. 각 파티션별로 저장되는 메세지 개수의 균형을 맞추기위해 round robin 알고리즘이 사용된다.
- sticky 처리에 대해서 더 찾아보기 ..............

## 있을 때
- 키 값이 지정된 상황에서 기본 파티셔너를 사용하는 경우, 키값을 hash한 결과를 기준으로 메세지를 저장할 파티션을 특정한다.
  - 파티셔너는 자체 hash 알고리즘을 사용하기때문에 java version이 달라져도 hash 값은 동일하다 !!
- 동일한 키값은 항상 동일한 파티션에 저장되는 것이 원칙이기때문에 파티션을 선택할 때에는 토픽의 모든 파티션을 대상으로 선택한다.
  - 단 !!! 파티션 수가 변하지 않는 한 변하지 않는다.
  - 토픽에 새로운 파티션을 추가하는 순간 hash값이 항상 같다고 보장할 순 없다.

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)
