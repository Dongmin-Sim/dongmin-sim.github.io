---
title: Redis
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - database
  - redis
tags:
  - build
  - ci/cd
image: 
path:
  - /assets/img/thumbnail/gradle.png
lqip: 
alt: Gradle이란?
---

  
## 들어가며

왜 Redis 를 공부해야할까 ?
- 가장 손쉽게 사용할 수 있는 In-memory 저장소
- 높은 성능 & 확장성
- 더욱 더 복잡해지는 현대적인 서버의 구조적 배경으로 인해 사용성 확대
	- 분산환경에서 세션 관리나 캐시를 담당하기 위한 하나의 구성 요소로서 필요성 증대


- Redis가 무엇인지, 
	- 왜 사용하는지 ?
	- 어떤 환경에서 주로 사용되는지 ?
	- 특성 중심으로 설명할 수 있어야 한다.
- 세션 스토어, 캐시와 같은 Redis의 주요한 활용 방법을 익히고 구현할 수 있게 된다 .
- 클러스터 구성, 백업 장애복구 등 실제 Redis 운영에 필요한 지식을 갖춤
- Redis를 활용하는 다양한 어플리케이션 아키텍쳐들을 익힘 

## Redis 특징 

Redis (Remote Dictionary Server)
- 외부 데이터 저장소(데이터 관점)
- 전동적인 DBMS의 역할 수행(영속성 관점)
	- 추가적인 백업 방식을 이용해 
- 어플리케이션이 이용할 수 있는 (소프트웨어)미들웨어
	- 어플리케이션에서 사용할 수 있는 기능들이 존재

Redis가 가능한 것
- 아주 빠른 데이터 저장소로 활용
- 분산된 서버들 간의 데이터 공유(동기화, 작업 분할 등)
- 내장된 자료구조를 활용한 기능 구현
	- ex) SortedSet을 이용한 랭킹 시스템

DB, Database DBMS
- 데이터를 읽고 쓸 수 잇는 기능을 제공하는 소프트웨어 
- 어플리케이션이 데이터 저장을 간단히 처리할 수 있도록 해줌
- 어플리케이션과의 관심사의 분리, 계층화
	- 데이터베이스가 데이터 관리(읽기, 쓰기, 수정 등)에 대한 (영속성)관심사를 담당

In-memory DB
- 데이터를 디스크에 저장하지 않는다. 
- 휘발성인 RAM에 저장하고 있음. 
	- DB가 다운될 경우 데이터가 모두 소실된다.
- 아주 빠른 속도
- 안정성과 성능(속도)간의 트레이드 오프를 적당히 교환한 일종의 특수목적 DB
	- 용도에 맞게 DB(높은 무결성을 필요로 하는 데이터들)와 Redis(낮은 무결성, 데이터 소실에도 큰 문제가 없는 용도의 데이터들)를 사용할 수 있음
	- 혼합해서 사용하기도함 (Cache용)
	- Redis의 영속성을 확보하기 위해 백업 기능등을 추가적으로 사용

![[Pasted image 20240320231336.png]]


![[Pasted image 20240320231751.png]]

## Key-Value store 로서의 Redis

Key-Value 구조 ? 
- 프로그램 언어에서의 데이터 구조 (Array, List, Map, ...)
	- 각각 다른 특성을 가지는 데이터 구조들 
- DB 의 데이터 모델 관점에서의 구조 
	- 구조에 따라 성능 특성이 달라짐. 
	- 관계형 모델(일반적 RDB), 네트워크모델, 계층형모델 등등

### Key-Value Store 컨셉
- 특정 값을 key 로 해서 그와 연관된 데이터를 Value로 저장(프로그램 언어에 존재하는 데이터 구조인Map 과 동일)
- 가장 단순한 데이터 저장 방식으로 
- Value에 접근속도가 가장 빠르다. O(1)

Map에 DBMS 기능을 달아놨다 라고 생각하면 편함

![[Pasted image 20240320232059.png]]


