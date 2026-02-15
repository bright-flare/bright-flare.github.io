---
title: "Kafka 설치 및 실행하기 for macos"
date: 2023-10-26 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-1-2]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-1-2/Kafka 설치 및 실행하기 for macos.md"
---
> Java 8 또는 11버전 설치 선행되어야 합니다.
> Homebrew 설치가 선행되어야 합니다.
> Homebrew로 설치한 kafka directory를 찾아가서 아래 command들을 실행해야함.



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


### Producer로 토픽에 메세지 작성

```
$ kafka-console-producer --bootstrap-server localhost:9092 --topic test
> create test message
> create test meaaage 2
```

### Consumer로 토픽 메세지 읽기

```
$ kafka-console-consumer --bootstrap-server localhost:9092 --topic test --from-beginning


# print 메세지 출력

create test message
create test meaaage 2
```
