---
title: "Producer, Consumer"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/Producer, Consumer.md"
---
> Producer (프로듀서)란?
> Kafka Client는 기본적으로 프로듀서, 컨슈머 두 종류가 있다.
> 고급 Client API인 Kafka Connect, Kafka Streams등이 있다.



## Producer

- 프로듀서는 새로운 메세지를 `생성`한다.  다른 발행/구독 시스템에서는 publisher 또는 writer라고도 부른다.
- 메세지를 쓸 때 토픽에 속한 파티션들 사이에 고르게 나눠서 쓰도록 되어있다.
- 특정한 파티션을 지정해서 메세지를 쓸 수도 있다.
  - key와 hash를 특정 partition으로 대응시켜주는 partitioner를 사용해서 구현할 수 있다.
  - partitioner를 구현하면, 동일한 key 값을 가진 모든 메세지는 같은 파티션에 저장된다.

## Consumer 

- 컨슈머는 메세지를 읽는다. 다른 발행/구독 시스템에서는 subscriber 또는 reader라고도 한다.
- 컨슈머는 1개 이상의 토픽을 구독해서 저장된 메세지들을 각 파티션에 쓰여진 `순서대로` 읽어온다.
- 메세지의 offset을 기록하여 어느 메세지까지 읽었는지 기록해둔다.
- 컨슈머는 offset을 통해 읽기 작업을 정지했다가 다시 시작하더라도 마지막으로 읽었던 메세지의 바로 다음 메세지부터 읽을 수 있다. 

## offset

- offset은 카프카가 메세지를 저장할 떄 각각의 메세지에 부여해주는 meta data 이다.
- 파티션의 각 메세지는 고유한 offset을 가진다.
- 지속적으로 증가하는 정수값이다.
- 뒤에 오는 메세지일수록 offset이 크다.

## Consumer group


- 컨슈머는 컨슈머 그룹의 일원으로서 작동한다.
- 컨슈머 그룹은 토픽에 저장된 데이터를 읽어오기 위해 협업하는 하나 이상의 컨슈머로 이루어진다.
- 컨슈머에서 파티션으로의 대응 관계는 컨슈머의 파티션 소유권(ownership)이라고도 부른다.
- 컨슈머 그룹에 속한 컨슈머 중 하나에 장애가 발생하더라도 그룹 안의 다른 컨슈머들이 장애가 발생한 컨슈머가 읽고 있던 파티션을 재할당 받은 뒤 이어서 데이터를 읽을 수 있다.


컨슈머 그룹에 속한 3개의 컨슈머가 하나의 토픽에서 데이터를 읽어오는 모습

> 🖼️ 원본 노트 이미지: `컨슈머그룹.png` (블로그 자산 경로 확인 필요)


---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)
