---
title: "Kafka 설치 및 실행하기 for macOS"
date: 2023-10-26 00:00:00 +0900
categories: [Kafka, Fundamentals]
tags: [Kafka]
description: "Kafka 설치 및 실행하기 for macOS 주제의 핵심 개념과 적용 포인트를 정리합니다."
author: bright-flare
---
## homebrew로 kafka 설치
```
$ brew install kafka
```

## zookeeper 서버 실행
```
$ sh /opt/homebrew/opt/zookeeper/bin/zkServer start
```

## 주키퍼 정보 확인
### zookeeper 서버 실행 여부 확인
```
$ sudo lsof -iTCP -sTCP:LISTEN -n -P | grep 2181
```

### zookeeper 접속 2181 port

```
$ telnet localhost 2181
```

### zookeeper 서버 정보 확인

```
$ srvr
```

### 브로커 서버 실행

```
$ cd /opt/homebrew/Cellar/kafka/3.5.1/bin
```

```
$ sh kafka-server-start -daemon ../../3.5.1/.bottle/etc/kafka/server.properties
```

### 토픽 생성

```
$ sh kafka-topics --bootstrap-server localhost:9092 --create --replication-factor 1 --partitions 1 --topic test Created topic "test".
```

### Producer로 토픽에 메시지 작성

```
$ kafka-console-producer --bootstrap-server localhost:9092 --topic test
> create test message
> create test meaaage 2
```

### Consumer로 토픽 메시지 읽기

```
$ kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning

# print 메시지 출력

create test message
create test meaaage 2
```
