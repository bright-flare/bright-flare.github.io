---
title: Spring Boot HTTP2 설정하기
description: Spring Boot HTTP2 설정하기
author: bright-flare
date: 2022-06-24
categories: [SpringBoot]
tags: [Java, Spring Boot, HTTP2]
image:
  path: /assets/img/post/2022/springboot-http2/thumbnail.png
---

> **이 설정은 Spring boot embeded Tomcat 기준 입니다 !**
{: .prompt-tip }

## 🧑🏻‍💻 SSL 설정하기

- HTTP2를 설정하려면 먼저 SSL설정이 되어있어야 한다.
- `Servlet Container`로 `Tomcat`을 사용할 경우 8.5 이후 `9 버전`이상을 사용하는 것을 권장한다. (설정이 매우 어려움) 9 version 부터는 별도의 설정이 필요 없다.

### Keystore 생성하기

```bash
# terminal
$ keytool -genkey
    -alias sseob
    -storetype PKCS12
    -keyalg RSA
    -keysize 2048
    -keystore keystore.p12
    -validity 4000
```

- application.properties에 설정 추가하기.

```bash
server.ssl.key-store=keystore.p12
server.ssl.key-store-type=PKCS12
server.ssl.key-store-password=123123
server.ssl.key-alias=sseob
server.port=8443
```

### SSL 설정후 확인하기.

- SSL 설정을 하게되면 https로만 서비스 접근이 가능하다.
- `/hello` 라는 Url을 Mapping하는 Controller를 만들어 놓고 아래 명령어로 확인하자.

```bash
curl -I -k --http2 https://localhost:8443/hello
```

### Connector 추가하기.

- https 이외에 http로도 접근할 수 있도록 구성하려면 Connector를 추가해야된다.
- Connector 객체를 하나 더 추가하여 http도 사용할 수 있도록 구성하자.

```java
@Bean
public ServletWebServerFactory servletWebServerFactory() {
	TomcatServletWebServerFactory tomcatServletWebServerFactory = new TomcatServletWebServerFactory();

	tomcatServletWebServerFactory.addAdditionalTomcatConnectors(createStandartConnector());
	return tomcatServletWebServerFactory;
}

private Connector createStandartConnector() {
	Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
	connector.setPort(8080);
	return connector;
}
```

- 확인

```bash
curl -I -k --http2 http://localhost:8080/hello
curl -I -k --http2 https://localhost:8443/hello
```

### HTTP2 설정

- application.properties에 한줄만 추가하면 간단하게 설정 된다 !

```bash
server.http2.enabled=true
```
