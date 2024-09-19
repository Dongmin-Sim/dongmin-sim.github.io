---
title: 서블릿이란? (Servlet)
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - spring
  - terminology
tags:
  - build
  - ci/cd
---
## 키워드
- 서블릿
- 자바
- 웹 어플리케이션

## 연관 개념
- [서블릿 컨테이너(Servlet Container)이란?]()
---

## 들어가며 

서블릿을 검색했을때 제일먼저 나오는 정의는 다음과 같다. 

> 자바 서블릿(Java Servlet)은 웹페이지를 동적으로 생성하는 서버 측 프로그램 혹은 그 사양을 말하며, 흔히 "서블릿"이라 불린다. - 위키피디아-

> 동적 웹 페이지를 만들 때 사용되는 자바 기반의 웹 애플리케이션 프로그래밍 기술

나는 처음 이 정의를 보았을때, 한글이 읽히기는 하지만, 정확히 서블릿이 무엇인지에 대해 와닿지는 않았다. 한마디로 서블릿의 정의가 어려웠다. "웹페이지를 동적으로 생성하는 서버측 프로그램 혹은 그 사양" 이게 대체 무슨말인가...

그래서 이 글에서는 
- 서블릿이 무엇인지 알고 싶은 사람, 
- 서블릿에 대해서 두루뭉실하게 어렴풋이 느껴져 답답한 사람

조금이라도 도움이 되고자 서블릿이란 무엇인지 내가 이해한 방식으로 설명해보고자 한다. 


결론부터 이야기 하자면 내가 내린 서블릿의 정의는 다음과 같다.

> 서블릿이란 요청을 **처리**하는 **주체**

이런 겨론을 내리게 되었는지 설명해보고자 한다.


## 단어 의미

- "서블릿(Servlet)"이라는 용어는 "서버(Servlet)"와 "릿(let)"이라는 두 단어의 합성어이다. 
	- "서버"는 요청에 대한 응답을 하는 컴퓨터나 프로그램을 가리키며, 
	- "릿"은 작은 프로그램이나 조각을 의미합니다. 


단순하게 단어의 조합에서 의미하는 "서블릿"이라는 용어는 서버에서 실행되는 작은 프로그램 혹은 프로그램 조각을 의미하는 듯 보인다.

## 정의
용어가 커버하는 넓이에 따라 분류한 여러 정의를 비교해보며 왜 위와 같은 정의를 내리게 되었는지 살펴보고자 한다.
### 넓은 의미

> **서블릿**은 **요청-응답(request-response)** 모델의 애플리케이션에서 특정 **요청**에 대한 **역할**을 수행하여 **응답**하는 객체

- 서블릿 정의에선 원칙적으로 모든 요청을 처리할 수 있지만, 아래와 같이 좁은 의미의 정의로 사용되는 경우가 많다. 
- (뒷받침 글 필요 - 설득력 있는.)

### 좁은 의미
> HTTP 프로토콜을 통해 웹서버에서 클라이언트의 요청을 받고 응답하기 위해 실행되는 작은 자바 프로그램 

- `javax.servlet.Servlet`, `jakarta.servlet.Servlet` 인터페이스를 구현한 클래스를 서블릿으로 지칭합니다.
- 그 중에서도 HTTP 프로토콜에서 사용하기 위한 서블릿을 구현한 것을 일반적으로 HTTP 프로토콜과 많이 사용되어 "서블릿"은 "HTTP 서블릿"을 의미하는 경우가 많습니다. 


![Desktop View](/assets/posts/spring/terminology/what-is-servlet/DefaultServlet.png){: width="972" height="589" .w-50}_이미지 설명_