#### Key-Value Store 장점
- 단순성에서 오는 쉬운 구현, 사용
	- RDB데이터 베이스의 경우 관계를 표현하고 관리할 수 있는 질의 언어 SQL을 사용하여 데이터에 접근, 수정,삽입, 삭제등을 할 수 있지만, 
	- KV 저장소의 경우 key를 가지고 데이터에 접근하므로 사용 방법이 훨씬 쉬움 
- Hash를 이용해 값을 바로 읽으므로 속도가 빠름 , 
	- 데이터에 관리(접근, 삽입, 수정, 삭제)에 대한 추가연산이 필요가 없다.
- 분산환경에서의 수평적 확장성
	- 확장성에서 강점이 있음.
#### Key-Value Store 단점
- 범위 검색 등의 복잡한 질의가 불가능하다.
- Key 가 없으면 데이터에 접근할 수 없음

#### Key-value Store 컨셉의 활용 
> 단순한 구조의 데이터로 높은 성능과 확장성이 우선시되는 환경에서 활용될 수 있음
- 대표적으로 프로그래밍 언어의 자료구조에서 활용될 수 있고, 
- NoSQL DB(Redis, AWS DynamoDB)에서 활용될 수 있음

## Redis 등장과 활용 
#### 등장

![[Pasted image 20240320233024.png]]
- Redis Labs 클러스터, 분산환경에 대한 더 적극적인 기능과 지원.

### 활용 
- Session Store
- Cache
- Limit Rater
	- 특정 API의 분당 호출수를 제한
- Job Queue
	- 여러 서버들간의 테스트 Job 을 저장하고 소비하는 형태로 활용도 가능
등 별도로 구현해야했던 공통기능들을 Redis를 활용하여 구현하기가 더 편리해졋음.
서버를 개발하며 Redis를 사용하지 않는 구조를 찾기 어려움 



## 관계형 DB 란 
> 관계형 모델에 기초한 데이터 베이스

- 관계 : 속성들로 이루어진 집합, DB에서 테이블이라고 부름
- 속성 : DB에서 컬럼을 나타냄
- 튜플 : DB에서 레코드, 로우로 표현 

#### 테이블 간의 관계
- 한 테이블에서 속성들이 연관되는 것 처럼 다른 테이블들 간에도 연관관계가 있을 수 있음, 
- 이러한 연관관계를 연결하는 것을 조인 이라고 함.
- 연관관계의 종류는 다음과 같이 나눌 수 있음. 
	- 일대일
	- 일대다
	- 다대다 

이러한 관계형 DB를 관리하기 위한 설계 질의 언어, sql(특수 목적 언어)
- 질의 언어의 표준
- 자료 검색, 수정, 스키마 관리

예시 
- 안정적, 정형 데이터를 다루기 용이하게 설계됨.
MySQL, Oracle, SQL Server, PostgreSQL 등등

#### 장점
- 데이터 무결성 유지에 용이 
	- 정규화를 통해 중복을 제거한다. 
	- 데이터 무결성은 데이터의 정확성, 일관성, 유효성이 유지되는 것
- SQL을 활용하여 복잡한 질의 가 가능함.
#### 단점
- 스키마를 정의해야함
- Join으로 인한 성능 문제 
- 데이터들의 복잡한 관계로 인해 수평적 확장이 쉽지 않음.  


## NoSQL

비관계형 DB를 표현할 때 주로 사용되는 용어
- No SQL, Not Only SQL
- 관계형 DB에서 데이터 질의 언어 SQL이 없다는 데이터베이스라는 의미

NoSLQ의 등장 배경 
- 2000년대의 개발 환경의 변화 
- 스토리지 비용이 내려감(데이터의 중복이 스토리지 용량 면에서는 큰 문제가 되지않음)
- 다루는 데이터의 크기와 성능의 요구사항이 커짐
	- 고성능 데이터 모델이나 데이터 분할이 필요 
