---
title: "Partition Rebalance"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Partition Rebalance 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Partition Rebalance란

리밸런스는 Consumer Group 내 파티션 소유권을 재분배하는 절차다.  
새 Consumer가 들어오거나 기존 Consumer가 이탈하면 현재 할당 상태를 다시 계산해야 하므로 리밸런스가 실행된다.

대표적인 발생 조건은 다음과 같다.
- Consumer 인스턴스 추가/종료
- Consumer 장애 및 세션 타임아웃
- 구독 토픽 파티션 수 변경

## 리밸런스 전략

### Eager Rebalance

Eager 방식은 모든 Consumer가 일단 파티션 소유권을 내려놓고 다시 할당받는다.  
구현이 단순하지만 재할당 순간 전체 처리 정지 구간이 커질 수 있다.

### Cooperative Rebalance

Cooperative 방식은 필요한 파티션만 점진적으로 이동시킨다.  
전체 중단 구간을 줄일 수 있어 Consumer 수가 많은 환경에서 유리하다.

## Group Coordinator와 Heartbeat

Consumer Group 상태는 Group Coordinator가 관리한다.  
각 Consumer는 주기적으로 heartbeat를 보내 생존과 소유권을 유지한다.

- heartbeat가 끊기면 Coordinator는 해당 Consumer를 이탈로 간주한다.
- 이때 파티션 재할당을 위해 리밸런스가 트리거된다.

즉 heartbeat 설정은 장애 감지 속도와 불필요한 리밸런스 빈도 사이 균형을 맞추는 파라미터다.

## 실무 운영 포인트

- 배포/스케일 이벤트가 잦으면 리밸런스 비용이 누적된다.
- 리밸런스 동안 처리 공백을 줄이려면 cooperative 전략과 graceful shutdown을 함께 고려해야 한다.
- poll 주기, session timeout, max poll interval 등 타이밍 파라미터를 서비스 특성에 맞게 튜닝해야 한다.
