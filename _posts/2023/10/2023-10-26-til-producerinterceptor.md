---
title: "Producerinterceptor"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Producerinterceptor 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## onSend()

- 프로듀서가 레코드를 broker에게 보내기전, 직렬화 되기 직전에 호출된다.
- 재정의할 때에는 보내질 레코드에 담긴 정보를 read, modify 할 수 있다.

## onAcknowledgement()

- broker가 보낸 응답을 받았을 때 호출된다.
- broker가 보낸 응답을 변경할 수는 없다.정보를 read만 가능하다.

## 사용 사례

- 모니터링
- 정보추적
- 표준 헤더 삽입
- 개인정보 삭제