다음은 Specification: JSR-340 Java Servlet 3.1 Specification ("Specification")에서 정의하는 서블릿이다. 
> A servlet is a Java™ technology-based Web component, managed by a container, that generates dynamic content. Like other Java technology-based components, servlets are platform-independent Java classes that are compiled to platform-neutral byte code that can be loaded dynamically into and run by a Java technology-enabled Web server. Containers, sometimes called servlet engines, are Web server extensions that provide servlet functionality. Servlets interact with Web clients via a request/response paradigm implemented by the servlet container. 

  
서블릿은 컨테이너에 의해 관리되는 Java™ 기반 웹 구성 요소로, 동적 콘텐츠를 생성합니다. 다른 Java 기술 기반 구성 요소와 마찬가지로 서블릿은 플랫폼에 독립적인 Java 클래스로 컴파일되어 플랫폼 중립적인 바이트 코드로 생성되며, Java 기술을 지원하는 웹 서버에서 동적으로 로드되어 실행될 수 있습니다.
컨테이너는 때로 서블릿 엔진이라고도 불리며, 서블릿 기능을 제공하는 웹 서버 확장 프로그램입니다. 서블릿은 서블릿 컨테이너에 의해 구현된 요청/응답 패러다임을 통해 웹 클라이언트와 상호 작용합니다.



결국, 이를 종합하여 내가 내린 아주 간략한 정의는 다음과 같다.

>서블릿은 요청을 **처리**하는 **주체**



## 등장 배경 & 필요목적

서블릿은 왜 필요해진걸까? 


[역사]

서블릿 등장 이전:

1. 정적 콘텐츠가 주류: 서버 측에서 클라이언트에 전달되는 콘텐츠는 주로 정적으로 생성되었습니다. 이는 미리 작성된 HTML 파일이나 다른 정적 자원이 서버에 저장되어 있어야 했습니다.
2. 상호작용 제한: 웹 애플리케이션은 주로 클라이언트와 상호작용하는 데 제한되었습니다. 사용자의 요청에 따른 동적인 응답 생성이 어려웠고, 복잡한 비즈니스 로직을 처리하기에는 적합하지 않았습니다.
3. 확장성의 한계: 기존의 방법으로는 웹 애플리케이션의 확장이 어려웠습니다. 새로운 기능 추가나 변경이 필요할 때마다 전체 애플리케이션을 다시 빌드하고 배포해야 했습니다.

결론적으로, 서블릿의 등장으로 웹 애플리케이션은 더욱 동적이고 확장 가능한 서비스로 발전하게 되었습니다. 사용자와의 상호작용과 개인화된 경험을 제공하는 데 있어서도 매우 강력한 도구로써 서블릿은 많은 이점을 제공합니다.
  
서블릿의 등장은 웹 애플리케이션 개발에 혁명적인 변화를 가져왔습니다. 이전에는 CGI(Common Gateway Interface)가 주로 사용되었는데, CGI는 각 요청마다 프로세스를 새로 생성하여 처리하므로 많은 부하를 유발했습니다. 또한 CGI 스크립트는 주로 Perl 또는 C와 같은 스크립트 언어로 작성되었는데, 이는 개발자가 서버 측 로직을 작성하는 데 제약을 가했습니다.

[초기 동적 cgi]

(CGI 이미지)

CGI(Common Gateway Interface)는 초기 웹에서 동적인 콘텐츠를 생성하기 위한 표준 인터페이스였습니다. CGI는 웹 서버와 외부 프로그램 사이의 통신을 담당하는 프로토콜로, 서버 측에서 클라이언트의 요청을 처리하고 응답을 생성하는 데 사용되었습니다.

CGI는 웹의 초기에 등장하여 정적인 HTML 페이지 이외의 동적인 콘텐츠를 생성하는 데 사용되었습니다. 이전에는 정적인 HTML 파일만을 서비스했기 때문에 사용자의 요청에 따른 동적인 응답을 생성하는 것이 어려웠습니다. CGI의 등장은 이러한 제한을 극복하고 동적인 콘텐츠를 생성하는 데 기여했습니다.

CGI는 클라이언트의 요청에 따라 서버 측에서 외부 프로그램을 실행하여 해당 프로그램이 동적으로 콘텐츠를 생성하고 서버에게 반환하는 방식으로 동작했습니다. 이때 외부 프로그램은 주로 Perl, C, Python 등의 스크립트 언어로 작성되었습니다.
https://live-everyday.tistory.com/197

