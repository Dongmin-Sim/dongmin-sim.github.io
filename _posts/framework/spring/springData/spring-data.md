---
title:
author: codongmin
date:
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

배경
spring data

Spring Data 프로젝트는 Spring 생태계에서 데이터 액세스 작업을 보다 간편하고 효율적으로 처리하기 위해 만들어졌습니다. 이 프로젝트의 탄생 배경은 다음과 같은 요인들에 기인합니다:

1. **복잡한 데이터 액세스 계층**: 기존에는 데이터베이스나 다른 데이터 저장소와의 상호 작용을 위해 복잡한 JDBC 코드를 작성해야 했습니다. 이로 인해 애플리케이션의 유지 보수성과 확장성이 저하되었습니다.
2. **다양한 데이터 저장소 지원**: 관계형 데이터베이스 뿐만 아니라 NoSQL 데이터베이스나 기타 데이터 저장소에 대한 지원이 필요했습니다. Spring Data는 다양한 종류의 데이터 저장소를 효과적으로 다룰 수 있는 추상화 계층을 제공합니다.
3. **반복적인 코드 작성**: CRUD(Create, Read, Update, Delete) 기능을 구현하는 데 필요한 반복적인 코드 작성이 필요했습니다. Spring Data는 이러한 반복적인 작업을 최소화하고자 표준화된 리포지토리 인터페이스를 제공합니다.
4. **객체-관계 매핑(Object-Relational Mapping, ORM)의 필요성**: 객체 지향 프로그래밍과 관계형 데이터베이스 간의 매핑 작업은 번거롭고 복잡합니다. Spring Data JPA는 JPA(Java Persistence API)를 기반으로 한 ORM 솔루션을 제공하여 객체-관계 매핑을 간소화합니다.
5. **데이터 액세스 작업의 추상화**: 데이터 액세스 작업은 애플리케이션의 핵심 로직과 분리되어야 합니다. Spring Data는 이러한 데이터 액세스 계층을 추상화하여 비즈니스 로직과의 분리를 도와줍니다.

이러한 배경 아래에서 Spring Data는 다양한 데이터 액세스 기술을 통합하고 표준화된 API를 제공하여 개발자가 데이터 액세스 작업을 더욱 쉽게 처리할 수 있도록 도와줍니다.

## Spring Data

관계형, 비관계형, 맵 리덕션 등 데이터 엑세스에 대해 일관된 접근 방식을 제공하는 것이 목표

- CRUD + 쿼리
- 동일한 인터페이스
  - db에 따른 차이만 있을뿐. 그래서 기본적인 crud에 대한 더 높은 수준의 추상화를 하여 인터페이스 화한것
- 페이징 처리
  - 기본적인 페이징 처리 통합
- 쿼리 메서드
- id 값만 넘겨도 도메인클래스로 바인딩

[Spring Data Commons](https://github.com/spring-projects/spring-data-commons) - Core Spring concepts underpinning every Spring Data module.

기타 메인 모듈들

-
- [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc) - Spring Data repository support for JDBC.
- [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) - Spring Data repository support for R2DBC.
- [Spring Data JPA](https://spring.io/projects/spring-data-jpa) - Spring Data repository support for JPA.
- [Spring Data KeyValue](https://github.com/spring-projects/spring-data-keyvalue) - `Map` based repositories and SPIs to easily build a Spring Data module for key-value stores.
- [Spring Data LDAP](https://spring.io/projects/spring-data-ldap) - Spring Data repository support for [Spring LDAP](https://github.com/spring-projects/spring-ldap).
- [Spring Data MongoDB](https://spring.io/projects/spring-data-mongodb) - Spring based, object-document support and repositories for MongoDB.
- [Spring Data Redis](https://spring.io/projects/spring-data-redis) - Easy configuration and access to Redis from Spring applications.
- [Spring Data REST](https://spring.io/projects/spring-data-rest) - Exports Spring Data repositories as hypermedia-driven RESTful resources.
- [Spring Data for Apache Cassandra](https://spring.io/projects/spring-data-cassandra) - Easy configuration and access to Apache Cassandra or large scale, highly available, data oriented Spring applications.
- [Spring Data for Apache Geode](https://spring.io/projects/spring-data-geode) - Easy configuration and access to Apache Geode for highly consistent, low latency, data oriented Spring applications.

## 마무리
