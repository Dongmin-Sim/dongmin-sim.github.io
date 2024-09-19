---
title: Spring Web MVC 구조 이해
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - Spring
  - webMVC
tags:
  - build
  - ci/cd
image: 
path: /assets/img/thumbnail/gradle.png
lqip: 
alt: Gradle이란?
---

## 들어가며


![Desktop View](/assets/posts/spring/mvc/spring-web-mvc-architecture.png){: width="972" height="589"}_이미지 설명_


### 전체 구조


### 서블릿 컨테이너

서블릿 컨테이너는 웹 애플리케이션 서버 내에서 서블릿의 생명주기를 관리하고, 서블릿이 클라이언트의 요청에 응답할 수 있도록 지원하는 컴포넌트입니다. 이는 서블릿을 실행하고 관리하는 환경을 제공하며, 서블릿이 서버와 통신할 수 있도록 네트워크 요청과 응답을 처리합니다. 서블릿 컨테이너는 일반적으로 웹 서버나 애플리케이션 서버에 내장되어 있으며, 웹 애플리케이션의 배포, 실행, 관리 등을 담당합니다. 대표적인 서블릿 컨테이너로는 Apache Tomcat, Jetty, Undertow 등이 있습니다.

부트를 실행하면 서블릿 컨테이너가 서블릿을 생성해둠.

웹 브라우저에서 http 요청이오면 was 서버, 서블릿 컨테이너가 request 객체와 respsone 객체를 생성하고 
안에 값을 넣어준다음 서블릿에게 request, response 객체를 전달하면서 싱글톤인 서블릿을 호출 (service 메서드)

그리고 서블릿의 .service() 메서드가 실행되면 필요한 작업을 수행 후

response에 데이터를 넣고 종료되면 was 서버가 response 객체 정보를 가지고 HTTP 응답메시지를 만들어서 웹브라우저에 다시 반환해줌

request = org.apache.catalina.connector.RequestFacade@592f706c

HttpServletRequest 는 인터페이스

상속 받은 ServletRequest도 인터페이스

org.apache.catalina.connector.RequestFacade@592f706c 일종의 tomcat, jetty was 서버의 인터페이스 구현체

### 스프링 컨테이너

네, 맞습니다. 서블릿 컨테이너는 스프링 프레임워크와는 별개의 개념입니다.

서블릿 컨테이너는 웹 애플리케이션의 동적인 컴포넌트인 서블릿의 생명주기를 관리하고, 서블릿이 클라이언트의 요청을 처리할 수 있도록 지원하는 소프트웨어 컴포넌트입니다. 대표적으로 Apache Tomcat, Jetty, Undertow 등이 있습니다.

반면에 스프링 프레임워크는 자바 기반의 엔터프라이즈 애플리케이션을 개발하기 위한 오픈 소스 프레임워크로, 의존성 주입(Dependency Injection), AOP(Aspect-Oriented Programming), 트랜잭션 관리 등의 기능을 제공합니다. 스프링은 서블릿 컨테이너 위에서 동작할 수도 있지만, 서블릿 컨테이너와는 별개의 레이어로 동작합니다.

Spring Boot에서의 서블릿 컨테이너와 스프링 프레임워크는 같은 레이어입니다. 서블릿 컨테이너는 스프링 프레임워크의 일부로 내장되어 있으며, 스프링 Boot는 애플리케이션을 빠르게 개발하고 실행하기 위한 스프링의 확장입니다. 따라서 Spring Boot 애플리케이션의 내부에서 서블릿 컨테이너는 스프링 프레임워크와 함께 같은 레이어로 동작하며, 서로 연동되어 애플리케이션을 구동합니다.

네, 일반적으로 스프링 프레임워크에서는 서블릿 컨테이너와 스프링 프레임워크가 서로 다른 레이어입니다.

- **서블릿 컨테이너**: 서블릿 컨테이너는 웹 애플리케이션의 동적인 컴포넌트인 서블릿의 실행을 관리합니다. 대표적으로 Apache Tomcat, Jetty, Undertow 등이 있습니다.
- **스프링 프레임워크**: 스프링 프레임워크는 자바 기반의 엔터프라이즈 애플리케이션을 개발하기 위한 프레임워크로, 의존성 주입(Dependency Injection), AOP(Aspect-Oriented Programming), 트랜잭션 관리 등의 기능을 제공합니다.

일반적으로 스프링 프레임워크에서는 서블릿 컨테이너 위에서 동작할 수 있지만, 서블릿 컨테이너와는 별개의 레이어로 동작합니다. 스프링 프레임워크에서는 서블릿 컨테이너가 웹 애플리케이션의 동적인 컴포넌트를 실행하고, 스프링 프레임워크가 애플리케이션의 비즈니스 로직을 담당합니다.

