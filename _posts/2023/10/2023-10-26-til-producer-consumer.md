---
title: "Producer, Consumer"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Fundamentals]
tags: [Kafka]
description: "Producer, Consumer 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## Producer

Producer는 애플리케이션에서 발생한 이벤트를 Kafka에 쓰는 역할을 한다.  
기본적으로 토픽을 지정해 메시지를 전송하고, 필요하면 key를 함께 전달해 파티션 분산 규칙을 제어할 수 있다.

특정 key를 사용하면 같은 key 값은 같은 파티션으로 라우팅되기 때문에, 같은 엔티티 단위의 순서를 유지하는 데 유리하다.

## Consumer

Consumer는 토픽에 저장된 메시지를 읽어 실제 비즈니스 처리를 수행한다.  
중요한 점은 Kafka가 push 방식이 아니라 pull 방식이라는 것이다. Consumer가 직접 데이터를 요청해 가져온다.

읽기 위치는 오프셋(offset)으로 관리한다.  
오프셋 덕분에 장애 후 재시작 시 마지막 처리 지점부터 다시 이어갈 수 있고, 필요하면 과거 데이터 재처리도 가능하다.

## Offset

오프셋은 파티션 내 메시지의 위치를 나타내는 증가하는 숫자다.  
같은 파티션 안에서는 메시지 순서와 오프셋 순서가 일치한다.

실무에서는 "어디까지 처리했는가"를 오프셋 커밋으로 관리하므로, 커밋 타이밍은 중복 처리/유실 가능성과 직결된다.

## Consumer Group

Consumer Group은 하나의 토픽을 여러 Consumer 인스턴스가 협업해 읽기 위한 단위다.
- 같은 그룹 내에서는 파티션이 Consumer 간에 분배된다.
- 하나의 파티션은 같은 시점에 하나의 Consumer만 담당한다.
- 특정 Consumer 장애 시 다른 Consumer가 파티션을 재할당 받아 이어서 처리한다.

이 구조를 통해 처리량 확장과 장애 복구를 동시에 얻을 수 있다.
