---
title: "OffsetCommit  방법"
date: 2023-11-16 00:00:00 +0900
categories: [kafka, Kafka-핵심-가이드, chapter-4]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: kafka/Kafka-핵심-가이드/chapter-4/OffsetCommit  방법.md"
---
# 자동 commit

- 오프셋을 커밋하는 가장 쉬운 방법.
- polling loop에 의해서 실행된다. poll() 메서드를 실행할 때마다 컨슈머는 커밋해야하는지 확인하여 commit 수행.
- `enable.auto.commit` 설정으로 auto commit 설정을 켜고 끌 수 있다.
- 5초에 한번 poll() 을 통해 받은 메세지중 마지막 메세지의 offset을 커밋한다.
	- `auto.commit.interval.ms` 설정을 통해 commit 주기 설정 가능.
- 기본적으로 5초 마다 commit이 이루어지기 때문에 commit 이후 5초안에 컨슈머가 중지될 경우 읽었던 offset을 다시 읽어 메세지를 처리하게 된다.
- 편리하지만 중복 메세지를 방지하기엔 적합하지 않다.

# 직접 commit

- 메세지 유실의 가능성을 제어하고, 리밸런싱시에 발생되는 중복 메세지의 수를 줄이기 위해 사용한다.
- `enable.auto.commit=false` 설정을 통해 auto commit을 비활성화 할 수 있다.

## 1️⃣ commitSync() 동기 커밋

- 명시적으로 현재 offset을 커밋할 때 사용하는 API이다.
- offset을 커밋한 뒤 커밋이 성공적으로 완료되면 return, 실패하면 예외를 발생시킨다.
	- 해결할 수 없는 에러가 발생하지 않는한 commitSync()는 커밋이 실패할 경우 커밋을 재시도한다.

```java
while (true) {  
  
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));  
    for (ConsumerRecord<String, String> record : records) {  
        // ... do something  
    }  
  
    try {  
        consumer.commitSync(); 🚀
    } catch (CommitFailedException e) {  
        // ...  
    }  
}
```

- 모든 레코드들의 처리가 끝나면 offset을 커밋한다.
- 많은 레코드를 처리하는 도중 crash가 날 경우, 메세지를 중복 처리해야한다.
- 동기 커밋의 단점중 하나는 브로커가 커밋 요청에 응답할 때까지 Application이 block된다는 점이다.
	- Application 처리량이 매우 떨어질 수 있다.
	- 성능때문에한번에 처리하는 record 수를 많이 다뤄 commit 빈도수를 낮추자니 잠재적인 중복 메세지 수가 늘어난다.

## 2️⃣ commitAsync() 비동기 커밋

- 비동기 커밋은 브로커가 커밋에 응답할 때까지 기다리는게 아닌 요청만 보내고 처리를 계속한다.

```java
while (true) {  
  
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));  
    for (ConsumerRecord<String, String> record : records) {  
        // ... do something  
    }  
    consumer.commitAsync(); 🚀
}
```

- 마지막 offset을 커밋 하고 처리 작업을 계속진행한다.
- `commitSync()` 는 재시도 불가능한 실패가 발생할 때까지 커밋을 재시도 하는 반면, <mark style="background: #FF5582A6;">commitAsync()는 재시도를 하지 않는다</mark>.
- 비동기적으로 커밋을 진행하고 다음 작업을 계속 진행하다보니, 커밋을 재시도할 경우 offset 커밋 위치가 꼬이는 경우가 발생할 수 있어 재시도 프로세스가 없다. ✅
- ![[비동기커밋이-있다면.png]]
- 위 그림처럼 첫번째 커밋이 더 늦게 완료되는 경우 .. 다음 요청에서 컨슈머가 받는 데이터는 1000 offset 위치에 해당하는 데이터일 것이다.

### 💡 commitAsync(callback()) callback을 통해 재시도 구현 가능

- commitAsync() 메서드는 브로커가 보낸 응답을 받았을 때 호출되는 callback을 지정할 수 있다.
- callback은 커밋 에러를 logging하거나, 커밋 에러 수를 지표 형태로 집계하기 위해 사용되는 것이 보통이지만 재시도 프로세스를 직접 구현할수도 있다.

## 3️⃣ 동기, 비동기 커밋 함께 사용

- 컨슈머를 종료하기 전, 리밸런스 직전 마지막 커밋이라면 커밋의 성공여부를 추가로 확인할 필요가 있다.
- 일반적인 패턴으로는 종료 직전에 commitAsync(), commitSync() 를 함께 사용하는 것.

```java

try {  
    while (!closing) {  
  
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(10));  
        for (ConsumerRecord<String, String> record : records) {  
            // ... do something  
        }  
        consumer.commitAsync();
    }  
    consumer.commitSync();  
  
} catch (Exception e) {  
    // ...  
} finally {  
    consumer.close();  
}

```

- 정상적인 상황에서는 비동기커밋이 제 기능을 할것이다.
	- 커밋이 실패하더라도 다음 커밋이 성공할 경우 offset이 최신으로 업데이트 되니까
- 컨슈머를 닫는 상황에서는 다음 커밋이라는게 없으므로 commitSync()를 호출한다.
	- 커밋이 성공하거나 회복 불가능한 에러가 발생할 때까지 재시도를 진행한다.


# 특정 offset 커밋하기.

- poll() 메서드가엄청나게 큰 레코드를 리턴하는 경우 리밸런스가 발생하여, 전체 배치를 재처리 하는 상황을 피하기 위해 배치를 처리하는 도중에 offset을 커밋할 수 있다.
	- 🔴 이 경우 commitSync(), commitAsync() 를 호출할 수 없다.  아직 처리하지 않은 return된 마지막 `offset + 1` 위치의 offset을 commit하기 때문이다.
- offset 객체를 만들어 paramter로 전달하여 offset을 명시할 수 있다.
   ![[commitSync-offset.png]]
   ![[commitAsync-offset2.png]]
