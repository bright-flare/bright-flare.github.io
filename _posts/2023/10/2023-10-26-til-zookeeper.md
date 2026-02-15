---
title: "Zookeeper"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/Zookeeper.md"
---
> 주키퍼는설정정보관리, 이름부여, 분산동기화, 그룹서비스 를 제공하는 중앙화된 서비스이다.

아파치 카프카는 카프카 클러스터의 메타데이터와 컨슈머 클라이언트에 대한 정보를 저장하기 위해 아파치주키퍼를사용한다.

주키퍼의 아키텍처는 중복 서비스를 이용한 고가용성을 제공한다. 클라이언트는 주키퍼 마스터가 응답을 하지 않으면 다른 주키퍼 마스터에게 요청을 한다.

![[zookeeper.png]]
