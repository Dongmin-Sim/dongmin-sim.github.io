---
title: Jpa
author: codongmin
date: 2024-05-17T01:23:00
categories:
  - spring
  - spring data
tags:
  - iBATIS
image:
  path: /assets/img/thumbnail/spring-data.png
  lqip:
  alt: 스프링 데이터
---

## 들어가며

JPA란 ?

## JPA란 ?

Java Persistence API 의 약자로 ORM 기술 표준을 의미한다.

ORM이 뭐냐 한다면 다음 글을 참조해보기를 권장한다.
👉 [ORM과 JPA에 대해 더 알고 싶다면?](https://dongmin-sim.github.io/posts/java-persistence-framework)

ORM을 구현하기 위한 일종의 표준안! == 인터페이스라고 보면된다. 이후에 설명하겠지만, JPA의 표준을 구현한 대표적인 구현체가 Hibernate 라는 친구다.

### JPA 구현체

- 하이버네이트
- 이클립스 링크
- 데이터 뉴클리어스

### JPA 버전

## 장점

- 생산성
- 유지보수
- 패러다임 불일치 해결자
  - SQL 중심 개발 -> 객체 중심으로
- 성능
- 데이터 접근 추상화와 벤더 독립

### 생산성

CRUD

persist
find
set~~

- java bean
- java collection
  remove

### 유지보수

객체 필드 변경시 모든 sql의 수정이 필요

필드만 처리하면되고 나머지는 jpa가 알아서 처리

### 패러다임 불일치 해결

- 상속
- 연관관계
- 객체 그래프 탐색

예시 hibernate

## 마무리
