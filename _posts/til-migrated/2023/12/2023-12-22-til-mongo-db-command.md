---
title: "mongo db command"
date: 2023-12-22 00:00:00 +0900
categories: [mongo-db]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: mongo-db/mongo db command.md"
---
### 도움말
```
help
```

### collection 조회
```
show collections
```

### 현재 사용 중인 데이터베이스 조회
```
db
```

### 모든 데이터베이스 조회
```
show dbs
```

### 데이터베이스 생성/선택
```
use mydatabase
```

### 컬렉션 생성
```
db.createCollection("mycollection")
```

### 문서 삽입
```
db.mycollection.insert({ name: "sseob", age: 30, city: "Seoul" })
```

### 문서 조회
```
db.mycollection.find()
```
### 문서 업데이트
```
db.mycollection.update({ name: "sseob" }, { $set: { age: 31 } })
```
### 문서 삭제
```
db.mycollection.remove({ name: "sseob" })
```
### 인덱스 생성
```
db.mycollection.createIndex({ name: 1 })
```
