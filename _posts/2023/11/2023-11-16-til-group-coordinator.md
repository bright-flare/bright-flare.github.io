---
title: "Group Coordinator"
date: 2023-11-16 00:00:00 +0900
categories: [Kafka, Consumer]
tags: [TIL]
description: "Group Coordinator 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Group Coordinator란

각 Consumer Group에는 상태를 관리하는 Coordinator가 존재한다.  
Coordinator는 그룹 멤버십과 파티션 할당 흐름을 관리하며, 리밸런스의 시작점 역할도 담당한다.

## 주요 역할

- 그룹 멤버 가입/이탈 관리
- 하트비트 수신 및 생존 판단
- 토픽/파티션 변경 감지에 따른 리밸런스 트리거
- 그룹 상태 변경 사항 전파

즉, Coordinator는 "그룹 제어 플레인"에 가까운 역할이다.

## 실무 포인트

- 리밸런스가 자주 발생하면 Coordinator 부하와 소비 지연이 함께 증가한다.
- heartbeat/session timeout/max poll interval 설정은 Coordinator 관점에서 함께 튜닝해야 한다.