- 분산 환경의 대중화(scale-out)
	- 데이터 분할 --> 분산
- 단순한 대량의 데이터가 다루기 쉽다.
	- 약간의 중복을 허용하겠다. 감수하겠다. 무결성을 조금 내려 놓음. 

### NoSQL의 데이터 모델 
- Key-Value 뿐 아니라 
- Graph
- Document
- Wide-Column 등 

### NoSQL DB 종류
![[Pasted image 20240320234719.png]]


## Redis의 특성

![[Pasted image 20240320235028.png]]
- 확장된 힙의 역할
- DBMS 역할
- 미들웨어 역할도 함. 

#### External Heal로서의 Redis
- Application 이 장애가 나도 Redis의 데이터는 보존 (단기)
- Application 이 여러 머신에서 돌아도 같은 데이터를 접근 가능 

#### DBMS 로서의 Redis

- Redis의 영속화 수단을 이요해 DBMS 처럼 이용 
- 일반 RDB 수준의 안정성을 얻기 위해서는 속도를 희생해야함.

#### Middleware 로서의 Redis
- Redis가 제공하는 자료구조를 활용해 복잡한 로직을 쉽게 구현 
- Soerted Set 
	- 단순 저장이 아닌 Redis가ㅏ 제공하는 자료구조를 활용 

Redis 의 다른 NoSQL DB 차별점
- Key-value 모델의 store
- 다양한 자료구조를 지원함
- 원하는 수준의 영속성을 구성할 수 있음
	- 백업 지원
- In-memory 솔루션이라는 점에서 오는 특징을 활용할 때 가장 효율적


## Redis의 data type

redis-cli 를 통해 redis 에 접속

key : value 에서 value에 다양한 데이터 타입을 지원함.
- key 는 대소문자 구분을 함.
- 
### String
- 가장 기본적인 데이터 타입으로 제일 많이 사용됨
- 키에 대응되는 문자열로 저장됨. 
	- 바이트 배열로 저장된 (binary-safe : 모든 문자를 표현할 수 잇다.)
	- 
- 바이너리로 변환할 수 있는 모든 데이터를 저장 가능하다(JPG와 같은 파일도 가능)
	- 이론상 다 가능
- 최대 크기는 512MB

#### 주요 커맨드 

| 명령어  | 기능                            | 시그니처                                   | 예시                             |
| ---- | ----------------------------- | -------------------------------------- | ------------------------------ |
| SET  | 특정 키에 문자열 값을 저장한다.            | SET [key] [value]                      | SET say hello                  |
| GET  | 특정 키의 문자열 값을 얻어옴              | GET [key]                              | GET say                        |
| INCR | 특정 키의 값을 Integer로 취급하여 1 증가시킴 | INCR [key:Integer]                     | INCR mycount                   |
| DECR | 특정 키의 값을 Integer로 취급하여 1 감소시킴 | DECR [key:Integer]                     | DECR mycount                   |
| MSET | 여러 키에 대한 값을 한번에 저장            | MSET [key]  [value] [key]  [value] ... | MSET mine milk     your coffee |
| MGET | 여러 키에 대한 값을 한번에 얻어옴           | MSET [key]  [key]                      | MGET mine your                 |
- INCR, DECR 명령어의 경우 atomic 하게 동작함
	- 멀티스레드 환경에서 두 명령어가 race condition에 빠질일이 없음 
	- 여러 요청이 있어도 원자적인 명령 결과 값을 보장함. 


## List
- Linked-list 형태의 자료구조 (인덱스 접근은 느리지만, 데이터 추가/삭제가 빠름)
- Queue, Stack 으로 사용할 수 있음. 

#### 주요 커맨드  