[cgi 단점]
하지만 CGI는 매 요청마다 새로운 프로세스를 생성하고 실행하는 데에 있어서 성능적인 문제가 있었습니다. 이는 많은 오버헤드를 발생시키고 서버의 부하를 증가시키는 원인이 되었습니다.

또한 CGI는 보안 취약점을 가지고 있었습니다. 외부 프로그램이 서버의 파일 시스템에 직접 접근하거나 서버의 설정 파일 등에 영향을 미칠 수 있는 문제가 있었습니다.


[servlet 등장]
1997년, 제임스 던컨(Cloudscape 사의 공동 창업자이자 JDBC의 저자)과 제이슨 헌터(JavaServer Web Development Kit을 개발한 썬 마이크로시스템즈의 엔지니어)는 "서블릿"이라는 개념을 제안했습니다. 서블릿은 자바 언어를 기반으로 하여 CGI와는 달리 각 요청마다 새로운 프로세스를 생성하는 것이 아니라, 웹 서버 내에서 실행되어 훨씬 빠르고 효율적으로 동작합니다. 이는 자바의 플랫폼 독립성과 객체 지향성을 웹 애플리케이션 개발에 적용할 수 있게 해주었습니다.

서블릿은 1998년에 자바 플랫폼, 엔터프라이즈 에디션(Java EE, 이전에는 J2EE)의 일부로 발표되었습니다. 이후 서블릿은 지속적으로 발전하여 현재의 웹 개발 표준으로 자리를 잡게 되었습니다. 서블릿은 Java에서 가장 중요한 기술 중 하나로 여겨지며, 현재까지도 많은 웹 애플리케이션 및 웹 서비스에서 널리 사용되고 있습니다. 서블릿은 웹 애플리케이션 개발에서 표준이 되어가고 있으며, 그 발전은 웹 개발 역사의 중요한 이정표 중 하나로 기록될 만합니다.
  
[서블릿 cgi 대체]
이러한 제한과 단점으로 인해 CGI는 현재에는 더 나은 대안인 서블릿과 같은 기술로 대체되었습니다. 그러나 CGI는 웹 기술의 발전에 큰 역할을 한 것으로 평가됩니다.


[서블릿 장점]
서블릿 등장 이후:

1. 동적 콘텐츠 생성 가능: 서블릿을 사용하여 웹 애플리케이션은 클라이언트의 요청에 따라 동적으로 콘텐츠를 생성할 수 있게 되었습니다. 이는 사용자에게 더 많은 상호작용 및 개인화된 경험을 제공할 수 있게 되었습니다.
2. 클라이언트-서버 상호작용 강화: 서블릿은 클라이언트와 서버 간의 효율적인 상호작용을 가능하게 합니다. 데이터베이스 조회, 외부 서비스 호출 등과 같은 복잡한 작업을 처리할 수 있으며, 클라이언트의 요청에 신속하게 응답할 수 있습니다.
3. 확장성 및 유지보수 용이성 향상: 서블릿을 사용하면 웹 애플리케이션의 확장이 용이해졌습니다. 새로운 기능을 추가하거나 변경할 때마다 개별 서블릿을 수정하고 다시 컴파일하여 애플리케이션을 다시 빌드하고 배포할 필요가 없어졌습니다. 이는 애플리케이션의 유지보수와 개선을 훨씬 효율적으로 만들어 줍니다.


## 역할

- 웹 프로그래밍에서 서블릿은 HTTP 요청을 받아들이고, 해당 요청을 처리하여 HTTP 응답을 생성하는 데 사용됩니다. 
- 위와 역할을 수행하기 위해 javax.servlet.Servlet 인터페이스를 구현한 클래스들을 서블릿이라 일컫습니다.
![Desktop View](/assets/posts/spring/terminology/what-is-servlet/servlet-interface.png){: width="972" height="589"}_servlet interface_


