---
title: "🦞 OpenClaw Docker Compose 설정 및 브라우저 사용하게 하기"
description: "Dockerfile, docker-compose.yml 기반으로 OpenClaw를 빠르게 올리고 브라우저 실행까지 연결하기"
author: bright-flare
date: 2026-03-02
categories: [AI]
tags: [AI, OpenClaw, Docker, Docker Compose, Linux, Ubuntu, Telegram]
pin: true
image:
  path: /assets/img/post/2026/openclaw/openclaw_linux.png
---

기존에는 Ubuntu 컨테이너에 접속해서 패키지를 한땀한땀 하나씩 설치했습니다. 😭  
이번에는 `Dockerfile`과 `docker-compose.yml`로 복잡하고 오래 걸리는 설치 과정을 자동화해서, 더 빠르고 간단하게 OpenClaw를 설치하고,  
🦞 OpenClaw가 브라우저를 사용할수 있도록 하는 방법을 소개합니다 ! 

## 1) 작업 디렉토리 준비

```bash
mkdir -p ~/Documents/openclaw-docker-compose
cd ~/Documents/openclaw-docker-compose
```

## 2) Dockerfile 작성

은근히 오래 걸리는 npm설치시간을 단축하기 위해서 node가 설치되어 있는 node:22-bookworm 이미지를 사용한다.  

기존에 Ubuntu Container에 접속하여 한땀한땀 설치하던 과정을 미리 Dockerfile로 작성하여 재설치시 엄청나게 시간 단축을 할 수 있다 ! 

```Dockerfile
# npm 설치시간 단축을 위해 node:22 이미지 사용
FROM node:22-bookworm

SHELL ["/bin/bash", "-c"]

# 필수 or 자주 사용하는 패키지 설치 (apt update & install 묶음), chromium 브라우저 설치
RUN apt-get update && apt-get install -y \
    ca-certificates \
    curl \
    jq \
    git \
    chromium \
    fonts-liberation \
    xdg-utils \
    && rm -rf /var/lib/apt/lists/*

# OpenClaw 글로벌 설치
RUN which node && node -v && npm -v && npm i -g openclaw

# 기본 작업 디렉토리 설정
WORKDIR /workspace

# 실행 시 기본 쉘
CMD ["bash"]
```

## 3) docker-compose.yml 작성

```yaml
version: '3.8'
services:
  openclaw-base:
    build: .
    container_name: openclaw-base
    stdin_open: true
    tty: true
    volumes:
      - "$HOME/Documents/openclaw/openclaw-workspace:/workspace"
      - "$HOME/Documents/openclaw/openclaw-data:/root/.openclaw"
    working_dir: /workspace
    ports:
      - "18789:18789"
    command: bash
```

## 4) 이미지 빌드 및 컨테이너 실행

```bash
docker compose up -d --build
```

## 5) OpenClaw 온보딩

```bash
openclaw onboard
```

온보딩 순서는 기존과 같다.

온보딩 과정은 이 포스팅에서 더 자세하게 확인 가능합니다.
- [🦞 OpenClaw Docker Ubuntu Container에 설치해서 더 자유롭게 사용하기 !](https://bright-flare.github.io/posts/openclaw-with-ubuntu-container-guide/)

1. QuickStart 선택
2. 모델/인증 방식 선택
3. Telegram Bot Token 입력
4. 완료

## 6) 브라우저 사용 설정 (Chromium)

컨테이너 기반에서는 브라우저 경로와 샌드박스 옵션을 명시하는 게 안전하다고 합니다.  

(cdpPort 변경은 openclaw 에게 권유받아서 세팅함. 포트 충돌 가능성이 있어 변경했다고 함.)

```bash
openclaw config set browser.headless true
openclaw config set browser.noSandbox true
openclaw config set browser.executablePath /usr/bin/chromium
openclaw config set browser.profiles.openclaw.cdpPort 18900
```

Gateway 재시작 후 브라우저 상태 확인:

```bash
openclaw gateway stop || true
rm -f ~/.openclaw/run/gateway.lock ~/.openclaw/run/gateway.pid 2>/dev/null || true
openclaw gateway &
sleep 3
openclaw browser --browser-profile openclaw start
openclaw browser --browser-profile openclaw status --json
```

## 7) 브라우저 연결 확인 !

```bash
openclaw browser --browser-profile openclaw status
```

아래와 같이 출력되면 연결 성공 !
```
profile: openclaw
enabled: true
running: true
cdpPort: 18800
cdpUrl: http://127.0.0.1:18800
browser: custom
detectedBrowser: custom
detectedPath: /usr/bin/chromium
profileColor: #FF4500
```

## 끝 !! 이제 브라우저 열어서 기능 테스트까지 시키면 된다 🦞 !!
![addAll()](/assets/img/post/2026/openclaw/browser.png)
