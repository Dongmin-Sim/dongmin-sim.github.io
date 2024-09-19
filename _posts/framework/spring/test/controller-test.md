---
title: Spring, Controller 레이어 단위 테스트
author: codongmin
date: 2024-05-29T19:27:00
categories: 
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며

테스트 컨트롤러 테스트란? 
컨트롤러란 


## 테스트 주요 목적 

내가 생각하는 Controller 단에서의 주요 테스트 목적은 다음과 같다. 
Controller의 목적은 요청에 맞는 적절한 핸들러를 연결해주는 것이다.
그렇다면 컨트롤러 테스트에서 집중해야 하는 부분도 그 목적에 해당하는 부분이 아닐까?

- HTTP Request와 handler mapping 여부 
	- request에 맞는 핸들러가 매핑되는지
- Request Parameter, Body 값 검증
	- type 등이 맞는지? parsing이 잘되는지
	- 중복이 되는가 이런거는 컨트롤러에서 X
- Service Layer의 정상적인 호출
- HTTP Response를 의도한 대로 전달하는지 여부



(컨트롤러의 요청과 응답 을 담당하는 적절한 이미지 추가)

기본적인 요청과 응답에 & 요청과 응답에 대한 세부 요구사항에 대한 테스트가 이루어져야 한다. 
결국 테스트를 하려면 테스트하고자 하는 의도가 명확해야한다.

이를 세부적으로 질문해보면 다음과 같다. 

- **HTTP 요청과 응답의 유효성** 
	- 요청유효성(Request Validation)
		- 요청이 올바른 URL로 전송되었는가?
	    - 요청의 컨텐츠 유형이 올바른가? (예: JSON, XML 등)

	- 응답 유효성(Response Validation)
		- 응답의 상태 코드가 올바른가? (예: 200 OK, 404 Not Found 등)
	    - 응답의 컨텐츠 유형이 올바른가? (예: JSON, XML 등)
	    - 응답 데이터가 예상한 형식과 일치하는가?
	        - 응답 body에 필요한 데이터가 모두 포함되어 있는가?
	        - 응답 body의 데이터가 정확한가?

- **파라미터 및 경로 변수 유효성 체크**
	- 파라미터(Parameter) 유효성 
		- Query String
			- 쿼리 문자열의 각 파라미터가 유효한가?
			- 각 파라미터의 값이 예상한 형식과 일치하는가?
		- Request Body가 유효한가
			- 요청 body의 유효성을 검사하는 항목이 적절한가?
			- 요청 데이터가 예상한 형식과 일치하는가?
		
	- 경로 변수(Path Variable) 유효성
		- 경로 변수의 값이 유효한가?

- **예외처리**
	- 요청 예외
		- 요청이 잘못된 URL로 전송되었을 때 의도된 예외가 발생하는가?
		- Path Variable 값이 유효하지 않을 때 의도된 예외가 발생하는가?
		- 
	- 응답 예외
		- 

## 테스트 목표 예시 

간단한 생성 api를 테스트 하는 상황을 가정해본다. 



## 어떻게 

### @WebMvcTest

Annotation that can be used for a Spring MVC test that focuses only on Spring MVC components.

Using this annotation will disable full auto-configuration and instead apply only configuration relevant to MVC tests (i.e. @Controller, @ControllerAdvice, @JsonComponent, Converter/GenericConverter, Filter, WebMvcConfigurer and HandlerMethodArgumentResolver beans but not @Component, @Service or @Repository beans).

By default, tests annotated with @WebMvcTest will also auto-configure Spring Security and MockMvc (include support for HtmlUnit WebClient and Selenium WebDriver). For more fine-grained control of MockMVC the @AutoConfigureMockMvc annotation can be used.
Typically @WebMvcTest is used in combination with @MockBean or @Import to create any collaborators required by your @Controller beans.

If you are looking to load your full application configuration and use MockMVC, you should consider @SpringBootTest combined with @AutoConfigureMockMvc rather than this annotation.
When using JUnit 4, this annotation should be used in combination with @RunWith(SpringRunner.class)

### mockMvc


## 예시 상황

(상황 설명 그림)

- 테스트 대상
- 테스트 요구사항(목표) 
	- (언제 어떤)



## 사용 예시 




```java
  
@WebMvcTest(LocationApiControllerTest.class)  
class LocationApiControllerTest {  
  
    private final MockMvc mockMvc;  
    private final ObjectMapper mapper;  
  
    public LocationApiControllerTest(  
            @Autowired MockMvc mockMvc,  
            @Autowired ObjectMapper mapper) {  
        this.mockMvc = mockMvc;  
        this.mapper = mapper;  
    }  
  
    @DisplayName("[POST] /api/locations: SUCC_장소 생성 - 생성된 장소 단건 정보를 담은 표준 API 응답 리턴")  
    @Test  
    void givenLocation_whenRegisterLocation_thenReturnLocationInApiResponseOk() throws Exception{  
        // Given  
        LocationRegisterRequest location = LocationRegisterRequest.builder()  
                .name("서부 창고")  
                .memo("서울시 강서구")  
                .build();  
  
        given(LocationBusiness.register(any()))  
                .willReturn(createLocationResponse());  
  
        // When  
        mockMvc.perform(  
                post("/api/locations")  
                        .contentType(MediaType.APPLICATION_JSON)  
                        .content(mapper.writeValueAsString(location))  
        );
```




테스트 코드의 대상 객체가 다른 빈의 의존성을 필요로 하는 경우에
@MockBean 어노테이션으로 테스트 코드에서 의존성이 필요한 빈을 주입받을 수 있다. 

controller(webmvc, mockbean) 테스트 같은 경우 service (mockito) 테스트에서 사용한 것 보다 느리다. 스프링 애플리케이션 컨텍스트를 활용하기 때문, 오토 컨피규레이션의 기능을 활용하기 위해 
```java
@InjectMocks private EventService sut; // system under test 관례 , 테스트 대상을 의미 
@Mock EventRepository eventRepository;
```
mock bean을 사용하게되면, 
스프링 어노테이션을 활용하는 테스트에서 목빈을 사용하게 되면, 
그 해당 어플리케이션 **컨텍스트**의 캐시가 오염이 됨. drity context
그래서 반드시 , 그 해당 테스트의 이전, 이후 테스트는 새로 컨텍스트롤 로드 (스프링 어플리케이션을 다시 실행)해주어야 함. : 터디 컨택스트를 다시 갱신하고 로드해우저야 함.

아직까지는 이러한 해당 방식을 피하는 방법은 없고, 전체적으로 하나의 테스트 환경을 구성하거나, 목빈을 최대한 사용하지 않는 방법등이 있음. 


   
## 마무리

결국 테스트 코드를 작성한다는 것은 어떤 요청이 오기를 원하는지, 어떤 응답을 내려주길 원하는지 , 요청과 응답에 대한 정확한 명세를 상세화하는 과정과도 비슷하다고 생각한다. 이를 통해 테스트와 구현 코드가 서로 발전하는 선순환이 테스트코드를 작성하는 이점이지 않을까 싶다.