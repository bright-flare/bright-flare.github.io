---
title: "GitHub SSH 인증 설정 완벽 가이드 🔐"
date: 2025-04-25 00:00:00 +0900
categories: [Git]
tags: [Git, GitHub, SSH]
description: "GitHub SSH 인증 설정 완벽 가이드"
author: bright-flare
---

## GitHub SSH 설정 방법

> 매번 비밀번호를 입력하지 않고 GitHub에 안전하게 연결하는 SSH 설정 방법을 정리합니다.

---

## 1. 기존 SSH 키 확인

```bash
ls -al ~/.ssh
```

`id_ed25519`, `id_ed25519.pub` 같은 파일이 이미 존재한다면 **2단계를 건너뛰고** 바로 3단계로 이동합니다.

---

## 2. SSH 키 생성

GitHub이 공식 권장하는 Ed25519 알고리즘으로 생성합니다.

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

실행하면 저장 경로와 passphrase를 묻는 프롬프트가 나타납니다.

```
Enter file in which to save the key (/home/you/.ssh/id_ed25519):   # Enter (기본값)
Enter passphrase (empty for no passphrase):                        # 비밀번호 설정 (선택)
Enter same passphrase again:
```

기본값으로 진행하면 `~/.ssh/id_ed25519`(개인 키)와 `~/.ssh/id_ed25519.pub`(공개 키)가 생성됩니다.

> **참고**: Ed25519를 지원하지 않는 레거시 환경에서는 `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` 을 사용합니다.

---

## 3. SSH Agent에 키 등록

```bash
# SSH Agent 실행
eval "$(ssh-agent -s)"

# 개인 키 등록
ssh-add ~/.ssh/id_ed25519
```

### macOS 추가 설정

`~/.ssh/config` 파일을 생성하거나 편집하여 아래 내용을 추가합니다.

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

---

## 4. GitHub에 공개 키 등록

### 4-1. 공개 키 복사

```bash
# macOS
pbcopy < ~/.ssh/id_ed25519.pub
```

### 4-2. GitHub에 등록

**GitHub 로그인 → 프로필 아이콘 → Settings → SSH and GPG keys → New SSH key**

- **Title**: 컴퓨터를 식별할 수 있는 이름 (예: `MacBook-Pro`, `Ubuntu-Desktop`)
- **Key type**: `Authentication Key`
- **Key**: 복사한 공개 키 붙여넣기

**Add SSH key**를 클릭해 등록을 완료합니다.

---

## 5. 연결 테스트

```bash
ssh -T git@github.com
```

처음 연결 시 fingerprint 확인 메시지가 나타나면 `yes`를 입력합니다.

```
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

이 메시지가 나오면 설정이 완료된 것입니다.

---

## 6. 기존 Repository의 Remote URL 변경

HTTPS로 clone한 저장소가 있다면 SSH URL로 변경합니다.

```bash
# 현재 URL 확인
git remote -v

# SSH URL로 변경
git remote set-url origin git@github.com:username/repo.git

# 변경 확인
git remote -v
```

---

## 트러블슈팅

### Permission denied (publickey)

```bash
# SSH Agent에 키가 등록되어 있는지 확인
ssh-add -l

# 상세 로그로 어떤 키를 시도하는지 확인
ssh -vT git@github.com
```

### 22번 포트 차단되어 있는 경우

`~/.ssh/config`에 아래 내용을 추가하면 443 포트로 우회할 수 있습니다.

```
Host github.com
  HostName ssh.github.com
  Port 443
  User git
```

---
