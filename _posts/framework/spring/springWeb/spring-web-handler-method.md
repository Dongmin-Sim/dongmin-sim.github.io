---
title: Handler method란? (feat. dispatch servlet)
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - Spring
  - webMVC
tags:
  - spring
  - webMVC
  - handler
image: 
path:
  - /assets/img/thumbnail/gradle.png
lqip: 
alt: 
preview: 핸들러 메소드란?
---

핸들러 메소드란? 
핸들러 메소드의 역할
핸들러 메소드의 spec
- input 
- output

## 들어가며

Spring 에서 사용하는 HandlerMethod 에 대해서 알아보려고 한다. 

핸들러 메소드가 클라이언트의 요청과 처리 구조에서 어떤 위치에 있는지 전체적으로 조망한다음.  
그에 맞추어 핸들러 메소드의 역할을 설명하려고 한다.

이후에는 핸들러 메소드의 기본 구조에 대해서 알아보고,  
핸들러 메소드의 작성 방법과 예시에 대해서 알아보려고 한다.


## 배경 

핸들러 메소드에 대해 이야기하기 전에 사용자의 특정 API 에 대한 요청이 어떻게 처리되는지, 전체 구조 속에서 핸들러 메소드의 위치를 살펴보고자 한다. 

예를 들어 제품의 정보를 알고 있는 서버가 있다고 가정해보자.

만약 특정 제품의 정보를 알고 싶다면 사용자는 서버에게 특정 제품의 정보에 대한 요청을 할 수 있다. 
그러면 서버에서는 해당 요청을 받아서, 요청에 맞는 정보를 다시 응답해준다. 

이것이 우리가 알고 있는 클라이언트-서버 구조이다. 

그러면 사용자는 그냥 전화기에 대고 정보를 요청하는 것처럼, 마구 요청을 하면 될까? 당연히 안된다. 요청-응답을 위해 특별히 약속한 규약을 API 라고하고, 우리는 이 API를 통해 정보를 요청하고 응답받는다. 

그러면 서버는 어떻게 요청들을 처리할까? 

![Desktop View](/assets/posts/이미지파일.png){: width="972" height="589" .w-50 }_이미지 설명_

- 여러 API 들을 만들어놓고 한 곳에서 모두 처리할까?
- 아니면 API마다 이것을 담당하는 "**무언가**"을 **매핑**해서 처리할까?

물론 첫번째 방법을 사용하는 것이 가장 직관적이고, 쉽다. 그렇지만 처리해야할 일은 많은데, 직원 한명이 모두 해당일을 담당하고 처리한다면 그 직원은 손발이 남아날까

사실 정해진 답은 없지만, 보통은 후자의 방법을 많이 사용한다. 
하지만 두번째 방법을 사용하게 되면 이 방법을 사용하기 위해 처리해야할 일이 하나 생긴다. 바로 API와 이를 처리하는 "**무언가**"를 서로 연결해줄 **담당자**가 필요해진다. 누가 이 API를 맡아 처리할지 결정해주어야 하는 결정자의 역할이 하나 더 생기는 것이다.

이와 같은 흐름을 이해했다면, HandlerMethod에 대한 이해는 사실상 끝났다! 여기서 처리를 담당하는 무언가를 담당하는게 바로 HandlerMethod이기 때문이다.

## DispatchServlet

앞에서 API 와 이를 처리하는 무언가를 서로 연결해줄 담당자가 필요하다고 언급했다. 


![Desktop View](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Pont_transpondeur.jpg/640px-Pont_transpondeur.jpg){: width="972" height="589" .w-50 }_기차 전차대_





![[Pasted image 20240318170752.png]]






Servlet에 대한 더 자세한 내용은 다음 글에 정리해두었다. 👉[서블릿이란? (Servlet)]()

## 핸들러 메소드 역할

앞의 배경맥락을 이해했다면, 핸들러 메소드는 요청의 처리를 담당하는 개체로 자연스럽게 받아들여졌을 것이다. 간단하다 그리 어렵지 않다.

정리하면 핸들러 메소드는 클라이언트의 요청을 실질적으로 처리하고 그에 맞는 응답을 반환하는 메소드이다. 
핸들러 메소드에는 "이 메소드는 OO 요청을 처리하는 거야~" 라고 알려주는 일종의 **태그**가 붙는다

그리고 **비슷한 태그**들이 붙은 핸들러 메소드들이 모여있는 클래스를 "컨트롤러"라고 부른다. 
컨트롤러과 핸들러 메소드는 일종의 포함관계이며, 컨트롤러 내에 여러 핸들러 메소드가 존재할 수 있다.

