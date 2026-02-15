---
title: "Header"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-3]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-3/Header.md"
---
- 레코드는 key, value 이외에도 header를 포함할 수 있다. 
- header는 kafka 레코드의 key, value 값을 건드리지 않고 추가 meta data를 심을 때 사용한다.
	- 주된 용도중 하나는 메세지의 전달 내역을 기록하는 logging 용도 이다. 
- header는 순서가 있는 key/value 쌍의 집합으로 구현되어 있다.
