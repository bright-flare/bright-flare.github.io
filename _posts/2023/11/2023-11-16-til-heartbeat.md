---
title: "Heartbeat"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Heartbeat 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Heartbeat란

Consumer는 백그라운드에서 주기적으로 heartbeat를 전송해 "나는 아직 살아 있고 이 파티션을 처리 중"임을 알린다.  
Coordinator는 이 신호를 기준으로 그룹 멤버 상태를 판단한다.

## heartbeat가 끊기면

일정 시간 동안 heartbeat가 도착하지 않으면 Coordinator는 해당 Consumer를 비정상으로 판단한다.  
그 결과 파티션 재할당을 위해 리밸런스가 발생한다.

## 실무 포인트

- heartbeat 간격을 너무 짧게 잡으면 불필요한 오버헤드가 늘 수 있다.
- 너무 길게 잡으면 장애 감지가 느려진다.
- poll 지연과 heartbeat 실패가 함께 발생하지 않도록 처리 구조를 분리해야 한다.
