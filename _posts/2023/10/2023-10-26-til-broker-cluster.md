---
title: "Broker와 Cluster"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Fundamentals]
tags: [Kafka]
description: "Kafka Broker/Cluster의 역할과 Zookeeper, MirrorMaker 운용 관점을 함께 정리합니다."
author: bright-flare
---
Kafka를 안정적으로 운영하려면 Producer/Consumer API보다 Broker와 Cluster 동작 원리를 먼저 이해해야 한다.

## Broker의 역할

Broker는 메시지를 받아 파티션 로그에 저장하고, Consumer fetch 요청에 응답한다.

핵심 책임은 세 가지다.

- 로그 append와 오프셋 관리
- 복제(replication) 동기화
- 보존 정책(retention)에 따른 데이터 관리

즉, Broker는 단순 전달자가 아니라 저장소와 분산 제어를 동시에 담당한다.

## Cluster와 파티션 리더십

Cluster는 여러 Broker로 구성된다. 각 파티션은 리더/팔로워 복제본을 가지며, Producer는 리더에 기록한다.

- 리더 장애 시 팔로워 승격으로 가용성 확보
- 파티션 분산으로 처리량 확장
- 복제 계수로 내구성 조절

운영 관점에서는 파티션 배치와 리더 편중 여부를 주기적으로 점검해야 한다.

## Zookeeper의 위치 (운영 배경)

기존 Kafka 운영에서는 Zookeeper가 메타데이터 관리와 브로커 조정에 사용되었다.

- 브로커 상태 관리
- 리더 선출 보조
- 클러스터 메타데이터 저장

버전별로 KRaft 전환이 진행되고 있으므로, 운영 환경에서는 "현재 클러스터가 Zookeeper 기반인지"를 먼저 확인해야 한다.

## 다중 클러스터 복제와 MirrorMaker

MirrorMaker는 클러스터 간 토픽 복제를 위한 도구다. 단일 클러스터 내부 복제와 달리, 리전 간 DR/이관 시나리오를 다룬다.

주요 사용 사례:

- 데이터센터 간 재해 복구
- 분석 클러스터 분리
- 단계적 마이그레이션

복제 정책은 토픽 선정, 지연 허용치, 순서 보장 요구사항을 기준으로 설계해야 한다.