주요 메서드로는 다음이 있습니다
- init(): 서블릿의 초기화를 수행하는 메서드입니다.
- service(): 클라이언트의 HTTP 요청을 처리하고 HTTP 응답을 생성하는 메서드입니다.
- destroy(): 서블릿의 종료 시점에 호출되어 리소스를 정리하는 메서드입니다.


## 서블릿 인터페이스

서블릿 인터페이스는 서블릿 컨테이너에 의해 구현되는 클래스들이 준수해야 하는 규약(인터페이스)을 정의합니다.
이 인터페이스는 서블릿의 생명주기(lifecycle)를 관리하고 HTTP 요청을 처리하며, HTTP 응답을 생성하는 메서드를 정의합니다.


아니요, 서블릿은 물리적인 개념입니다. 서블릿은 자바 언어를 기반으로 한 웹 애플리케이션의 동적인 컴포넌트를 구현하는 자바 클래스입니다. 이 클래스는 서블릿 컨테이너에 의해 인스턴스화되고 실행되며, 클라이언트의 요청을 받아들이고 처리하는 역할을 수행합니다. 따라서 서블릿은 웹 애플리케이션의 동적인 부분을 구현하는 자바 클래스로, 논리적이지 않고 실제적인 개념입니다.

. 이러한 서블릿은 웹 서버와 웹 애플리케이션 서버에서 동적인 웹 페이지를 생성하고 관리하는 데 사용됩니다.
javax.servlet.Servlet 인터페이스는 

서블릿은 자바의 표준 웹 컴포넌트이며, 서블릿 컨테이너에서 실행됩니다. 반면에 `@Controller` 어노테이션은 스프링 프레임워크에서 제공하는 어노테이션으로, 스프링 MVC에서 사용되는 컨트롤러를 정의할 때 사용됩니다.

`@Controller` 어노테이션이 붙은 클래스는 스프링의 빈으로 등록되어 스프링 컨테이너에서 관리됩니다. 스프링 MVC에서는 클라이언트의 요청을 받아들이고 처리하는 역할을 수행하며, 서블릿보다 더 추상화된 수준의 개념입니다. 서블릿은 직접적으로 HTTP 요청과 응답을 처리하는 반면에, `@Controller`는 스프링 MVC 프레임워크에서 HTTP 요청을 처리하기 위한 논리적인 컴포넌트입니다.



## 서블릿의 라이프 사이클

(생명주기 이미지)

The lifecycle of a servlet is controlled by the servlet container in which the servlet has been deployed. When a request is mapped to a servlet, the servlet container performs the following steps:

1. If an instance of the servlet does not exist, the servlet container:
    
    1. Loads the servlet class
        
    2. Creates an instance of the servlet class
        
    3. Initializes the servlet instance by calling the `init` method
        
    
2. The servlet container invokes the `service` method, passing request and response objects.
    

Initialization is covered in [[creating-and-initializing-a-servlet]](https://jakarta.ee/learn/docs/jakartaee-tutorial/current/web/servlets/servlets.html#creating-and-initializing-a-servlet). Service methods are discussed in [[writing-service-methods]](https://jakarta.ee/learn/docs/jakartaee-tutorial/current/web/servlets/servlets.html#writing-service-methods).

If it needs to remove the servlet, the servlet container finalizes the servlet by calling the servlet’s `destroy` method. For more information, see [[finalizing-a-servlet]](https://jakarta.ee/learn/docs/jakartaee-tutorial/current/web/servlets/servlets.html#finalizing-a-servlet).



## 참고자료 
> A **servlet** is a Java programming language class that is used to extend the capabilities of servers that host applications accessed by means of a request-response programming model 
- [What is a servlet](https://docs.oracle.com/javaee/5/tutorial/doc/bnafe.html)


https://jakarta.ee/learn/docs/jakartaee-tutorial/current/web/servlets/servlets.html#_what_is_a_servlet


- 서블릿 API( [JSR 340](https://www.jcp.org/en/jsr/detail?id=340) )
chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://download.oracle.com/otn-pub/jcp/servlet-3_1-fr-eval-spec/servlet-3_1-final.pdf?AuthParam=1711691390_5ea063365bd75187f9a56a61c4490b83