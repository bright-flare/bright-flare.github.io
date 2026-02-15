---
title: "🦞 OpenClaw 빠른 시작"
description: "Docker로 OpenClaw를 올리고 Telegram 연동, 작업 마운트 경로까지 한 번에 확인하는 퀵스타트"
author: bright-flare
date: 2026-02-16
categories: [AI]
tags: [AI, OpenClaw, Docker, Telegram]
pin: true
image:
  path: /assets/img/post/2026/openclaw/openclaw.png
---

OpenClaw를 **빠르게 설치**하고, Telegram까지 붙인 다음,
마지막으로 **Docker 마운트 작업 경로가 제대로 연결됐는지** 확인하는 흐름입니다.

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

## 5) Docker 마운트 작업 경로 확인 (중요)

OpenClaw 기본 작업 영역은 아래처럼 매핑됩니다.

- **호스트(macOS)**: `~/.openclaw/workspace`
- **컨테이너 내부**: `/home/node/.openclaw/workspace`

즉, 컨테이너에서 파일을 만들면 호스트에서도 바로 보여야 정상입니다.

### 컨테이너 내부 확인

```bash
ls -la /home/node/.openclaw/workspace
```

### 호스트 확인

```bash
ls -la ~/.openclaw/workspace
```

두 위치에서 동일한 프로젝트/파일이 보이면 마운트가 정상입니다.

## 6) 트러블슈팅 포인트

- 호스트에서 파일이 안 보이면:
  - 경로를 `~/.openclaw/workspace`로 보고 있는지 확인
  - 파일 탐색기 새로고침
  - Docker Desktop 볼륨/마운트 설정 확인
- Telegram 알림이 안 오면:
  - Bot Token 확인
  - 봇 대화창에서 `/start` 보냈는지 확인
  - pairing code 승인 여부 확인

---