---
title: "Offset Commit"
date: 2023-11-16 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-4]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-4/Offset Commit.md"
---
💡 컨슈머가 polling할 때, 카프카에 쓰여진 메세지 중에서 컨슈머 그룹에 속한 컨슈머들이 아직 읽지 않은 Record가 return된다.

💡 컨슈머는 어떻게 읽지 않은 레코드를 요청해서 읽어들일 수 있을까 ?
# offset commit

- 컨슈머는 파티션에서 성공적으로 읽어들인 마지막 메세지를 커밋함으로써 현재 위치를 업데이트 한다. 이 작업을 `offset commit` 이라고 한다.
- 컨슈머 그룹에서 리밸런싱이 발생하는 경우 컨슈머들은 리밸런스 이전에 할당받은 파티션과 다른 파티션을 할당받을 수 있다. 하지만 각 파티션으로부터 마지막으로 commit된 offset 메타데이터 정보를 통해 작업을 이어나갈 수 있다.
- 컨슈머는 카프카에 특수 토픽인 `__cunsumer_offsets` 토픽에 각 파티션별로 오프셋을 업데이트하도록 하는 메세지를 보낸다.

## offset commit 위치

- poll() 메소드가 return한 마지막 offset 바로 다음 offset을 commit 한다.

![[offset commit.png]]
