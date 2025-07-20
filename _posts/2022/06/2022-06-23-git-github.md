---
title: 🧩 Git, GitHub
description: Git 과 Github에 대하여 정리한다.
author: bright-flare
date: 2022-06-23
categories: [Devtools]
tags: [Git, Github]
pin: true
image:
  path: /assets/img/post/2022/git-github/thumbnail.png
---

## Git❓
- git은 파일의 상태를 저장하는 프로그램이다.
- commit한 시점의 파일들의 상태를 저장해놓는다고 생각하면 된다. (branch를 옮겼을 때에도 마찬가지)

## Git, GitHub ❓
- 정말 간단하게 github은 저장소이고, git은 local version 관리 프로그램이다 !
- **git**에서 마음껏 version 관리를 해놓은 **commit들**을 push해서 **github**에 저장한다. 

## Stage ❓
- commit시키기 전 파일들에게 마음의 준비를 시켜주는 장소임.. 🙏
- Stage에 올라간 파일들은 commit대기중인 파일들인 것.

<br>
<hr>

## ⌨️ 명령어

### 🛠 git 설정, 초기화

```bash
# git version 확인 !

git --version
```

```bash
# 🌱 git 저장소 초기화. 초기화 하면 [ .git ] 이라는 폴더가 생기며 version관리 시작가능 !

git init
```

```bash
# 🌏 git 글로벌 설정 목록 조회
# 📝 --global 옵션을 빼고 입력하게되면 Repository단위로 설정하는것 입니다. 

git config --global --list
```

```bash
# 글로벌 사용자 설정

git config --global user.name "User name"
```

```bash
# 글로벌 이메일 설정

git config --global user.email "git@gmail.com"
```

```bash
# 기본 브랜치를 main으로 설정

git config --global init.defaultBranch main
```

<br>
<hr>

### ☁️ 원격 저장소와 연결하기
```bash
# 📝 여기서 origin은 원격 repository의 별칭이다.
# 🚀 local git과 원격 저장소(GitHub)의 repository와 연결한다.
 
git remote add origin https://github.com/github-user/github-repository.git
```


```bash
# 💎 github-user의 github-repository 저장소 복제하기

git clone https://github.com/github-user/github-repository.git
```

```bash
# 👀 현재 local 저장소와 연결된 원격 저장소 url 확인

git remote -v
```

<br>
<hr>

### 🧑🏻‍💻 파일 상태 다루기,상태 확인하기 원격 저장소에 올리기

```bash 
# 👀 현재 상태 확인 commit할 파일이 있는지 add할 파일이 있는지

git status
```

```bash
# 👆 전체 파일 stage에 올리기 dot, --all option 대신에 파일명을 쓰면 해당 파일만 올라감
git add .

git add --all
```

```bash
# 👇 전체 파일 unstage. 스테이지에서 내리기. dot 대신에 특정 파일만 내릴수도 있음.
git reset HEAD .
```

```bash
# 📸 stage에 올라간 파일,디렉토리 commit하기. 현재 상태를 저장한다. (현재 시점을 사진 찍는것과 같은 느낌.)

git commit -m "commit message"
```

```bash
# 👀 commit log 확인하기

git log
```

```bash
# git log 를 통해 commit hash code를 복사하여 해당 📸 시점으로 돌아가기.
# hash code만 있으면 앞, 뒤로 원하는 시점으로 돌아갈 수 있음.
# --hard option 말고 다른 option들도 있습니다.

git reset --hard 91f61e82b0175d49c4fceda57d8b9133432f124f
```

```bash
# stage에 올리지 않은 파일 중, 변경사항 특정 파일의 되돌리기
git checkout -- test.java
```

```bash
# 🚀 commit한 내용들을 원격 저장소 main branch에 밀어넣기.

git push origin main
```

```bash
# 원격 저장소 main branch의 코드들을 local repository에 동기화한다. 

git pull origin main
```

<br>
<hr>

### 🌱 Branch

```bash
# 🌱 branch 생성하기
# 현재 커밋 상태부터 branch가 생성되며 생성 이후로는 하나의 큰 줄기에서 뻗어나가는 가지처럼 갈라진다.
# 갈라진 가지들, branch들은 각자의 삶을 살다가 pull request를 통해 merge하면 다시 합칠 수 있음 !

git branch branchname
```

```bash 
# 👀 현재 branch 확인하기

git branch
```

<br>
<hr>

### 👉 branch 이동하기 !
```bash
git checkout branch002
```

```bash
# branch 삭제하기

git branch -d branch002
```

<br>
<hr>

### 🏷 tag
```bash
# git tag 생성하기
git tag <tag-name>
```
```bash
# 특정 커밋에 태그 생성하기 (추가하기)
git tag <tag-name> <commit-identifier>
```
```bash
# local tag를 원격 저장소에 푸시하기
git push origin <tag-name>

```
```bash
# local에 생성되어 있는 전체 태그 푸시하기.
git push origin --tags
```

```bash
# origin tag 삭제하기
git push origin :<tag-name>
```