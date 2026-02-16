---
title: "Spring Boot With Kotlin Plugin"
date: 2025-03-07 00:00:00 +0900
categories: [SpringBoot]
tags: [TIL]
description: "Spring Boot With Kotlin Plugin의 핵심 개념과 실무 포인트를 정리한 학습 노트입니다."
author: bright-flare
---
## 한눈에 보기
- Kotlin + Spring을 사용할 때, 플러그인, 의존성에 대하여 정리.
- Plugin
- Dependency

## Kotlin + Spring을 사용할 때, 플러그인, 의존성에 대하여 정리.

## 목차
- Plugin
- Dependency
- 기타

이 문서는 Gradle 기준으로 설명합니다.

## Plugin

### 1. id 'org.jetbrains.kotlin.jvm'

org.jetbrains.kotlin.jvm 플러그인은 Kotlin 코드를 JVM에서 실행할 수 있는 형태인 .class 파일로 변환하는 역할을 한다.

### 2. id 'org.jetbrains.kotlin.plugin.spring'

@Component, @Transactional, @Async 등 스프링 어노테이션을 사용하는 Class에 대하여 open 시켜주는 플러그인.

코틀린은 상속 가능한 Class에 대하여 open 키워드로 명시해주어야 하기 때문.

이 플러그인을 사용하지 않으면 CGLIB Proxy 생성이 불가능하다. (CGLIB Proxy는 상속하여 만들어진다.)

### 3. id 'org.jetbrains.kotlin.plugin.jpa'

Entity Class는 반드시 기본 생성자가 필요하다. 리플렉션할 때 기본 생성자를 활용하기 때문이다.

그런데 Kotlin의 클래스는 명시적으로 생성하지 않는한 기본 생성자가 없으므로 인해 발생하는 문제를 해결해주는 플러그인.

### 4. id 'org.jetbrains.kotlin.plugin.allopen'

```groovy
allOpen {
    annotation("javax.persistence.Entity")
    annotation("javax.persistence.Embeddable")
    ... 등 생략
}
```
1번과 같은 이유로 Jpa 프록시 객체 생성을 위해 Class open 시켜주는 플러그인.

## Dependency

### 1. implementation 'org.jetbrains.kotlin:kotlin-stdlib-jdk8'

JDK 8 - 이상을 사용한다면 JDK 8과 관련된 기능을 추가적으로 활용할 때 kotlin-stdlib-jdk8 의존성이 필요하다.

JDK 9 이상 - kotlin-stdlib만 사용해도 기본적으로 호환된다. org.jetbrains.kotlin.jvm 을 추가하면 이 의존성은 자동으로 추가된다 !

### 2. kapt 'com.querydsl:querydsl-apt:5.0.0:jpa'

QueryDSL에서 JPA 기반 코드 생성을 위한 APT(Annotation Processing Tool) 플러그인.

QueryDSL을 사용할 때, Query 타입 클래스(QClass)를 자동 생성하는 역할을 한다.

:jpa 옵션이 붙어 있기 때문에 JPA 관련 메타 클래스를 생성하는 데 사용된다.

### 3. kapt란 ??

Kotlin Annotation Processing Tool 의 줄임말이고, 자바의 annotation proccessor를 대체한다.

컴파일 타임에 어노테이션 프로세서를 실행하여 코드를 자동 생성한다.

### 4. kapt 'org.springframework.boot:spring-boot-configuration-processor'

Spring Boot의 @ConfigurationProperties 어노테이션을 처리하여 자동으로 메타데이터를 생성하는 Annotation Processor이다.

IDE에서 자동 완성(autocomplete) 및 유효성 검사(validation) 기능을 제공하는 역할을 한다.

## 기타

### 1. jvmToolChain()

```groovy
kotlin {
  jvmToolchain(?) // 11, 17 ...
}
```

Kotlin에서 프로젝트가 특정 JVM 버전으로 컴파일되도록 설정하는 역할을 한다.

jvmToolchain(11)을 설정하면, JDK 11이 필요한 코드 작성 시, Gradle 빌드에서 JDK 11을 사용한다.

프로젝트가 특정 JDK version으로 동작하도록 설정하여 IDE, Gradle JDK 버전 일관성을 보장하도록 하는 설정.
