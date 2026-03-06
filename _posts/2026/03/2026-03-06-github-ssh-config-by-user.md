---
title: "GitHub SSH 사용자별 인증 설정 완벽 가이드 🔐"
date: 2026-03-06 00:00:00 +0900
categories: [Git]
tags: [Git, GitHub, SSH, Multi user]
description: "GitHub SSH 인증 설정 완벽 가이드"
author: bright-flare
---

# 계정별 GitHub SSH 설정 방법 (멀티 계정)

> 하나의 컴퓨터에서 개인 계정과 회사 계정처럼 여러 GitHub 계정을 SSH로 분리하여 사용하는 방법을 정리합니다.
>
> 이 글에서는 유저별 SSH 설정 방법을 다루고, 자세한 GitHub SSH 설정은 이 포스팅을 참고해 주세요. [GitHub SSH 인증 설정 완벽 가이드 🔐](https://bright-flare.github.io/posts/github-ssh-config/)

---

## 왜 멀티 계정 설정이 필요한가?

회사에서 업무용 GitHub 계정을 사용하면서 개인 프로젝트도 별도 계정으로 관리하는 경우가 많습니다. SSH 키를 하나만 사용하면 한쪽 계정에만 인증이 되기 때문에, 계정마다 별도의 SSH 키를 생성하고 구분해서 사용해야 합니다.

---

## 1. 계정별 SSH 키 생성

각 계정에 대해 별도의 키를 생성합니다. `-f` 옵션으로 파일명을 지정하여 기존 키와 구분합니다.

```bash
# 개인 계정용
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal

# 회사 계정용
ssh-keygen -t ed25519 -C "work@company.com" -f ~/.ssh/id_ed25519_work
```

완료되면 `~/.ssh/` 디렉토리에 총 4개의 파일이 생성됩니다.

```
~/.ssh/
├── id_ed25519_personal       # 개인 계정 개인 키
├── id_ed25519_personal.pub   # 개인 계정 공개 키
├── id_ed25519_work           # 회사 계정 개인 키
└── id_ed25519_work.pub       # 회사 계정 공개 키
```

---

## 2. SSH Agent에 키 등록

생성한 키를 모두 SSH Agent에 등록합니다.

```bash
eval "$(ssh-agent -s)"

ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

등록 확인은 아래 명령어로 할 수 있습니다.

```bash
ssh-add -l
```

```
256 SHA256:xxxx... personal@email.com (ED25519)
256 SHA256:yyyy... work@company.com (ED25519)
```

---

## 3. GitHub에 각각 공개 키 등록

계정별로 해당하는 공개 키를 GitHub에 등록합니다.

```bash
# 개인 계정 공개 키 확인
cat ~/.ssh/id_ed25519_personal.pub

# 회사 계정 공개 키 확인
cat ~/.ssh/id_ed25519_work.pub
```

- **개인 계정** GitHub에 로그인 → Settings → SSH and GPG keys → `id_ed25519_personal.pub` 등록
- **회사 계정** GitHub에 로그인 → Settings → SSH and GPG keys → `id_ed25519_work.pub` 등록

> **주의**: 반드시 각 계정에 맞는 공개 키를 등록해야 합니다. 잘못된 키를 등록하면 인증에 실패합니다.

---

## 4. SSH Config 설정 (핵심)

이 단계가 멀티 계정 설정의 핵심입니다. `~/.ssh/config` 파일에서 Host 별칭을 만들어 어떤 계정으로 접속할지 구분합니다.

```bash
vi ~/.ssh/config
```

```
# 개인 GitHub 계정
Host github.com-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

# 회사 GitHub 계정
Host github.com-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work
```

각 항목의 의미는 다음과 같습니다.

- **Host**: 내가 정하는 별칭입니다. `git@github.com-personal` 형태로 사용하게 됩니다.
- **HostName**: 실제 접속할 서버 주소입니다. 두 계정 모두 `github.com`입니다.
- **User**: GitHub SSH 접속 시 항상 `git`입니다.
- **IdentityFile**: 이 Host로 접속할 때 사용할 키 파일 경로입니다.

---

## 5. 연결 테스트

각 별칭으로 SSH 연결을 테스트합니다.

```bash
# 개인 계정 테스트
ssh -T git@github.com-personal

# 회사 계정 테스트
ssh -T git@github.com-work
```

각각 아래와 같이 해당 계정의 username이 출력되면 성공입니다.

```
Hi personal-username! You've successfully authenticated, ...
```

```
Hi work-username! You've successfully authenticated, ...
```

---

## 6. Repository에서 사용하기

### 6-1. 새로 clone 할 때

기존 `github.com` 대신 config에서 설정한 **Host 별칭**을 사용합니다.

```bash
# 개인 계정 저장소 clone
git clone git@github.com-personal:personal-user/my-side-project.git

# 회사 계정 저장소 clone
git clone git@github.com-work:work-org/company-project.git
```

### 6-2. 기존 Repository의 Remote URL 변경

이미 clone한 저장소가 있다면 remote URL을 변경합니다.

```bash
# 현재 URL 확인
git remote -v

# 개인 계정 저장소라면
git remote set-url origin git@github.com-personal:personal-user/my-side-project.git

# 회사 계정 저장소라면
git remote set-url origin git@github.com-work:work-org/company-project.git
```

---

## 7. 계정별 Git 사용자 정보 설정

SSH 키로 인증은 분리했지만, 커밋에 기록되는 사용자 정보도 계정별로 다르게 설정해야 합니다. global 설정을 사용하면 모든 저장소에 동일한 이름과 이메일이 기록되므로, 저장소별로 local 설정을 하거나 `includeIf`로 디렉토리 기반 자동 분리를 사용합니다.

### 방법 1: 저장소별 local 설정

각 저장소 디렉토리에서 직접 설정합니다.

```bash
cd ~/projects/company-project

git config user.name "회사이름"
git config user.email "work@company.com"
```

### 방법 2: 디렉토리 기반 자동 분리 (권장)

프로젝트를 디렉토리로 분리하고, `~/.gitconfig`에서 디렉토리별 설정을 자동 적용합니다.

```
~/
├── personal/     # 개인 프로젝트
│   ├── my-side-project/
│   └── blog/
└── work/         # 회사 프로젝트
    ├── company-project/
    └── internal-tool/
```

`~/.gitconfig` 파일에 아래 내용을 추가합니다.

```gitconfig
[user]
    name = 개인이름
    email = personal@email.com

[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work
```

`~/.gitconfig-work` 파일을 생성합니다.

```gitconfig
[user]
    name = 회사이름
    email = work@company.com
```

이렇게 설정하면 `~/work/` 하위의 모든 저장소에서는 자동으로 회사 이메일이 커밋에 기록됩니다.

설정이 잘 적용되었는지 확인합니다.

```bash
# 개인 프로젝트 디렉토리에서
cd ~/personal/my-side-project
git config user.email   # personal@email.com

# 회사 프로젝트 디렉토리에서
cd ~/work/company-project
git config user.email   # work@company.com
```

---

## 트러블슈팅

### 어떤 키가 사용되는지 확인하고 싶을 때

```bash
ssh -vT git@github.com-work
```

verbose 출력에서 `Offering public key` 라인을 확인하면 어떤 키 파일로 인증을 시도하는지 볼 수 있습니다.

### config 파일 수정 후에도 이전 키로 접속되는 경우

SSH Agent에 캐시된 키가 우선 사용될 수 있습니다. Agent를 초기화하고 필요한 키만 다시 등록합니다.

```bash
# 모든 키 제거
ssh-add -D

# 필요한 키만 다시 등록
ssh-add ~/.ssh/id_ed25519_personal
ssh-add ~/.ssh/id_ed25519_work
```

### Host 별칭을 잊어버렸을 때

```bash
cat ~/.ssh/config
```

---