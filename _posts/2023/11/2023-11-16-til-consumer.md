---
title: "Consumer"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Consumer 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Consumer의 역할

Kafka Consumer는 토픽을 구독하고 파티션 데이터를 읽어 비즈니스 로직을 수행한다.  
다른 메시징 시스템의 subscriber/reader에 해당하는 역할이다.

Kafka는 push가 아니라 pull 모델이므로, Consumer가 직접 poll 요청으로 레코드를 가져온다.  
이 구조는 소비 속도를 애플리케이션이 제어하기 쉽다는 장점이 있다.

## Offset이 중요한 이유

Offset은 파티션 내 메시지의 위치를 나타낸다.  
Consumer는 "어디까지 처리했는가"를 오프셋으로 기록하고, 재시작 시 그 지점 이후부터 다시 처리한다.

이 메커니즘 덕분에 다음이 가능하다.
- 장애 후 복구
- 중단 지점부터 재처리
- 소비 지연(lag) 측정

## Consumer Group으로 확장하기

Consumer Group은 같은 토픽을 병렬 처리하기 위한 기본 단위다.  
같은 그룹 내에서는 파티션이 분배되어 처리되고, 한 Consumer 장애 시 다른 Consumer가 파티션을 재할당받아 이어서 처리한다.

다만 중요한 제약이 있다.
- 그룹 내 동시 활성 Consumer 수는 파티션 수를 초과해도 실제 병렬성은 늘지 않는다.
- 즉, 확장 계획은 "Consumer 수"가 아니라 "파티션 수"와 함께 설계해야 한다.

## 운영 관점에서 자주 보는 상황

- **처리 지연 증가**: Producer 유입량이 Consumer 처리량을 넘으면 lag가 누적된다.
- **재시작/배포 이벤트**: 리밸런스가 잦으면 일시적 처리 공백이 늘어난다.
- **용도별 소비 분리**: 같은 토픽을 여러 Consumer Group이 각각 독립적으로 소비할 수 있다.

이 구조를 잘 활용하면, 하나의 이벤트 스트림을 분석/알림/적재 등 여러 목적에 동시에 재사용할 수 있다.
