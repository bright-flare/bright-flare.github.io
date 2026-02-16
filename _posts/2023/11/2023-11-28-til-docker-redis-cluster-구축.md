---
title: "Docker로 Redis Cluster 구축하기"
date: 2023-11-28 00:00:00 +0900
categories: [Docker]
tags: [Redis, Docker]
description: "로컬 환경에서 Docker 기반 Redis Cluster를 구성하는 최소 절차와 점검 포인트를 정리합니다."
author: bright-flare
---
로컬 테스트 환경에서 Redis Cluster를 빠르게 구성할 때는 "네트워크 생성 → 노드 실행 → 클러스터 초기화" 순서로 접근하면 된다.

## 1. Docker 네트워크 생성

클러스터 노드 간 통신을 위해 전용 네트워크를 먼저 만든다.

```bash
docker network create redis-net
```

## 2. Redis 노드 컨테이너 실행

아래 예시는 3개 노드를 각각 다른 호스트 포트로 실행하는 최소 구성이다.

```bash
docker run -d --name redis-node1 --net redis-net -p 7001:6379 redis:latest \
  redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000

docker run -d --name redis-node2 --net redis-net -p 7002:6379 redis:latest \
  redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000

docker run -d --name redis-node3 --net redis-net -p 7003:6379 redis:latest \
  redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000
```

## 3. 클러스터 초기화

노드가 모두 올라오면 `redis-cli --cluster create`로 클러스터를 생성한다.

```bash
docker exec -it redis-node1 redis-cli --cluster create \
  <node1-ip>:6379 <node2-ip>:6379 <node3-ip>:6379 --cluster-replicas 0
```

`<nodeX-ip>`는 각 컨테이너 IP로 바꿔야 한다. 로컬 실습에서는 `docker inspect`로 확인한다.

## 4. 구성 확인

```bash
docker exec -it redis-node1 redis-cli cluster nodes
```

노드 목록과 슬롯 배치가 확인되면 클러스터 구성이 완료된 상태다.

## 운영 시 주의점

- 개발용 단일 호스트 구성과 운영 구성(복제/장애복구)은 분리해서 설계한다.
- `--cluster-replicas` 값은 고가용성 요구사항에 맞게 설정한다.
- 포트 개방, 컨테이너 재시작 정책, 데이터 볼륨 마운트 여부를 함께 점검한다.