컨트롤러가 전체적인 요청-응답 흐름에 대한 묶음 처리를 담당한다면,
각 핸들러 메서드는 개별 요청을 구체적으로 처리하는 세부 구현 단위가 된다. 

요청을 처리하는데 서로 이해관계가 높지만, 세부 처리가 필요한 경우 컨트롤러로 묶일 수 있다.

## 핸들러 메소드 구조 

핸들러 메소드는 크게 3가지 구조로 이루어진다. 

앞서 핸들러 메소드에는 태그가 붙는다고 했던 것을 기억하는가?
이놈이 어떤 일을 처리할 수 있는지 

- 매핑정보, 요청, 응답으로 구성 

```java
@GetMapping("/boards/{boardId}") // 요청 매핑 정보
public String getBoard(
		@PathVariable Integer boardId // 요청
) {
	return boardId; // 응답
}
  

```


### Mapping Info


"요청 매핑" 은 요청(Request)과 요청을 처리하는 핸들러(컨트롤러)를 매핑하는 역할을 해준다.
- 스프링에서는 @RequestMapping 애노테이션을 사용하여 요청 매핑을 정의할 수 있음

요청 매핑은 클라이언트의 HTTP 요청이 어떤 핸들러 메서드로 매핑되는지를 결정합니다. 
요청 매핑은 URL 경로, HTTP 메서드, 특정 요청 헤더 등과 관련하여 요청을 처리할 핸들러 메서드를 결정합니다

spring 에서는 다음 어노테이션들로 핸들러 메서드와 매핑이 가능 + class(Controller)와 Method에 다 사용가능 

일종의 정보 + filter 역할을 겸함. 
![[Pasted image 20240318172114.png]]
- 정보
	- name : 뷰 템플릿에서 식별할 때 쓰는 이름, 템플릿 엔진을 사용하지 않고 뷰를 구현하면 그다지 사용할 일이 적음. 기본적으로 내부 생성해줌. (컨트롤러의 대분자 + # + 메서드 이름)
	- value, path : URI (기본 애트리뷰트, 바로 값을 입력해도됨. )
	- method : Http method (Ex, Get, post)
		- Http 메소드를 정해둔 일종의 shortcut 어노테이션을 제공
		- @GetMapping
		- @PutMapping
		- @PostMapping
		- @DeleteMapping
- 필터
	- params : 파라미터 검사 (필터 역할)
	- headers : 헤더 검사 (필터 역할)
	- consumes : 헤더의 Content-type 검사 (필터 역할)
	- produces : 헤더의 Accept 검사 (필터 역할)


### Request
핸들러 메소드가 받을 수 있는 요청들 

메소드 파라미터로 적어 넣을 수 있는 타입들은 다음과 같음. 
- `@RequestParam`, `@PathVariable`
	- 주로 많이 사용
	- `@RequestParam` 으로 지정하지 않아도, (컬렉션 타입이 아니면) `@RequestParam` 로 자동으로 인식됨, 하지만 지정할때와 안할때의 차이가 있음. 
		- required 인자의 차이
		- `@RequestParam Integer storeId`  이면 해당 값이 반드시 있어야 하지만 (true가 기본값)
		- `@RequestParam` 을 지정하지 않고, ` Integer storeId` 일 경우 해당값은 기본이 false임
- `@RequestBody` , `HttpEntity<B>`
	- 주로 많이 사용
- `@ModelAttribute`, `@SessionAttribute`, `@Model`, `@ModelMap`
	- 레거시에서 자주 쓰고자함.
- `@RequestHeader`, `@CookieValue`
	- 헤더정보를 주로 다룰때
- `ServletRequest`, `ServletRespsone`, `HttpSession`
	- jsp 프로그래밍에서 주로 사용 
- `WebRequest`, `NativeWebRequest`
- `Principal`, `Locale`, `TimeZone`, `InputStream`, `OutputStream`, `Reader`, `Writer` 등등
👉 [인자 타입 상세: 공식 문서 참고](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html) 



### Response 

핸들러 메소드가 보낼 수 있는 응답들 

메소드가 리턴할 수 있는 타입들은 다음과 같음 
- `ModelAndView`
	- 데이터와 view를 동시에 내려줌
- `String`, `View`
- `@ModelAttribute`, `Map`, `Model`
- `@RespsoneBody`
	- @RestController안에 @Controller같이 있는 어노테이션
	- Body에 평문으로 내보낼때, api로 데이터만 주고 받을때
- `HttpEntity<B>`, `ResponseEntity<B>`
	- `ResponseEntity<B>` 사용할때는 `@RespsoneBody` 를 지정안해도됨 
	- 동일한 동작을 하기 때문
- `HttpHeaders`
- `void` 등등 

👉 [리턴 타입 상세: 공식 문서 참고](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/return-types.html) 