| 명령어    | 기능                           | 시그니처                       | 예시                  |
| ------ | ---------------------------- | -------------------------- | ------------------- |
| LPUSH  | 리스트의 왼쪽(head)에 새로운 값을 추가한다.  | LPUSH [key] [value]        | LPUSH mylist apple  |
| RPUSH  | 리스트의 오른쪽(tail)에 새로운 값을 추가한다. | RPUSH [key] [value]        | RPUSH mylist banana |
| LLEN   | 리스트에 들어있는 아이템 수를 반환          | LLEN [key]                 | LLEN mylist         |
| LRANGE | 리스트의 특정 범위를 반환               | LRANGE [key] [start] [end] | LRANGE mylist 0 -1  |
| LPOP   | 리스트의 왼쪽head에서 값을 삭제하고 반환     | LPOP [key]                 | LPOP mylist         |
| RPOP   | 리스트의 오른쪽tail에서 값을 삭제하고 반환    | RPOP [key]                 | RPOP mylist         |
- LRANGE의 경우 시작인덱스와 끝 인덱스를 추가 인자로 받음
	- -1 의 경우 제일 마지막 원소를 뜻함 
	- 파이썬과 똑같은 인덱스 시스템을 가짐


## Set
- 순서가 없는 유니트한 값들의 집합
- 중복 허용하지 않음
- 검색이 빠름. 
	- 개별 접근을 위한 인덱스가 존재하지는 않고
	- 집합 연산이 가능함(교집합, 합집합)

#### 주요 커맨드  

| 명령어       | 기능                    | 시그니처                             | 예시                           |
| --------- | --------------------- | -------------------------------- | ---------------------------- |
| SADD      | Set에 데이터를 추가한다.       | SADD [key] [value1] [value2] ... | SADD memberList kim hong sim |
| SREM      | Set에서 데이터를 삭제한다.      | SREM [key] [value]               | SREM memberList sim          |
| SCARD     | Set에 저장된 아이템 개수를 반환   | SCARD [key]                      | SCARD memberList             |
| SMEMBERS  | Set에 저장된 아이템들을 반환     | SMEMBERS [key]                   | SMEMBERS memberList          |
| SISMEMBER | 특정 값이 Set에 포함되어있는지 반환 | SISMEMBERS [key] [value]         | SISMEMBERS memberList kim    |
- 웹 페이지에서 특정 시간대 사용가능한 쿠폰을 발행하고자 할때, 
	- 해당 유저가 쿠폰을 발급 받았는지 않았는지 빠르게 확인 하는 방식으로 체크하는 예시
- 결과가 true 일 경우 1, false 일 경우 0 

## Hash
![[Pasted image 20240321163131.png]]
- 하나의 key 하위에 여러개의 field-value 쌍을 저장할 수 있음
	- json 형태를 그대로 string 저장해도 되지만, 
	- 특정 필드에 접근하려면 json 형식의 string 꺼내서 parsing 해야하는 과정이 필요함. 
	- 대신 전체 값을 불러오고 저장하는데 어려울 수 있음. 
	- 
- 여러 필드를 가진 객체를 저장하는 것으로 생각 가능
- 각각의 필드를 따로 사용할 때 유용함.
	- HINCRBY 명령어를 사용해 카운터로 활용 가능

#### 주요 커맨드  

| 명령어     | 기능                                 | 시그니처                                            | 예시                                  |
| ------- | ---------------------------------- | ----------------------------------------------- | ----------------------------------- |
| HSET    | 한개 또는 다수의 필드에 값을 저장                | HSET [key]  [field][value]  [field] [value] ... | hset post name "redis guide" like 1 |
| HGET    | 특정 필드의 값을 반환                       | HGET [key] [field]                              | hget post name                      |
| HMGET   | 한개 이상의 필드 값을 반환한다.                 | HMGET [key] [hash key] [field] ...              | hmget post name like                |
| HINCRBY | 특정 필드의 값을 Integer로 취급하여 지정한 숫자를 증가 | HINCRBY [key] [field] [int]                     | hincrby post like 1                 |
| HDEL    | 한개 이상의 필드를 삭제                      | HDEL [key] [hash key] [field] ...               | hdel post name                      |




