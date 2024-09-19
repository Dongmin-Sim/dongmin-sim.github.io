---
title: 함수형 인터페이스로 테스트 코드 리팩토링하기
author: codongmin
date: 2024-06-03T15:01:00
categories:
  - spring
  - test
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
preview: 테스트 코드를 리팩토링하는 방법 중 함수형 인터페이스를 이용해보는 케이스를 다룹니다.
---

## 들어가며

테스트 코드도 프로덕션 코드만큼이나 관리가 필요하다. 프로덕션 코드가 리팩토링되는 만큼 그에 맞춰 테스트 코드도 같이 관리되어야 유지보수에 도움이 되기 때문이다. 

그 중 테스트 코드중에서도 비슷한 Assertions 로직이 반복되는 경우가 있는데 이런 상황을 리팩토링하는 방법 중 하나를 소개해보려고 한다.

여기서 소개하고자하는 내용은 함수형 인터페이스를 사용하는 방식이며, 함수형 인터페이스의 간단한 개념만 알고 있다면 이해하는데 그리 어렵지 않다. 

만일 함수형 인터페이스에 대해서 낯설다면 다음 글을 참고해 볼 수 있다. 👉[람다와 함수형 인터페이스]()

## 반복되는 Assertions

여기 제품 ID 매개변수로 해당하는 제품의 정보를 요청하는 간단한 API Spec 이 있다. 

**제품 정보 요청**

```http
GET 
/api/v1/product/{productId}
```


**정상 응답**
정상 응답의 경우 data라는 필드로 해당 정보를 감싸서 공통응답을 내려주는 spec이 있다고 가정한다. 
```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
	"data": {
		"id": {id},
		"price": {price}
	}
}
```

그리고 다음 예시는 WebMvcTest를 이용한 ProductController 테스트 코드이다. 
이 테스트 코드는 특정 API에 대한 요청과 그에 대응하는 응답에 대한 검증을 목표로 하고 있으며, 정상적인 흐름일 경우 그에 적절한 HttpStatus code와 response body의 정보가 예상한 대로 흘러가는지 검증한다.
```java
final String productId = "1";
final Long price = 100L;

@Test  
@DisplayName("재고조회 - 정상 요청일 경우, 200 status 와 재고 리턴")  
void test1000() throws Exception {
    // given  
    final Product product = new Product(productId, price);
    BDDMockito.given(productService.findByItemId(productId))  
	            .willReturn(product);  
  
    // when  
    final MvcResult result = mockMvc.perform(
			    get("/api/v1/product/{productId}", productId)
			)  
            .andExpect(status().isOk())  
            .andReturn();
    // then
    final String response = result.getResponse().getContentAsString();
    final JsonNode responseBody = objectMapper.readTree(response);
    final JsonNode dataField = responseBody.get("data");
    
    assertEquals(product.getId(), dataField.get("id").asText());
    assertEquals(product.getPrice(), dataField.get("price").asLong());
}
```

해당 코드를 간단하게 설명해보자면 
- BDD style로 `given-when-then` 구조를 따르도록 작성된 테스트 코드이다. 
- `given` 절에서는 ProductController 에서 참조하고 있는 productService의 findByProductId 메서드를 미리 지정해둔 Product를 리턴하도록 stubbing하고
- `when` 절에서 `"/api/v1/product/{productId}"` 로 productId를 담은 get 요청을 보낼때, 
	- httpstatus 는 200(ok) 를 기대한다.
- 그리고 `then` 절에서 응답값으로 내려온 json reponseBody가 "data" 를 가지고 있는 구조인지 검증하고 
	- 해당 값들도 미리 스터빙해둔 Product의 값으로 정상적으로 내려오는지를 검증한다. 


그런데 위와 같은 `"정상 요청일 경우, 200 status 와 재고 리턴"`  테스트의 구조상, 정상흐름과 그에 대한 응답값을 확인하는 검증로직은 비단 조회뿐 아니라 다른 API에서도 사용될 것으로 예상해볼 수 있는데, 다음 예시도 한번 살펴보자.

**제품 가격 수정 요청**
```http-request
PATCH /api/v1/product/{productId}/price
Content-Type: application/json;charset=UTF-8

{
  "newPrice": {newPrice}
}
```
**정상 응답**
```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
	"data": {
		"id": {id},
		"name": {name},
		"price": {price}
	}
}
```
**테스트 코드**
```java
final String productId = "1";
final Long newPrice = 200L;

@Test  
@DisplayName("재고수정 - 정상 요청일 경우, 200 status 와 수정된 재고 리턴")  
void test1000() throws Exception {
    // given  
    final Product updatedProduct = new Product(productId, newPrice);
    BDDMockito.given(productService.updateProduct(productId, newPrice))  
	            .willReturn(updatedProduct);  
  
    // when  
	String content = "{\"newPrice\":" + newPrice + "}";
    final MvcResult result = mockMvc.perform(
			patch("/api/v1/product/{productId}/price", productId)
				.contentType(MediaType.APPLICATION_JSON)  
				.content(content)
		)
        .andExpect(status().isOk())  
        .andReturn();

	// then
	final String response = result.getResponse().getContentAsString();  
	final JsonNode responseBody = objectMapper.readTree(response);  
	final JsonNode dataField = responseBody.get("data");  
	assertEquals(updatedProduct.getId(), dataField.get("id").asText());  
	assertEquals(updatedProduct.getName(), dataField.get("name").asText());  
	assertEquals(updatedProduct.getPrice(), dataField.get("price").asLong());
}
```

위와 같은 경우도, 정상흐름일 경우에 대한 응답 검증으로 `given`, `when` 절을 제외한 `then`절만 살짝씩 달라진다.

이에 대해서 리팩토링을 수행해볼 수 있을 것 같다. 

## Consumer<>



## 마무리