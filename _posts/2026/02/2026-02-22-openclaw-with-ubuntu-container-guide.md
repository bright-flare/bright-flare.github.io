---
title: "🦞 OpenClaw Docker Ubuntu Container에 설치해서 더 자유롭게 사용하기 !"
description: "Ubuntu에 OpenClaw를 직접 설치하고, 더 자유롭게 일 시키기."
author: bright-flare
date: 2026-02-22
categories: [AI]
tags: [AI, OpenClaw, Docker, Telegram, Linux, Ubuntu]
pin: true
image:
  path: /assets/img/post/2026/openclaw/openclaw_linux.png
---

OpenClaw를 더 자유로운 환경에서 구동하기 위해, Ubuntu 이미지로 docker container를 생성하여,
직접 설치하고, Telegram 연동 및 대시보드에서 Gateway token을 연결 및 승인하기까지 과정을 더 자세하게 소개합니다.


## 1) ubuntu 이미지 pull
```bash
docker pull ubuntu:24.04
```

## 2) ubuntu container run, openclaw 작업 경로 마운트 설정
```bash
docker run -it --name openclaw-base \
-p 18789:18789 \
-v "$HOME/Documents/openclaw/openclaw-workspace:/workspace" \
-v "$HOME/Documents/openclaw/openclaw-data:/root/.openclaw" \
-w /workspace \
ubuntu:24.04 bash
```

## 3) apt 업데이트
```bash
apt update
```

## 4) curl 설치
```bash
apt install curl
```

## 5) npm 설치 (npm으로 openclaw 설치할 것)
```bash
apt install npm
```

## 6) node 22.xx version up (openclaw 설치시 필요버전)
```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22
node -v
```

## 7) [Telegram 봇 준비 (연결된 글 확인)](https://bright-flare.github.io/posts/openclaw-telegram-integration-guide/)

## 8) openclaw 설치
```bash
npm i -g openclaw
```

## 9) 온보딩 !
```bash
openclaw onboard
```

## 10) 온보딩 진행.

### 1. 온보딩 시작  
![addAll()](/assets/img/post/2026/openclaw/1.png)

### 2. 온보딩 모드 QuickStart 선택  
![addAll()](/assets/img/post/2026/openclaw/2.png)

### 3. 원하는 모델 선택
![addAll()](/assets/img/post/2026/openclaw/3.png)

### 4. 원하는 인증방식 선택, (이 가이드에서는 OpenAI + OAuth로 진행합니다.)
![addAll()](/assets/img/post/2026/openclaw/4.png)

### 5. OpenAI OAuth 인증하기.
인증 이후에 브라우저 주소창의 Redirect URL을 복사하여 붙여넣기 한다 !  
![addAll()](/assets/img/post/2026/openclaw/5.png)

### 6. Model 선택하기
![addAll()](/assets/img/post/2026/openclaw/6.png)

### 7. Telegram 선택
![addAll()](/assets/img/post/2026/openclaw/7.png)

### 8. Telegram Bot Token 입력
Telegram Bot 생성 이후에 얻은 Token을 입력해야 합니다.  
[Telegram 봇 준비 (연결된 글 확인)](https://bright-flare.github.io/posts/openclaw-telegram-integration-guide/)  
![addAll()](/assets/img/post/2026/openclaw/8.png)

### 9. Skill 설정. (이 가이드에서는 생략.) 
![addAll()](/assets/img/post/2026/openclaw/9.png)

### 10. Hooks 설정. (이 가이드에서는 생략.)
![addAll()](/assets/img/post/2026/openclaw/9-1.png)

### 11. 온보딩 완료 !
![addAll()](/assets/img/post/2026/openclaw/11.png)


## 11) 드디어 openclaw 시작.
```bash
openclaw gateway
```
![addAll()](/assets/img/post/2026/openclaw/16.png)

## 12) openclaw 대시보드를 접속해보기.

Docker container 내부 `~/.openclaw/openclaw.json` 파일 또는  
마운트 되어있는 `~/Documents/openclaw/openclaw-data/openclaw.json` 파일 설정 변경

```json
"gateway": { "bind": "loopback" }

변경 👇

"gateway": { "bind": "0.0.0.0" }
```

변경시 로컬에서 대시보드 접속 가능하게됨.  
- http://localhost:18789/overview  

![addAll()](/assets/img/post/2026/openclaw/12.png)

## 13) Gateway Token 값 세팅.

container 내부 `~/.openclaw/openclaw.json` 파일 `gateway.auth.token` `property` 에 있습니다.

jq로 값 가져오기 (귀찮으면 jq로 가져오기)
```bash

apt install jq

jq -r '.gateway.auth.token' ~/.openclaw/openclaw.json
```

세팅하고 하단에 Connect 버튼을 누르면 대시보드 우측 영역에 STATUS OK 표시된다 !

![addAll()](/assets/img/post/2026/openclaw/13.png)

## 14) Telegram 페어링 코드 승인하기

페어링 코드 입력 대기 목록확인  
```bash
openclaw pairing list telegram
```

대기중인 페어링 코드 입력
```bash
openclaw pairing approve telegram <your pairing code>
```

승인 완료되면 텔레그램에서 안녕 나의 봇아 인사하기 ! 

## 15) 대시보드에서 텔레그램으로 나눈 대화내용도 확인 가능하다 !
![addAll()](/assets/img/post/2026/openclaw/14.png)

## 16) 대시보드에서 설정된 크론잡 정보도 확인할 수 있다 !
![addAll()](/assets/img/post/2026/openclaw/15.png)

## 17) 트러블슈팅 (버전 업데이트 이후 수정된 내용)

### 1. 업데이트 후 openclaw 시작이 안되는 경우
설정 파일이 다시 생성되면서 `gateway.bind` 값이 `loopback`으로 돌아갈 수 있다.

아래 경로 확인해서 `gateway.bind` 0.0.0.0 or lan 으로 수정
```bash
~/.openclaw/openclaw.json
```

### 2. 대시보드 Connect가 실패하는 경우
업데이트 이후 토큰이 갱신되었는데 이전 토큰을 입력하면 연결이 실패한다.

확인/재설정:
```bash
jq -r '.gateway.auth.token' ~/.openclaw/openclaw.json
```

출력된 최신 토큰으로 대시보드 `Gateway Token` 값을 다시 넣는다.

### 3. openclaw 신규 버전 업데이트 이후 start 불가한 경우

~/.openclaw/openclaw.json `gateway.controlUi.allowedOrigins` 값 추가하여 대응.

![addAll()](/assets/img/post/2026/openclaw/2026-02-26-issue.png)


```json
  "gateway": {
    "controlUi": {
      "allowedOrigins": [
        "http://localhost:18789",
        "http://127.0.0.1:18789"
      ]
    },
...
```

## 끝!! 이제 신나게 일시키자 🦞
