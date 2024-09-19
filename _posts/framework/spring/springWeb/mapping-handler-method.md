---
title: mapping 방법
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






### PathVariable

단순 매핑
```java
/**  
 * PathVariable 사용  
 * 변수명이 같으면 생략 가능  
 * @param userId -> String userId  
 * @return userId  
 */@GetMapping("/mapping/{userId}")  
public String mappingPath(  
        @PathVariable String userId  
) {  
    log.info("mappingPath userId={}", userId);  
    return userId;  
}
```

다중 매핑
```java
@GetMapping("/mapping/users/{userId}/orders/{order}")  
public String mappingPath2(  
        @PathVariable String userId,  
        @PathVariable(name = "order") String orderId  
) {  
    log.info("mappingPath userId={}, orderId={}", userId, orderId);  
    return userId + " " + orderId;  
}
```

###  Param

특정 param 조건
```java
/**  
 * 파라미터로 추가 매핑  
 * params="mode",  
 * params="!mode" * params="mode=debug" * params="mode!=debug" * parmas= {"mode=debug", "data=good"} * @return  
 */  
@GetMapping(value = "/mapping-param", params = "mode")  
public String mappingParam() {  
    log.info("mappingParam");  
    return "ok";  
}
```

###  Header
특정 헤더 조건
```java
/**  
 * 파라미터로 추가 매핑  
 * headers="mode",  
 * headers="!mode" 
 * headers="mode=debug" 
 * headers="mode!=debug" 
 * headers= {"mode=debug", "data=good"}
 */  
@GetMapping(value = "/mapping-header", headers = "mode=debug")  
public String mappingHeader() {  
    log.info("mappingHeader");  
    return "ok";  
}
```

미디어 타입 조건 (consume) HTTP 요청 Content-Type 헤더  (서버쪽에서 받을 수 있는 타입) 서버에서 소비
```java
/**
*
*/
@PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)  
public String mappingConsumes() {  
    log.info("mappingConsumes");  
    return "ok";  
}  

```


미디어 타입 조건 (produces) HTTP 요청 Accept 헤더 (클라쪽에서 받을 수 있는 타입) 서버에서 생산
```java
@PostMapping(value = "/mapping-produce", produces = MediaType.TEXT_PLAIN_VALUE)  
public String mappingProduces() {  
    log.info("mappingProduces");  
    return "ok";  
}
```



### HTTP 요청 조회 (header, 기본값 조회)

```java
package inflearn.springMVC.basic.request;  
  
import jakarta.servlet.http.Cookie;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.cglib.core.Local;  
import org.springframework.http.HttpMethod;  
import org.springframework.util.MultiValueMap;  
import org.springframework.web.bind.annotation.CookieValue;  
import org.springframework.web.bind.annotation.RequestHeader;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
import java.net.http.HttpResponse;  
import java.util.Locale;  
  
@Slf4j  
@RestController  
public class RequestHeaderController {  
  
    @RequestMapping("/headers")  
    public String headers(  
            HttpServletRequest request,  
            HttpServletResponse response,  
            HttpMethod httpMethod,  
            Locale locale,  
            @RequestHeader  
            MultiValueMap<String, String> headerMap,  
            @RequestHeader("host") String host,  
            @CookieValue(value = "myCookie", required = false) Cookie cookie  
    ) {  
  
        log.info("request={}", request);  
        log.info("response={}", response);  
        log.info("httpMethod={}", httpMethod);  
        log.info("locale={}", locale);  
        log.info("headerMap={}", headerMap);  
        log.info("host={}", host);  
        log.info("cookie={}", cookie);  
        String value = cookie.getValue();  
        String myCookie = cookie.getAttribute("myCookie");  
        log.info("cookie, myCookie = {}", myCookie);  
        log.info("cookie, value = {}", value);  
        return "ok";  
    }  
}
```

- `locale` locale 정보
- `@RequestHeader  MultiValueMap<String, String> headerMap`
	- 모든 HTTP Header를 MultiValueMap 형식으로 조회한다.
	- MultiValueMap 은 Map과 유사하나, 하나의 키에 여러 값을 받을 수 있다. 
		- HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용된다.
		- "keyA=value1&keyA=value2"

```java
MultiValueMap<String, String> multiValueMap = new LinkedMultiValueMap<>();  
multiValueMap.add("key1", "value1");  
multiValueMap.add("key1", "value2");  
  
List<String> keyList = multiValueMap.get("key1");
```

```
headerMap={keya=[value1, value2], user-agent=[PostmanRuntime/7.37.0], accept=[*/*], cache-control=[no-cache], postman-token=[60a89f79-3165-44c1-8399-ec176fbf4f19], host=[localhost:8080], 
```

가능한 handler method 인자값 argument value
- https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html

### HTTP 요청 조회 (데이터 조회)












가능한 handler method 반환 값 return value
https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/return-types.html