---
title: "🦞 OpenClaw 퀵 스타트 가이드 !"
description: "미리 정의된 docker compose로 OpenClaw를 빠르게 설치할 수 있는 퀵 스타트 가이드 !"
author: bright-flare
date: 2026-02-16
categories: [AI]
tags: [AI, OpenClaw, Docker, Telegram]
pin: true
image:
  path: /assets/img/post/2026/openclaw/openclaw.png
---

미리 정의된 docker compose로 OpenClaw를 **빠르게 설치**하고, Telegram을 연동하는 퀵 스타트 가이드 입니다 ! 

## 1) 설치

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
./docker-setup.sh
```

## 2) Telegram 봇 만들기

1. Telegram에서 `@BotFather` 검색
2. `/start` 입력
3. `/newbot` 입력
4. 봇 이름/username 설정
5. 발급된 Bot Token 복사

## 3) LLM OAuth 인증

설치 중 안내되는 OpenAI OAuth URL을 브라우저에서 열어 인증합니다.

❗ 주의 오픈클로를 서버용 장비에 배포하고, OAuth 인증을 하게되면 벤 당할수도 있다고 합니다 !!  
서버용 장비에서는 API Key로 인증하세요 !

예시:

```text
https://auth.openai.com/oauth/authorize?response_type=code&client_id=app_...
```

인증이 끝나면 redirect URL이 나옵니다. 그 URL 전체를 복사해 CLI에 붙여넣습니다.

예시:

```text
http://localhost:1455/auth/callback?code=ac_xxxxx...
```

## 4) Telegram 페어링 승인

Telegram에서 만든 봇 대화창에 `/start`를 먼저 보낸 뒤,
CLI에 표시된 pairing code를 승인합니다.

```bash
docker compose run --rm openclaw-cli pairing approve telegram <PAIRING_CODE>
```

예시:

```bash
docker compose run --rm openclaw-cli pairing approve telegram TA6JBLX3
```

## 5) Gateway token 확인
```bash
docker compose run --rm openclaw-cli config get gateway.auth.token
```

## 6) Gateway toekn 대시보드에 입력
- 대시보드 Overview 접속
- http://localhost:18789/overview
- Gateway Token 란에 토큰 입력.

## 7) 디바이스 승인.

> 디바이스 리스트 확인 및 승인을 해야 대시보드에서 여러가지 부가 정보가 확인 가능하지만 여기서 막힘..
> 하지만 텔레그램으로 소통하면서 즐겁게 개발은 가능.

```bash
# 디바이스 리스트 확인
docker compose run --rm openclaw-cli devices list

# 디바이스 승인
docker compose run --rm openclaw-cli devices approve <request ID>
```

## 8) Docker 마운트 작업 경로 확인

OpenClaw 기본 작업 영역은 아래처럼 매핑됨.

- **호스트(macOS)**: `~/.openclaw/workspace`
- **컨테이너 내부**: `/home/node/.openclaw/workspace`

호스트 영역에서 OpenClaw가 어떤 작업을 하는지, 개발 결과물을 눈으로 직접 확인 가능합니다 !

두 위치에서 동일한 프로젝트/파일이 보이면 마운트가 정상입니다.

## 6) 트러블슈팅 포인트
- Telegram 알림이 안 오면:
  - Bot Token 확인
  - 봇 대화창에서 `/start` 보냈는지 확인
  - pairing code 승인 여부 확인

---
