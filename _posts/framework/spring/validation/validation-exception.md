---
title: controller의 유효성 예외 잡아내기
author: codongmin
date: 
categories: 
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며

## Header

MethodArgumentNotValidException와 HandlerMethodValidationException는 모두 Spring Framework에서 발생하는 유효성 검사 예외입니다. 그러나 두 예외 사이에는 몇 가지 중요한 차이가 있습니다.



1. **발생 시점**:
    
    - **MethodArgumentNotValidException**: 이 예외는 메소드 매개변수의 유효성 검사에 실패할 때 발생합니다. 주로 Spring MVC나 Spring Boot에서 폼 입력 유효성 검사에 사용됩니다.
    - **HandlerMethodValidationException**: 이 예외는 컨트롤러 메소드에서의 유효성 검사에 실패할 때 발생합니다. 보통 Spring MVC나 Spring Boot에서 REST API 엔드포인트에서의 요청 본문 유효성 검사에 사용됩니다.
2. **발생 원인**:
    
    - **MethodArgumentNotValidException**: 주로 사용자 입력 값이나 폼 데이터와 같은 메소드 매개변수의 유효성을 검사하는 데 사용됩니다.
    - **HandlerMethodValidationException**: REST API 엔드포인트에서 요청 본문의 유효성을 검사하는 데 사용됩니다.
3. **해결 방법**:
    
    - **MethodArgumentNotValidException**: 주로 @Validated나 @Valid 어노테이션을 사용하여 메소드 매개변수의 유효성을 검사하고, 유효성 검사 실패 시 BindingResult 객체를 통해 오류를 처리합니다.
    - **HandlerMethodValidationException**: 주로 Spring의 Validator 인터페이스를 구현하여 컨트롤러 메소드에 적용하고, 유효성 검사 실패 시 ResponseEntity를 사용하여 클라이언트에게 오류 응답을 반환합니다.

이러한 차이점을 이해하면 각 예외를 적절하게 처리하고 유효성 검사를 구현할 때 올바른 방법을 선택할 수 있습니다.


  
네, 둘 다 인자의 유효성 검사 실패를 나타내지만, 발생하는 시점과 발생하는 방식에 차이가 있습니다.

1. **HandlerMethodValidationException**:
    
    - 이 예외는 주로 스프링 MVC 컨트롤러 메서드의 실행 전에 발생합니다. 컨트롤러 메서드에 전달된 인자의 유효성 검사가 실패하면 이 예외가 발생합니다. 스프링은 컨트롤러 메서드 실행 전에 요청된 데이터를 DTO 객체로 변환하고, 이 DTO 객체에 대한 유효성 검사를 수행합니다. 만약 유효성 검사에 실패하면 HandlerMethodValidationException이 발생하고, 이 예외는 스프링에서 자동으로 처리됩니다.
2. **MethodArgumentNotValidException**:
    
    - 이 예외는 주로 메서드 실행 중에 발생합니다. 스프링은 요청된 데이터를 DTO 객체로 변환한 후에도 빈 유효성 검사를 수행합니다. 이 과정에서 DTO 객체의 필드가 유효성 검사를 통과하지 못하면 MethodArgumentNotValidException이 발생합니다. 즉, 이 예외는 메서드가 이미 실행된 후에 발생합니다.

따라서, HandlerMethodValidationException은 컨트롤러 메서드의 인자 유효성 검사에 실패한 것을 나타내며, MethodArgumentNotValidException은 메서드 실행 중에 인자의 유효성 검사가 실패한 것을 나타냅니다.

## 마무리
