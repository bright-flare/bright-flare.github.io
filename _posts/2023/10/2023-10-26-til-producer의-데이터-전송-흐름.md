---
title: "Producer의 데이터 전송 흐름"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Kafka Producer의 직렬화, 파티셔닝, 배치 전송 흐름과 튜닝 포인트를 정리합니다."
author: bright-flare
---
Producer의 `send()`는 단순한 API 호출처럼 보이지만, 내부에서는 직렬화와 배치, 비동기 전송 파이프라인이 연쇄적으로 동작한다.

## 전송 파이프라인

Producer 전송은 보통 아래 순서로 진행된다.

1. 레코드 생성 (`topic`, `key`, `value`, `headers`)
2. Serializer로 byte 배열 변환
3. Partitioner가 대상 파티션 결정
4. RecordAccumulator에 배치 적재
5. Sender 스레드가 브로커로 비동기 전송
6. ACK 응답 후 콜백 실행

이 흐름을 이해하면 레이턴시와 처리량 이슈를 어디서 봐야 하는지 명확해진다.

## Header를 언제 쓰는가

Header는 메시지 본문을 바꾸지 않고 메타데이터를 함께 전달할 때 사용한다.

예시:

- trace id, request id
- schema version
- producer application/version

본문 스키마 변경 없이 라우팅/관찰성 정보를 붙일 수 있어 운영에서 유용하다.

## Batch가 성능에 미치는 영향

Kafka는 레코드를 배치 단위로 묶어 전송해 네트워크 오버헤드를 줄인다. 다만 배치가 커질수록 처리량은 좋아질 수 있지만 개별 메시지 지연은 늘어난다.

핵심 옵션:

- `batch.size`: 배치 최대 크기
- `linger.ms`: 배치를 모으는 대기 시간
- `compression.type`: 네트워크 사용량과 CPU의 트레이드오프

튜닝은 단일 옵션이 아니라 세 옵션 조합으로 보는 것이 정확하다.

## 운영에서 자주 보는 병목

- 특정 key 편중으로 인한 hot partition
- 콜백 로직 과부하로 인한 Sender 지연
- 과도한 retry로 인한 지연 전파

Producer 메트릭(전송 지연, 재시도율, 에러율)과 브로커 메트릭을 같이 봐야 원인을 빠르게 좁힐 수 있다.
