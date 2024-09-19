---
title: 
author: codongmin
date: 2024-03-17T22:16:00
categories: 
tags:
  - build
  - ci/cd
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며


## 타임리프란 

모던 서버 사이드 자바 템플릿 엔진이다. 


## Thymeleaf 만의 특징, 강점

- HTML5 웹 표준을 준수하는 템플릿, 
- 전체적인 문법인 HTML5 마크업 표준을 최대한 해치지 않게끔 설계 
- Decoupled logic : 템플릿 문법을 아예 템플릿에서 분리 가능  (thymeleaf 3 에서 도입되는 피처) 
	- 순수한 마크업만 남음 -> 템플릿 엔진이 작동하지 않아도 렌더링 되는 정적 목업 페이지 
	- 템플릿 코드와 템플릿 문법이 별개의 파일에 존재하게 됨.
	- 디자이너가 이해하기 쉬운 코드이다.

## 템플릿 문법 적용 방법

3가지 
- "th:" tag
- "data-th" attribute
- decoupled logic

## Expressions
- Variable Expressions : `${...}`
	- 주로 많이 사용 
- Selection Variable Expressions : `*{...}`
- Message Expression : `#{...}`
	- 외부 환경 변수, 
- Link URL Expressions : `@{...}`
	- url 연결시
- Fragment Expressions : `~{...}`


### Text operations 
- String concatenation : `+`
- Literal substitutions : `|the name is ${name}|`

### Arithmetic operations 
- Binary operators : `+, -, *, /, %`
- Minus sign (unary operator) : `-` 

### Boolean operations 
- Binary operators : `and`, `or` 
- Boolean negation (unary operator) : `!`, `not`

### Comparisons and equality
- Comparators:  >, <, >=, <=, (gt, lt, ge, le)
- Equality operators : == != (eq, ne)

### Conditional operators 
- if-then : `(if) ? (then)`
- if-then-else : `(if) ? (then) : (else)`
- default : `(value) ?: (default value)`

### Special tokens
- No-operations : `_`







- **서버 사이드HTML 렌더링(SSR)**
	- 백엔드 서버에서 HTML을 동적으로 렌더링하는 용도로 사용됨
- **네츄럴 템플릿** 
	- 타임리프틑 순수 HTML을 최대한 유지하는 특징이 있음. 
	- 타임리프로 작성한 파일은 HTML을 유지하기 때문에 웹 브라우저에서 파일을 직접 열어 내용으 ㄹ확인할 수 있음 .
	- 서버를 통해 뷰 템플릿을 거치면 동적으로 변경된 결과를 확인할 수 있음. 
	- JSP를 포함한 다른 뷰 템플릿들은 해당 파일을 열면, 예를 들어서 JSP 파일 자체를 그대로 웹 브라우저에서 열어보면 JSP 소스코드와 HTML이 뒤죽박죽 섞여서 웹 브라우저에서 정상적인 HTML 결과를 확인할 수 없다. 오직 서버를 통 해서 JSP가 렌더링 되고 HTML 응답 결과를 받아야 화면을 확인할 수 있다.  
	- 반면에 타임리프로 작성된 파일은 해당 파일을 그대로 웹 브라우저에서 열어도 정상적인 HTML 결과를 확인할 수 있다. 
	- 물론 이 경우 동적으로 결과가 렌더링 되지는 않는다. 하지만 HTML 마크업 결과가 어떻게 되는지 파일만 열어도 바 로 확인할 수 있다.  
	- 이렇게 **순수 HTML을 그대로 유지하면서 뷰 템플릿도 사용할 수 있는 타임리프의 특징을 네츄럴 템플릿**(natural templates)이라 한다.
- **스프링 통합 지원**
	- 타임리프는 스프링과 자연스럽게 통합되고, 스프링의 다양한 기능을 편리하게 사용할 수 있게 지원


선언 
`<html xmlns:th="http://www.thymeleaf.org">`