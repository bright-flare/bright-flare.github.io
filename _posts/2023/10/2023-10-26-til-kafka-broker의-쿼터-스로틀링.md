---
title: "Kafka broker의 쿼터, 스로틀링"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Producer]
tags: [Kafka]
description: "Kafka broker의 쿼터, 스로틀링 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## 쿼터 종류

1. 쓰기 쿼터 (produce quota)
  1. 데이터를 전송하거나 받는 속도를 초방 바이트 수 단위로 제한.
2. 읽기 쿼터 (consume quota)
  1. 데이터를 전송하거나 받는 속도를 초방 바이트 수 단위로 제한.
3. 요청 쿼터 (request quota)
  1. 브로커가 요청을 처리하는 시간 비율 단위로 제한
