---
title: "MongoDB 기본 명령어 정리"
date: 2023-12-22 00:00:00 +0900
categories: [Database, MongoDB]
tags: [MongoDB, Database]
description: "자주 사용하는 MongoDB 셸 명령어를 작업 흐름 기준으로 정리합니다."
author: bright-flare
---
MongoDB를 빠르게 다룰 때는 명령어를 기능별로 외우기보다 "조회 → 생성 → 변경" 흐름으로 기억하는 편이 실무에 더 유리하다.

## 상태 확인

```bash
help
show dbs
db
show collections
```

- `show dbs`: 데이터베이스 목록 확인
- `db`: 현재 선택된 데이터베이스 확인
- `show collections`: 현재 DB의 컬렉션 목록 확인

## 데이터베이스/컬렉션 생성

```bash
use mydatabase
db.createCollection("mycollection")
```

`use`는 데이터베이스를 선택하고, 없으면 이후 쓰기 시 생성된다.

## 문서 CRUD

```javascript
// Create
db.mycollection.insert({ name: "sseob", age: 30, city: "Seoul" })

// Read
db.mycollection.find()

// Update
db.mycollection.update({ name: "sseob" }, { $set: { age: 31 } })

// Delete
db.mycollection.remove({ name: "sseob" })
```

## 인덱스

```javascript
db.mycollection.createIndex({ name: 1 })
```

조회 조건에 맞는 인덱스가 없으면 데이터가 늘어날수록 조회 비용이 급격히 커진다.

## 실무 체크포인트

- `update`/`remove` 실행 전 조회 조건을 먼저 검증한다.
- 운영에서는 `insertOne`, `updateOne`, `deleteOne`처럼 의도를 명확히 드러내는 API 사용을 권장한다.
- 인덱스는 생성 이후에도 실제 쿼리 패턴 기준으로 주기적으로 점검한다.
