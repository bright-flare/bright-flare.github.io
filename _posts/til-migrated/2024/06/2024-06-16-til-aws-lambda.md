---
title: "AWS Lambda"
date: 2024-06-16 00:00:00 +0900
categories: [AWS]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: AWS/AWS Lambda.md"
---
## Serverless ?
- 서버가 없는게 아니라 서버가 보이지 않거나 서버를 직접 다루지 않는 것.
- 서버를 프로비전(공급)하지 않는것을 의미.

### Serverless의 예
- AWS Batch, ECS Fargate, Aurora mysql, Elasticache 등이 있음.

## AWS Lambda
- EC2는 메모리와 CPU크기가 제한됨. 인스턴스가 계속 실행되는 상태.
- 람다는 관리할 서버가 없이 함수(기능을) 실행함. 주문형으로 그때그때 실행된다.

## Lambda 장점
- 가격 책정에 유리함
- 프리티어에서 람다를 넉넉히 제공해줌
- 다양한 AWS 서비스와 통합가능.
- 프로그래밍 언어에 제한적이지 않음. 많은 언어를 지원함.
- 클라우드와치 모니터링 통합이 쉬워짐.


## Lambda container image 지원
- 람다 런타임 API 를 구현하여 사용가능.
- 람다 런타임 API를 구현하지 않으면 ECS Fargate에서 컨테이너를 실행해야 한다.


## Lambda 사용 예 
- Serverless Cron tab (event bridge) 를 생성하여 lambda로 간단한 기능을 주기적으로 실행하게 할 수 있다.
- API GW REST API 를 통해 lambda 함수를 호출하고 Kenesis를 통해 데이터를 변환한 뒤 Dynamo DB와 같은 데이터베이스에 데이터를 저장하고, S3로 파일을 생성할 수 있다.
- S3에 새 이미지가 업로드되면 이벤트를 발행해 lambda 함수를 작동시켜 크기가 작은 썸네일 이미지를 S3 저장소에 업로드하는 기능을 구현할 수 있다.
