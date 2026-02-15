---
title: "2025-02-07-ai-new-feature-seminar"
date: 2025-02-17 00:00:00 +0900
categories: [seminar, aws]
tags: [TIL, migration]
description: "TIL에서 마이그레이션한 문서: seminar/aws/2025-02-07-ai-new-feature-seminar.md"
---
## AI 관련 신규 기능

### 검색, 탐색 트렌드의 변화

- 검색창을 통한 검색이 아니라 이제는 이커머스에서도 AI agnet를 통한 검색이 엄청나게 발전할 것이다.
- 앞으로는 양방향으로 검색이 이루어질 것이다.

### Bedrock

- 다양한 모델이 있고 선택적으로 골라서 사용할 수 있음.
- 마켓플레이스에서 모델을 선택해서 배포가능.

### Nova

- bedrock 통해서 노바 이용 가능. 
- aws에서 개발한 인텔리전스 중, foundation 모델이다.
- 200+ lanuguage를 지원한다. -> 한국어 지원
- text, image, vedio 등을 지원하는 모델이 있음.

### Text to sql

- 사람의 언어. 자연어를 sql query 형태로 바꿔주는 기능.
- application 개발을 수개월에서 며칠로 단축 예상.

### Amazon Q

- 기존에는 코파일럿 수준의 코드 작성 , 수정 등의 기능을 지원했는데 더 많은 기능이 확장되었다.
- Java 8, 11 to 17 migration 을 지원가능 등등


## AI를 제외한 신규 기능

### Aurora

- 0개 ACU 지원.  기존에는 0.5가 미니멈이었음.
- 1개만 존재했던 Writer를 

### Aurora DSQL (NewSQL)

- DSQL을 구성하는 컴포넌트별로 확장이 가능한 DB이다.
	- 트랜잭션, 세션 라우터 
	- 쿼리 프로세서
	- 스토리지 등
- 멀티 리전 액티브-액티브가 가능하다.
- A리전에 write할 경우 , B리전에 sync 하는 과정까지가 하나의 transaction 단위로 동작한다.
- 성능 보장이 어렵지만, 고 가용성이 보장된다.
- 기존 데이터베이스와 동작하는 방식이 다르다.

### Oracle Database@AWS

- AWS 데이터센터에서 Oracle Exadata 워크로드를 실행한다.
- OCI 에 비해 장점 ? 
	- AWS 통합이 쉽다.
	- zero-ETL 예정이라서 오라클 데이터를 Data Lake에 옮기거나 DW에 옮기는 작업이 매우 수월해진다.


### CloudWatch

- Database Insights
	- 여러 DB 지표를 모아서 하나의 페이지에서 확인 가능.
	- 슬로우쿼리, 이벤트 등등 확인가능.

### DynamoDB

- 매우 파격적인 가격인하 ! 
	- 50 ~ 70 %

### S3 Table

- S3 에 정형 데이터를 쌓아두는 케이스가 많아지고 있는데, 
- S3 데이터를 트랜잭션 처리 가능하다.

### S3 Metadata

- 버킷에 추가되는 모든 신규 오브젝트들에 대해 자동으로 메타데이터를 기록함.
