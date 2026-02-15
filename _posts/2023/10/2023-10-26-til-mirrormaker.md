---
title: "MirrorMaker"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/MirrorMaker.md"
---
> MirrorMaker란? 

MirrorMaker란 데이터를 다른 클러스터로 복제하는 데 사용되는 Tool 이다.
기본적으로 Kafka 프로젝트에 포함되어 있다.

카프카가 다수의 데이터센터에서 운용될 때에는 데이터센터 간에 메세지를 복제할 필요가 있다.
데이터센터간 데이터를 복제한다면 온라인 Application은 양쪽 데이터센터에서 모두 동일한 정보를 사용할 수 있다.

카프카 클러스터의 복제 메커니즘은 다중 클러스터 사이에서가 아닌 하나의 클러스터 안에서만 작동하도록 설계되어있다.

아래 이미지는 MirrorMaker를 사용하여 두개의 로컬 클러스터의 메세지를 하나의 집적 클러스터로 모은 뒤 다른 데이터센터로 복사하는 모습을 나타낸 것이다.
![[미러메이커.png]]

---

📚 **시리즈 목차:** [Kafka TIL 모음 (2023-10-26)](/posts/kafka-til-index/)

