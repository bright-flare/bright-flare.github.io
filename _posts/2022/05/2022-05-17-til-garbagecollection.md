---
title: "Garbagecollection"
date: 2022-05-17 00:00:00 +0900
categories: [Language, Java]
tags: [TIL]
description: "Garbagecollection의 핵심 개념과 실무 포인트를 정리한 학습 노트입니다."
author: bright-flare
---
## 한눈에 보기
- Garbage Collection
- Garbage Collector
- Stop the world상태의 **Garbage Collection** 실행자이다. **Garbage Collection**을 실질적으로 실행하는 주체.

## Garbage Collection

> **Garbage** **Collection은 JVM heap 메모리 공간에서 사용되지 않는 객체 메모리 할당을 해제하는 일을 말한다.**
>

### Garbage Collector

- Stop the world상태의 **Garbage Collection** 실행자이다. **Garbage Collection**을 실질적으로 실행하는 주체.

### `finalize()`

- 참조(메모리 할당)가 더이상 없는 객체에 대해 메모리 회수를 하기 전에 **Garbage Collector**가 호출하는 **method**이다. **method** 실행이 완료되면 **Garbage Collector**가 메모리를 회수한다.

### 개발자

- 개발자는 GC덕분에 사용되지 않는 메모리에 대해 신경쓰지 않아도 된다. 명시적으로 메모리 할당을 해제하지 않아도 된다.

### Garbage Collection이 발생하는 시점

- JVM의 Heap 영역이 가득 차서 더이상 메모리를 할당할 수 없는 경우 **Garbage Collector**가 호출된다.
- `System.gc()` method 호출을 통해 발생시킬 수 있다.

### Garbage Collection 기본 프로세스

- 기본 프로세스는 Mark와 Sweep이라는 두가지 단계로 작동한다. **(추가로 메모리를 압축하는 프로세스도 있다.)**
    1. Mark - GC가 사용중인 메모리와 사용되지 않는 메모리를 식별한다.
    2. Sweep - Mark 단계에서 식별된(사용되지 않는 메모리) 객체를 제거한다.

### Garbage Collection 종류

- **Minor GC** : **Young Generation**영역에서 일어난다. 새로 생성된 객체는 **Young Generation** Eden영역에 메모리가 할당된다. **Eden** 영역이 가득 차게 되면 **Minor GC**가 발생한다. **Minor GC**가 발생할때마다 **Eden**영역에서 **S0**영역으로 **S1**영역으로 옮겨진다. **Survior** 영역에서 옮겨지는 객체들은 **나이(age)**를 먹는데 **Minor GC**가 몇번이고 발생해도 계속해서 살아남은 객체들은 **나이**를 먹어 특정 **나이(age값)**에 도달하면 **Old Generation** 영역으로 옮겨진다.
- **Major GC : Old Generation** 영역이 가득차게 되면 **Major GC**가 발생한다. 일반적으로 **Minor GC**보다 시간이 오래걸린다.
- **Full GC : Heap 영역 전체**를 **GC**한다. **Young Generation, Old Generation** 전체를 **GC**한다.

### Stop the world

- Heap영역에 메모리가 가득 차면 **GC**가 일어난다. **GC**가 일어나면 **GC**를 담당하는 **Thread**를 제외한 모든 **Thread**가 일시적으로 멈추게 되는데 이를 **Stop the world**라고 한다.
- **Young Generation** 영역에서 발생하는 **Minor GC**의 경우 **GC**시간이 짧다.

### Heap 영역에 존재하지 않는 Primitive Type

**Heap 영역**에 존재하지 않는 **Primitive type**의 변수들은 **GC**의 대상이 아니다.

**Stack 메모리**에 할당되어있던 **Primitive type**의 값들은 **`method`**실행이 끝나면 순차적으로 **pop**되며 **Stack 메모리**에서 제거된다.

### Static

- 클래스 영역 또는 메소드 영역이라고 불리우는 영역에 존재하는 static 객체들은 Java 프로그램이 끝날 때 메모리에서 제거되기 때문에 GC 대상이 아니다.