### 애플리케이션 컨텍스트

`BeanFactory` 인터페이스를 상속받은 인터페이스

```java
public interface BeanFactory {
    String FACTORY_BEAN_PREFIX = "&";

    Object getBean(String name) throws BeansException;
    <T> T getBean(String name, Class<T> requiredType) throws BeansException;
    Object getBean(String name, Object... args) throws BeansException;
    <T> T getBean(Class<T> requiredType) throws BeansException;
    <T> T getBean(Class<T> requiredType, Object... args) throws BeansException;

    <T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);
    <T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);

    boolean containsBean(String name);
    boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
    boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
    boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;
    boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;

    @Nullable
    Class<?> getType(String name) throws NoSuchBeanDefinitionException;
    @Nullable
    Class<?> getType(String name, boolean allowFactoryBeanInit) throws NoSuchBeanDefinitionException;

    String[] getAliases(String name);
}
```

-
- 스프링 컨테이너의 최상위 인터페이스
- 스프링 **빈을 관리하고 조회**하는 역할을 담당.
- `getBean()` 제공

`ApplicationContext`

- BeanFactory 에서 제공하는 기능(빈을 관리하고 조회) + 여러 부가 기능을 제공한다.
- BeanFactory나 `ApplicationContext` 보통 스프링 컨테이너라고 함.

```java

public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory, MessageSource, ApplicationEventPublisher, ResourcePatternResolver {
    @Nullable
    String getId();
    String getApplicationName();
    String getDisplayName();
    long getStartupDate();

    @Nullable
    ApplicationContext getParent();

    AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException;
}
```

스프링 애플리케이션을 구성하는 빈(bean)들의 설정 정보를 담고 있으며, 런타임 시에 빈들을 관리하고 제공합니다. 이것은 스프링 애플리케이션의 핵심적인 컨테이너 역할을 수행합니다.

1. 일반적인 애플리케이션 컨텍스트 (GenericApplicationContext): 가장 일반적으로 사용되며, 기본적인 스프링 애플리케이션 컨텍스트입니다.
2. 웹 애플리케이션 컨텍스트 (WebApplicationContext): 웹 애플리케이션에서 사용되며, 웹 환경에서의 특정한 기능을 지원합니다.
3. 테스트 애플리케이션 컨텍스트 (TestApplicationContext): 테스트를 위한 애플리케이션 컨텍스트로, 테스트 환경에서 스프링 애플리케이션을 설정하고 실행하는 데 사용됩니다.

이러한 종류의 애플리케이션 컨텍스트는 스프링 프레임워크의 다양한 사용 사례와 환경에 맞게 제공됩니다.

### AnnotationConfigWebApplicationContext

스프링 애플리케이션에서 Java Config를 사용하여 빈을 정의하는 데 사용되는 클래스입니다. 이 클래스는 ApplicationContext 인터페이스의 구현체 중 하나이며, 주로 스프링 MVC와 함께 사용됩니다.

AnnotationConfigWebApplicationContext는 다음과 같은 역할을 수행합니다:

1. **Java Config 클래스 로드**: AnnotationConfigWebApplicationContext는 설정 정보가 포함된 Java Config 클래스를 로드합니다. 이 Java Config 클래스에는 `@Configuration` 어노테이션이 붙은 클래스가 포함됩니다. 이 클래스에서 스프링 빈을 정의하고 구성합니다.
2. **빈 등록**: AnnotationConfigWebApplicationContext는 Java Config 클래스에서 정의한 빈을 등록하고 관리합니다. `@Bean` 어노테이션이 붙은 메서드에서 반환된 객체들을 스프링 빈으로 등록합니다.
3. **의존성 주입**: AnnotationConfigWebApplicationContext는 스프링 애플리케이션에서 필요로 하는 빈들 간의 의존성을 주입합니다. 이는 `@Autowired` 어노테이션을 사용하여 의존성을 주입받는 방식으로 이루어집니다.
4. **기타 스프링 기능 제공**: AnnotationConfigWebApplicationContext는 스프링에서 제공하는 다양한 기능들을 사용할 수 있도록 해줍니다. 이는 AOP(Aspect-Oriented Programming), 이벤트 처리, 메시지 소스, 프로퍼티 파일 로딩 등의 기능을 포함합니다.

요약하면, AnnotationConfigWebApplicationContext는 Java Config를 사용하여 스프링 애플리케이션을 설정하고 구성하는 데 사용되며, 이를 통해 스프링의 다양한 기능들을 활용할 수 있도록 합니다.

### Web application context 구분

![[Pasted image 20240319163846.png]]

### 서블릿 WebApplicationContext

