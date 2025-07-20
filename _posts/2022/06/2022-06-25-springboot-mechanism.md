---
title: Spring Boot 원리
description: Spring Boot 원리와 자동설정에 대하여 알아보자.
author: bright-flare
date: 2022-06-25
categories: [Framework, Spring Boot]
tags: [Java, Spring Boot]
image:
  path: /assets/img/post/2022/springboot-mechanism/thumbnail.png
---

## 🌈 의존성 관리

- springboot의 pom.xml에는 `spring-boot-starter-parent` 의존성이 존재한다.

```xml
<parent>    
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.5.6</version>
  <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.5.6</version>
</parent>
```

- `spring-boot-starter-parent` pom의 속내를 들여다보면 `spring-boot-dependencies`라는 parent pom이 보이는데 이곳에 수많은 의존성들의 version과 세부 의존성들이 명시되어 있다. 그래서 `spring-boot-starter-web` 이라는 의존성을 추가하면 `spring-boot-dependencies` pom에 정의 되어있는 version과 세부 의존성들이 추가된다.
- `spring-boot-dependencies`에서 관리하지 않는 의존성들은 version을 명시 해야한다.

### `spring-boot-starter-parent` 를 parent로 두는 이유는 의존성 관리의 편의성을 위해서만이 아니다.

```
<properties>
  <java.version>1.8</java.version>
  <resource.delimiter>@</resource.delimiter>
  <maven.compiler.source>${java.version}</maven.compiler.source>
  <maven.compiler.target>${java.version}</maven.compiler.target>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>
```

- 위 properties에 적혀있는 설정값들과, resource, plugin등 springboot 프로젝트에 최적화 되어있는 수많은 설정들이 추가된다.

<br>
<hr>

## @SpringBootApplication annotation

- `@SpringBootApplication` 어노테이션은 아래 3가지 어노테이션이 혼합되어 있다.
- @Configuration + @EnableAutoCofiguration + @ComponentScan
- 이 어노테이션이 적용 되어있는 Class의 package부터 ComponentScan 한다.

<aside>
💡 **@ComponentScan 어노테이션에 base package를 쓰지 않는다면 해당 Class가 존재하는 package부터 Bean을 Scan한다.**

</aside>

### ✨ Springboot에서 Bean이 읽어지는 방법

- Bean은 두단계로 나누어져 읽어진다.
    1. `@ComponentScan`을 통해서.
    2. `@EnableAutoConfiguration`을 통해서.

### @EnableAutoConfiguration

- Auto configuration을 활성화 하는 어노테이션이다.
- `@EnableAutoConfiguration` 어노테이션은 Springboot에 미리 작성 되어있는 meta file을 읽어 자동설정을 하게 한다.
- spring-boot-autoconfigure.jar의 meta file중 `spring.factories` file의 key값에 해당하는 파일들을 읽어들인다.

```xml
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
```

- 하지만 무조건적으로 읽어들이지 않고 `@Conditional~~` 로 시작하는 **Conditional** 어노테이션들의 조건에 따라 Bean으로 등록할지 말지 정해진다.

<br>
<hr>

## ⚡️ 독립적으로 실행 가능한 JAR !

> **Java는 중첩된 jar파일. 즉, jar파일 내에 포함된 jar파일을 로드하는 표준 방법이 없다. 이전에 'uber jar'로 패키징을 하여 해당 문제를 해결했었지만, 
jar 파일을 class의 형태로 만들어 패키징 하기 때문에 어떠한 library가 있는지 확인하기 어렵다는 문제가 있다.
springboot는 이 문제를 다른 접근 방식을 사용하여 jar 중첩 구조를 해결하였다.**
> 

- `$ mvn package` 를 통해 실행 가능한 JAR파일이 생성된다.
- 생성된 JAR파일을 java 명령어를 이용해 실행하면 Web application이 실행된다.

### Packaging plugin

> **springboot 프로젝트를 생성하면 아래와 같은 빌드 플러그인이 존재하는 것을 볼 수 있다.
이 플러그인은 packaging할 때 jar, war파일을 독립적으로 실행 가능한 파일로 만들어지도록 구조를 변경해준다.**
> 

```
<build>
  <plugins>
    <plugin>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-maven-plugin</artifactId>
    </plugin>
  </plugins>
</build>
```

### Packaging된 Jar 파일 구조

> **packaging된 jar파일을 압축 해제 하였을 때의 구조이다.
모든 의존성은 BOOT-INF/lib에 위치하게 된다.**
> 

```
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-BOOT-INF
    +-classes
    |  +-mycompany
    |     +-project
    |        +-YourClasses.class
    +-lib
       +-dependency1.jar
       +-dependency2.jar
```

### war 파일로 packaging 했을 때의 구조

```
example.war
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-WEB-INF
    +-classes
    |  +-com
    |     +-mycompany
    |        +-project
    |           +-YourClasses.class
    +-lib
    |  +-dependency1.jar
    |  +-dependency2.jar
    +-lib-provided
       +-servlet-api.jar
       +-dependency3.jar
```

### Jar파일을 어떻게 읽고 실행할까 ?

- `org.springframework.boot.loader.jar.JarFile`을 사용해서 내장 JAR를 읽는다.
- `org.springframework.boot.loader.Launcher`를 사용해서 실행한다.
    - jar file일 경우 `JarLauncher` class를 통해서, war file일 경우 `WarLauncher` class를 통해 application을 실행하게 된다.
    - `@SpringBootApplication` 어노테이션이 적용된 `Start-Class`의 main method를 호출하는 역할을 한다.