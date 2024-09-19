---
title: Spring Boot 버전별 변천사
author: codongmin
date: 2024-03-17T22:16:00
categories: 
tags:
  - build
  - ci/cd
image: 
path: /assets/img/thumbnail/gradle.png
lqip: 
alt: Gradle이란?
---

  
## 들어가며


주요 Spring Boot 변경 사항을 체크하는 경로 
- Spring Offical Blog : https://spring.io/blog/category/releases
- Spring Boot Github : https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.2-Release-Notes
- SpringOne Platform Presentation 2017
- Baeldung 
- Quora
- Stackoverflow


## Spring Boot 1 to Spring Boot 2 주요 변경사항 


### Java8(+java9) + Spring Framework 5
- Java 8 이 최소 사양 
- Java 9 공식 최초 지원 - Boot 1.x 는 미지원
- Spring Framework 5 


### third-party upgrade
- Tomcat 8.5(embedded)
- Flyway 5
- Hibernate 5.2
- Thymeleaf 3
- Elasticsearch 5.6
- Gradle 4 (버전 확인 필요, 문법이 일부 변경됨)
- Jetty 9.4
- Mockito 2.x 

### Reactive Spring 
- 한정된 자원(thread pool)으로 비동기(asynchronous) 넌블럭킹(non-blocking)알고리즘을 이용해 다수의 요청에도 빠르고 예측 가능한 응답 성능(반응)을 실현하고자 하는 요구에서 나옴

리액티브 지원 모듈 
- Spring WebFlux
- Reactive Spring Data (Spring Jpa에 대응되는)
- Reactive Spring Security
- Embedded Netty Server(Tomcat 서버를 사용할 필요가 없어짐, 서블릿 컨테이너 X)

### Functional APIs
- WebFlux.fn
- WebMvc.fn (Spring Framework 5.2)
- 기존의 스프링 웹어플리케이션을 함수형 스타일로 작성 가능
- 스프링 기술과 애노테이션에서 분리된 코드 
	- 자바 코드 레벨에서 분석 가능
	- 독립적인 유닛 테스트 가능
	- 스프링 컨테이너에서 독립


### Kotlin 1.2 
- 코틀린으로 본젹적으로 개발 가능 

### configuration Properties
- 프로퍼티를 쓸 때, Relaxed binding 은 여전히 지원 
```
my.foo.hello-world
my.foo.helloWorld
my.foo.hello_world
===>
my.foo.helloworld
```
- 프로퍼티를 읽을 때, 양식이 통일됨. 
	- 엘리먼트 구분 : "."
	- 영어 소문자 + ㅜㅅ자 
	- 단어 구분자로 "-" 사용 가능 
- 환경 변수 (environment variables)에서 컬렉션 데이터의 인덱스 표현 가능
	- MY_VAR_1 = a -> my.var[1] = "a"
	- MY_VAR_1_2 = a -> my.var[1]  [2] = "a"
- 더 편리한 자료형 인식(ex: java.time.Duration -> "1s", "2m", "5d")
- Origin 지원 : 스프링 부트가 읽은 프로퍼티의 위치를 기억하고, 에러가 나면 알려줌
	- ex: "origin":"class path resource [application.properties]1:27"

### Gradle plugin
- 최소 버전 4.x
- bootRepackage -> bootJar & bootWar
- 이제는 dependency management 를 명시적으로 작성해주어야 함. 

### Actuator 변경점
- 보안성 강화 : 1.5 에서 기본으로 보여주던 endpoint를 더 이상 보여주지 않음
- @Endpoints : 커스텀 endpoint 를 환경(MVC, JMX, Jerdy..)에 상관 없이 편하게 구현
- 이름 변화 
	- /autoconfig -> /conditions
	- /trace -> /httptrace

### Spring Security 
- OAuth 2.0 통합 
- 커스텀 설정이 더 쉬워짐
- WebSecurityConfigurerAdpater 순서 문제 해결  
	- 기본 설정이 하나로 통합됨
	- WebSecurityConfigurerAdpater를 추가하면 기본 설정이 꺼짐
- 보안이 중요한 기능들은 명시적으로 작성하게끔 변경(ex: actuator)