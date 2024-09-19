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

이 레이어는 무엇일까?

## @Repository

스프링 스테레오 타입 애노테이션

Persistence layer를 구현하는 클래스에 사용

- @Component와 마찬가지로 해당 클래스를 빈으로 등록
- DAO 패턴을 적용한 클래스에도 사용 가능
- Persistence layer에서 발생하는 예외를 잡아서 DataAccessException으로 처리해줌 --> @Component 와 @Repository 애노테이션의 가장 큰 차이
  - PersistenceExceptionTranslationPostProcessor <--- 내부 구현체
- Spring Data JPA 를 사용한다면 "직접 사용할 일은 없다"고 봐도 무방함.

## 마무리

layered archieteure https://www.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch01.html
https://dev-setung.tistory.com/19
https://www.dimodelo.com/blog/2018/what-is-a-persistent-layer/
