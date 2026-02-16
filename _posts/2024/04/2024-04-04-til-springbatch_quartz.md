---
title: "Springbatch Quartz"
date: 2024-04-04 00:00:00 +0900
categories: [SpringBatch]
tags: [TIL]
description: "Springbatch Quartz의 핵심 개념과 실무 포인트를 정리한 학습 노트입니다."
author: bright-flare
---
## 한눈에 보기
- Spring Batch, Quartz에 대해서 나의 생각 정리하기.
- 배치란 무엇인가 ?
- 한번에 큰 작업을 사람의 상호작용이 아닌 어떤 프로그램의 작업을 통해서 진행한다.

## Spring Batch, Quartz에 대해서 나의 생각 정리하기.

이 글을 읽는 독자 : 나와 같은 ... 주니어

- Quartz Shedule Job에 배치성격의 비즈니스로직을 넣어서 작업을 몇번 해본적이 있는데, 그 때 Spring Batch와 Quartz는 무슨 차이인가 생각했었다.

## 배치란 무엇인가 ?
- 한번에 큰 작업을 사람의 상호작용이 아닌 어떤 프로그램의 작업을 통해서 진행한다.
- Batch는 영어로 무엇인가 → 일괄. 한국말로 일괄 작업을 배치작업이라고 말할 수 있다.
- ![batch뜻.png](/assets/img/til/batch뜻.png)

- 최근에 3일정도 배치작업 했는데, Spring batch 세팅해서 Job 만들었다.

- spring batch Application 실행시에 java argument로 job의 name을 넘겨서 Job을 실행할 수 있도록 세팅함.

- Intellij 에서 java argument 넘겨서 해당 job만 실행시키기.

```java
-Dspring.batch.job.names=sampleJob
```

- 그리고 AWS Eventbridge 라는 일종의 이벤트 발생 트리거를 이용해서 1분 주기로 sampleJob 배치를 동작시키도록 Schedule 세팅 완료.
- Eventbridge를 통해 1분주기로 sampleJob을 실행할 수 있도록 작업을 마무리하였다.
- ECR이미지를 통해 AWS Batch에서 배치 작업을 위한 1회성 container를 구동시켜 배치작업을 마무리하고 Application 구동이 마무리되는 형식.

## Batch와 Quartz는 무슨 차이인가.

- AWS Eventbridge로 1분마다 실행될 수 있도록 스케줄링한다.
- Spring batch로 Job (배치) 작업 비즈니스로직을 짜두어 1분마다 배치가 실행되게 한다.
- AWS Eventbridge는 이벤트발생 도구이자 트리거이다.
- 이벤트 브릿지를 대체할 수 있는 트리거는 무엇인가 ?
    1. 리눅스 Shell script
    2. Apache Quartz

- 스케줄 기능과 배치를 분리해서 생각해야 이해가 편하다.
