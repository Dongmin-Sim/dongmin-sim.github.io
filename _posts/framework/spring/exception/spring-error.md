---
title: error
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - Spring
  - error
tags:
  - spring
image: 
path: /assets/img/thumbnail/gradle.png
lqip: 
alt: Gradle이란?
---

  
## 들어가며


  

## **에러** **처리**

  

## **스프링** **부트** **기본** **에러** **처리**

스프링 부트에서는 기본적으로 whitelabel error page를 가지고 있는데 이는 BasicErrorController 를 내부에서 빈으로 등록하고 가지고 있어 동작함 이때, ErrorController를 implement 하여 사용자가 구현할 경우 해당 사용자 errorcontroller를 빈으로 대신 등록함. 

  

application.properties, yaml 에서 에러에 대한 정보 노출 정도를 조정할 수 있음. 

```yaml
server:
  error:
    whitelabel:
      enabled: false
    include-stacktrace: always
    include-exception: true
    include-message: always
```

  

시스템 정보가 노출되기 때문에 주의해서 사용해야힘.

  

## **BasicErrorController** 

- 스프링 부트의 기본응답이 마음에 든다면 

* BasicErrorController 를 상속받아 특정 메소드만 오버라이드 

* 특정 핸들러 메소드를 추가하는 식으로 응용

* BasicErrorController의 핸들러 메소드 

* BasicErrorController.errorHtml() -> 뷰 응답.

* BasicErrorController.error() -> Json body 응답.

```java

@Controller

@RequestMapping({"${server.error.path:${error.path:/error}}"})

public class BasicErrorController extends AbstractErrorController {

...

@RequestMapping(produces = {"text/html"})

public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {...}

  

@RequestMapping

public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {...}

...

}

```

  

  

##  **커스텀** **에러** **페이지** **:** **기본** 

- Spring Boot 에서 미리 약속해둔 이름의 파일을 넣는 것 만으로도 구현 가능 
* 간단히 static html, template 파이르 추가해서 커스텀 페이지를 등록
- 단일 기본 페이지 
	* /resources/static/error.html
	* /resources/public/error.html
	* /resources/template/error.[템플릿 확장자]
* http status 별 기본 페이지 
	* /resources/[static | public | template]/error/{http status number}.[html | 템플릿 확장자]
	* /resources/[static | public | template]/error/4xx.[html | 템플릿 확장자]
	* /resources/[static | public | template]/error/5xx.[html | 템플릿 확장자]
	* 실제 `4xx` 를 텍스트로


## @ExceptionHandler 예외 핸들러 메소드
- 비지니스 로직에서 발생한 예외를 담당하는 핸들러 메소드   
- 위치 : 특정 컨트롤러 클래스의 내부나, `@ControllerAdvice` 컴포넌트 내부
- 특정 예외를 지정하여 반응하도록 가능 
- 예외 처리 범위 
	- 컨트롤러 안에 작생했을 경우 --> 해당 컨트롤러 한정
	- `@ControllerAdvice` 컴포넌트에 작성했을 경우 : 프로젝트 전체 

- 핸들러 메소드에 해당되기 때문에, 입출력 자료형도 웹 핸들러 메서드와 유사.
- 예외를 입력 인자로 다룰 수 있다는 점이 차이.

```java
@ExceptionHandler
public ResponseEntity<APIErrorResponse> general(RuntimeException e {
	return ResponseEntity.internalServerError().build();
})
```

런타임 익셉션이 스택트레이스 안에 있는 다른 모든 익셉션을 핸들링할 수 있음. 

```java
@ExceptionHandler(RuntimeException.class)
public ResponseEntity<APIErrorResponse> general(RuntimeException e {
	return ResponseEntity.internalServerError().build();
})
```
이 경우에는 RuntimeException 만 잡게됨. RuntimeException의 하위 exception은 잡지 않음. 

## @ControllerAdvice
- @ExceptionHandler 는 특정 컨트롤러 클래스의 내부에서 특정 예외를 잡는데 사용되었는데(예외 커버 범위가 특정 컨트롤러), @ControllerAdvice 를 통해 글로벌하게 적용할 수 있게 됨. 
- @ExceptionHandler을 모아서 글로벌하게 적용하고자 할 때 사용

### 종류 
- `@ControllerAdvice`
	- 주로 view 에러 처리할 때
- `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`
	- 주로 json api 에러 처리할 때

### 속성
> 예외 커버 범위를 지정하기 위한 목표의 속성들 
- value == basePackages
- basePackages
	- 적용 범위를 **문자열**을 이용해 특정 패키지로 지정할때
- basePackageClasses
	- 적용 범위를 **대표 클래스** 한개를 이용해 특정 패키지로 지정
		- 대표 클래스가 담겨있는 패키지 전체(하위까지) 포함됨.
	- basePackages 를 type-safe 하게 사용하기 위해 제공하는 옵션
		- 문자열의 경우 컴파일 타임에서 에러날 경우 잡지 못하는데 이를 방지하고자.
- assignableTypes : 적용 범위를 **특정 클래스에 할당가능 타입**으로 컨트롤러로 지정 
- annotations : 적용 범위를 특정 **어노테이션**이 있는 컨트롤러를 지정.

### ResponseEntityExceptionHandler
- Spring MVC에서 내부적으로 발생하는 예외들을 처리하는 클래스 
	- API 예외 처리를 담당하는 @ControllerAdvice 클래스에서 상속 받아 사용 
	- 커스터마이징을 원하는 특정 메소드를 오버라이드






예외 발생시 stack trace 사용 X - 커버
- 보안 문제 
- log space 큰 문제