## Sorted Set
![[Pasted image 20240321164514.png]]
- Set과 유사하게 유니크한 값의 집합
- 각 값은 연관된 score를 가지고 정렬되어 있음
- 정렬된 상태이기 때문에 빠르게 최소/최대값을 구할 수 있음.
- 순위계산, 리더보드 구현 등에 활용

#### 주요 커맨드  

| 명령어      | 기능                            | 시그니처                                         | 예시                                         |     |
| -------- | ----------------------------- | -------------------------------------------- | ------------------------------------------ | --- |
| ZADD     | 한개 또는 다수의 값을 추가 또는 업데이트       | ZADD [key] [score][value] [score][value] ... | zadd rank 10 member1 20 member2 30 member3 |     |
| ZRANGE   | 특정범위의 값을 반환(오름차순 정렬 기준)       | ZRANGE [key] [start] [end]                   | zrange rank 0 2                            |     |
| ZRANK    | 특정 값의 위치(순위)를 반환 (오름차순 정렬 기준) | ZRANK [key] [value]                          | zrank rank member1                         |     |
| ZREVRANK | 특정 값의 위치(순위)를 반환 (내림차순 정렬 기준) | ZREVRANK [key] [value]                       | zrevrank rank member1                      |     |
| ZREM     | 한개 이상의 값을 삭제                  | ZREM [key] [value]                           | zrem rank member3                          |     |



## Bitmap
![[Pasted image 20240321165357.png]]
- 비트 벡터를 사용해 N개의 Set을 공간 효율적으로 저장 
- 하나의 비트맵이 가지는 공간은 4byte(Integer)
- 비트 연산 가능
#### 주요 커맨드  

| 명령어      | 기능                             | 시그니처                                | 예시                               |     |
| -------- | ------------------------------ | ----------------------------------- | -------------------------------- | --- |
| SETBIT   | 비트맵의 특정 오프셋에 값을 변경             | SETBIT [key] [offset] [int]         | setbit visit 10 1                |     |
| GETBIT   | 비트맵의 특정 오프셋에 값을 반환             | GETBIT [key] [offset]               | getbit visit 10                  |     |
| BITCOUNT | 비트맵에서 set(1) 상태인 비트의 개수를 반환    | BITCOUNT [key]                      | bitcount visit                   |     |
| BITOP    | 비트맵들간의 비트 연산을 수행하고 결과를 비트맵에 저장 | BITOP [op] [dest key] [key1] [key2] | bitop and result today yesterday |     |
- 4byte 만큼의 공간을 가짐. 42억개 offset
- op는 and, or 등 여러 비트 연산자가 가능


## HyperLogLog

- 유니크한 값의 개수를 효율적으로 얻을 수 있음 .
- 확률적 자료구조로서 오차가 있으며, **매우 큰 데이터를 다룰 때 사용** 
	- 값을 저장하는 것은 아님.
- 2^64 개의 유니크 값을 계산 가능
- 12kb 까지 메모리를 사용하며, 0.81의 오차율을 허용 

#### 주요 커맨드  

| 명령어     | 기능                                   | 시그니처                                    | 예시                                               |     |
| ------- | ------------------------------------ | --------------------------------------- | ------------------------------------------------ | --- |
| PFADD   | HyperLogLog에 값들을 추가한다.               | PFADD [key] [value] [value] [value] ... | pfadd today_visit jay peter jane                 |     |
| PFCOUNT | HyperLogLog에 입력된 값들의 cardinality를 반환 | PFCOUNT [key]                           | pfcount today_visit                              |     |
| PFMERGE | 다수의 HyperLogLog를 병합                  | PFMERGE [key] [key] ...                 | pfmerge result_visit today_visit yesterday_visit |     |


## Redis 연동 
![[Pasted image 20240321171652.png]]
- Lettuce : 가장 많이 사용되는 라이브러리로, Spring Data Redis에 내장되어 있음. 
- Spring Data Redis는 Redis Template 라는 Redis 조작 추상레이어를 제공함.

JDBC 같은거