각 서블릿은 자신이 담당하는 기능에 필요한 빈들을 각각의 servlet WebApplicationContext에서 로드하고 사용할 수 있습니다. 이렇게 하면 각 서블릿은 자신의 독립적인 스프링 컨텍스트를 가지게 되어 서로 영향을 주지 않으면서 스프링의 다양한 기능을 활용할 수 있습니다.

root WebApplicationContext의 예시로는 데이터베이스 연결 설정을 들 수 있습니다. 웹 애플리케이션 전역에서 사용되는 데이터베이스 연결을 설정하고 관리하는 빈들은 root WebApplicationContext에서 로드됩니다. 이러한 설정은 모든 서블릿에서 공통으로 사용되므로, 데이터베이스 연결 설정을 한 번만 정의하고 모든 서블릿에서 사용할 수 있습니다.

### Root WebApplicationContext

서블릿 WebApplicationContext는 웹 애플리케이션 내에서 특정 서블릿에 대한 스프링 컨텍스트를 관리하는 역할을 합니다. 각 서블릿마다 자체적인 WebApplicationContext가 생성되어 해당 서블릿에 필요한 빈(bean)들을 로드하고 관리합니다. 이렇게 함으로써 각 서블릿은 자신의 독립적인 환경을 갖게 되어 서로 영향을 주지 않으면서 스프링 기능을 활용할 수 있습니다.

반면에 root WebApplicationContext는 웹 애플리케이션의 전역적인 스프링 컨텍스트를 관리합니다. 이 컨텍스트는 모든 서블릿이 공유하는 빈들을 로드하고 관리하며, 주로 데이터베이스 연결과 같은 전역적인 설정을 담당합니다. 모든 서블릿에서 공통으로 사용되는 빈들을 포함하므로, 서블릿 간의 의존성 주입(Dependency Injection) 및 데이터 공유를 쉽게 할 수 있습니다.

서블릿 WebApplicationContext의 예시로는 하나의 웹 애플리케이션 내에 여러 서블릿이 존재하는 경우를 들 수 있습니다. 예를 들어, 온라인 쇼핑몰 웹 애플리케이션에서는 상품을 조회하는 서블릿, 장바구니를 관리하는 서블릿, 주문을 처리하는 서블릿 등이 각각 존재할 수 있습니다. 각 서블릿은 자신이 담당하는 기능에 필요한 빈들을 각각의 WebApplicationContext에서 로드하고 사용할 수 있습니다. 이렇게 하면 각 서블릿은 자신의 독립적인 스프링 컨텍스트를 가지게 되어 서로 영향을 주지 않으면서 스프링의 다양한 기능을 활용할 수 있습니다.

반면에 root WebApplicationContext의 예시로는 데이터베이스 연결 설정을 들 수 있습니다. 웹 애플리케이션 전역에서 사용되는 데이터베이스 연결을 설정하고 관리하는 빈들은 root WebApplicationContext에서 로드됩니다. 이러한 설정은 모든 서블릿에서 공통으로 사용되므로, 데이터베이스 연결 설정을 한 번만 정의하고 모든 서블릿에서 사용할 수 있습니다.

---

이는 서블릿마다 별도의 스프링 컨텍스트를 가지고 있다고 생각할 수 있습니다. 이는 마치 각 서블릿이 자신만의 작은 스프링 컨테이너를 가지고 있는 것과 같습니다. 각 서블릿의 WebApplicationContext는 해당 서블릿에 필요한 빈들을 로드하고 관리합니다. 이렇게 하면 각 서블릿이 서로 영향을 주지 않고 독립적으로 동작할 수 있습니다. 따라서 서블릿 간의 충돌이나 의존성 문제를 방지하면서, 각각의 서블릿이 자신의 역할을 수행할 수 있도록 해줍니다.
서블릿들은 자신의 WebApplicationContext에서 빈을 로드하고 관리합니다. 이것은 각 서블릿마다 독립적으로 생성되는 스프링 컨텍스트입니다.

```java
public interface WebApplicationContext extends ApplicationContext {
    String ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE = WebApplicationContext.class.getName() + ".ROOT";
    String SCOPE_REQUEST = "request";
    String SCOPE_SESSION = "session";
    String SCOPE_APPLICATION = "application";
    String SERVLET_CONTEXT_BEAN_NAME = "servletContext";
    String CONTEXT_PARAMETERS_BEAN_NAME = "contextParameters";
    String CONTEXT_ATTRIBUTES_BEAN_NAME = "contextAttributes";

    @Nullable
    ServletContext getServletContext();
}
```

네, 사용자가 상품을 조회하는 상황을 예시로 살펴보겠습니다.

