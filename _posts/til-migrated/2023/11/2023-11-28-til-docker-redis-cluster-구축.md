---
title: "docker redis-cluster 구축"
date: 2023-11-28 00:00:00 +0900
categories: [docker, redis]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: docker/redis/docker redis-cluster 구축.md"
---
### 1. Docker 네트워크 생성

```
docker network create redis-net
```


### 2. Redis 클러스터 노드 컨테이너 실행

Redis 클러스터는 여러 노드로 구성되며 각 노드는 다른 포트에서 실행됩니다.

#### 노드 1 실행

```
docker run -d --name redis-node1 --net redis-net -p 7001:6379 redis:latest redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000
```

#### 노드 2 실행

```
docker run -d --name redis-node2 --net redis-net -p 7002:6379 redis:latest redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000
```

#### 노드 3 실행

```
docker run -d --name redis-node3 --net redis-net -p 7003:6379 redis:latest redis-server --port 6379 --cluster-enabled yes --cluster-config-file nodes.conf --cluster-node-timeout 5000
```


### 3. Redis 클러스터 생성

Redis 클러스터를 생성하려면 각 노드의 IP와 포트를 사용하여 클러스터를 초기화해야 합니다. 이 작업은 `redis-cli`를 사용하여 수행됩니다.

```
docker exec -it redis-node1 redis-cli --cluster create <node1-IP>:6379 <node2-IP>:6379 <node3-IP>:6379 --cluster-replicas 1

```

위 명령에서 `<node1-IP>`, `<node2-IP>`, `<node3-IP>`는 각 노드의 IP 주소로 교체되어야 합니다. 클러스터를 초기화하는 과정에서 각 노드의 정보와 슬롯 할당이 수행됩니다.

### 4. 클러스터 확인

클러스터가 정상적으로 구성되었는지 확인하기 위해 다음 명령을 사용합니다.

```
docker exec -it redis-node1 redis-cli cluster nodes
```

클러스터의 노드 정보와 슬롯 할당이 표시됩니다.

이제 Redis 클러스터가 Docker 컨테이너 내에서 구축되었습니다. 클러스터는 각 Redis 노드가 별도의 Docker 컨테이너로 실행되며, Docker 네트워크를 사용하여 상호 통신합니다.
