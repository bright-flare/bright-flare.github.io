---
title: "Application 설계 초기의 발행-구독 시스템 및 문제"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/Application 설계 초기의 발행-구독 시스템 및 문제.md"
---
## 초기
초기 발행/구독 패턴을 따르는 많은 사례들은 비슷한 형태로 시작한다.
간단한 Application에서 어떤 데이터를 사용한다고 했을 때 아래와 같이 API와 직접 연결하는 형태로 사용하게 된다.

point to point 방식
![[1-1.png]]

간단한 Application이므로 단순한 문제에 걸맞는 단순한 해법이 될 수 있다.

## 문제

장기적으로 Application의 기능, 비즈니스가 발전하는 과정에서 API 간의 복잡도가 증가한다.
![[1-2.png]]
지표라는 데이터를 생성하고, 사용하는 Application들이 복잡하게 얽혀 있다.