1. 클라이언트가 웹 브라우저를 통해 상품 조회 페이지에 접속합니다.
2. 웹 브라우저는 HTTP 요청을 생성하고, 해당 요청은 웹 서버로 전송됩니다.
3. 웹 서버는 해당 요청을 받아들이고, 서블릿 컨테이너에게 요청을 전달합니다.
4. 서블릿 컨테이너는 요청을 처리하기 위해 상품 조회 서블릿을 호출합니다.
5. 각 서블릿은 자체적으로 독립적인 WebApplicationContext를 가지고 있습니다. 상품 조회 서블릿의 WebApplicationContext에서는 해당 서블릿이 필요로 하는 빈들을 로드하고 준비합니다. 예를 들어, 데이터베이스 접근을 위한 DAO 빈, 상품 관련 서비스 빈 등이 있을 수 있습니다.
6. 상품 조회 서블릿은 상품 정보를 데이터베이스에서 조회하기 위해 데이터베이스 관련 빈을 사용합니다.
7. 데이터베이스 관련 빈은 데이터베이스와의 연결을 설정하고, 데이터베이스에서 상품 정보를 조회하여 서블릿에게 반환합니다.
8. 상품 조회 서블릿은 상품 정보를 받아 화면에 출력하기 위해 적절한 뷰 빈을 사용합니다.
9. 뷰 빈은 상품 정보를 받아 HTML 형식으로 화면에 출력할 수 있도록 처리합니다.
10. 최종적으로 서블릿은 클라이언트에게 HTTP 응답을 생성하고, 해당 응답은 웹 서버를 거쳐 웹 브라우저에게 전송됩니다.
11. 웹 브라우저는 받은 HTTP 응답을 해석하여 사용자에게 상품 정보가 출력된 웹 페이지를 표시합니다.

이러한 과정을 통해 사용자는 웹 브라우저를 통해 상품을 조회할 수 있게 됩니다. 각 서블릿은 자신의 WebApplicationContext를 통해 필요한 빈들을 로드하고 사용하여 독립적으로 동작합니다.

### 컴포넌트

스프링 프레임워크에서의 "컴포넌트(Component)"는 애플리케이션의 구성 요소를 의미합니다. 스프링에서 컴포넌트는 주로 비즈니스 로직을 담당하는 객체를 가리키며, 이러한 객체들은 스프링 컨테이너에 의해 관리됩니다. 스프링에서는 다음과 같은 방식으로 컴포넌트를 정의하고 사용합니다:

1. **@Component 어노테이션**: 스프링에서 컴포넌트를 정의할 때 주로 사용되는 방법 중 하나입니다. 이 어노테이션을 사용하여 해당 클래스를 스프링의 컴포넌트로 등록할 수 있습니다.
2. **@Controller, @Service, @Repository 어노테이션**: 각각 웹 애플리케이션의 컨트롤러, 서비스, 리포지토리(데이터 접근 객체)를 정의할 때 사용됩니다. 이러한 어노테이션들은 내부적으로 @Component 어노테이션을 포함하고 있으며, 보다 명확한 의미를 부여하기 위해 사용됩니다.
3. XML 설정 파일: 스프링의 XML 설정 파일을 사용하여 컴포넌트를 정의할 수도 있습니다. 이 방식은 어노테이션을 사용하지 않고 컴포넌트를 정의하는 전통적인 방법입니다.

컴포넌트는 스프링 애플리케이션의 주요 구성 요소로, 비즈니스 로직을 포함하고 있으며, 스프링 컨테이너에 의해 관리되어 의존성 주입(Dependency Injection)과 같은 기능을 통해 사용됩니다.

### 빈

네, "컴포넌트"와 "빈"은 스프링 프레임워크에서 다른 개념입니다.

- **컴포넌트(Component)**: 스프링 애플리케이션의 구성 요소를 가리킵니다. 주로 비즈니스 로직을 담당하는 객체를 의미하며, 스프링에서는 주로 @Component, @Controller, @Service, @Repository 어노테이션 등을 사용하여 컴포넌트를 정의합니다.
- **빈(Bean)**: 스프링 컨테이너에 의해 생성되고 관리되는 객체를 가리킵니다. 빈은 스프링에서 제어의 역전(Inversion of Control)과 의존성 주입(Dependency Injection)을 통해 관리되며, 주로 컴포넌트를 포함하여 스프링 애플리케이션의 모든 객체를 가리킵니다.

즉, 컴포넌트는 애플리케이션의 구성 요소를 나타내는 개념이며, 빈은 스프링 컨테이너에 의해 관리되는 객체를 지칭하는 개념입니다. 일반적으로 스프링에서 컴포넌트는 빈으로 등록되어 스프링의 기능을 활용할 수 있습니다.
