---
title: Parameterized Test ,테스트 메서드가 다수의 테스트 케이스를 가질 때
author: codongmin
date: 2024-04-26T14:19:00
categories:
  - test
  - \
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며

JUnit 에서 테스트 코드를 작성할때, 테스트 메서드는 리턴값을 void를 갖고 파라미터를 가질 수 없다. 
하지만, 파라미터 값을 가지고 테스트를 진행해야할 경우가 종종 있는데, 그럴 때 사용할 수 있는 JUnit 방법이 존재한다. 

바로 오늘 소개할 `@ParameterizedTest` 이다. 


## @ParameterizedTest
`package org.junit.jupiter.params`;

`@ParameterizedTest` 는 테스트에 필요한 매개변수가 전달될 메서드라는 것을 알리는 어노테이션이다. 
여러 인자들이 필요한 경우 사용될 수 있다. 


### 예시 상황 

**한 개의 메서드**로  **다수의 테스트 케이스**를 가지는 경우 사용하면 좋다. (1:N)
이런 상황에는 어떤 상황이 있을까?  다음 그림을 살펴보자

(한개의 메서드로 다수의 테스트 케이스 이미지)

테스트 케이스별 상이하게 적용해야 하는 테스트 데이터는 
Paramters로 전달할 수 있어서 중복을 제거할 수 잇다. 

## Syntax

ParameterizedTest 어노테이션이 달린 메서드는 @ArgumentsSource 또는 해당 구성된 주석을 통해 최소한 하나 이상의 ArgumentsProvider를 지정해야 한다. 

이러한 Provider들은 테스트 메서드를 호출할 때 사용되는 Arguments 인터페이스의 Stream을 제공할 책임을 갖는다.

interface Arguments
`Arguments` is an abstraction that provides access to an array of objects to be used for invoking a `@ParameterizedTest` method.


### Example

User 객체
이름을 필드로 갖는데, 이름에는 몇가지 제약사항이 있다 .
- null이면 안되고, 
- 공백이면 안된다.
- 길이가 10 이하여야 한다.
이러한 제약사항을 테스트하기 위해서는 어떻게 해야할까?

```java
public class JunitAnnotationTests {  
  
    @ParameterizedTest  
    @NullAndEmptySource
    void parameterizedTest(String username) {  
        assertThatThrownBy(() -> new User(username))  
                .isInstanceOf(IllegalArgumentException.class);  
    }  
  
    @Test  
    @DisplayName("User의 이름이 Null이면, 예외가 발생한다.")  
    void userNameNullThenException() {  
        assertThatThrownBy(() -> new User(null))  
                .isInstanceOf(IllegalArgumentException.class);  
    }  
  
    @Test  
    @DisplayName("User의 이름이 공백이면, 예외가 발생한다.")  
    void userNameBlankThenException() {  
        assertThatThrownBy(() -> new User(" "))  
                .isInstanceOf(IllegalArgumentException.class);  
    }  
}
```


### ArgumentSource 종류
Source 를 선택해야함. 

#### @ValueSource
`@ValueSource(strings = {" ", "ttd-practice})`
- 테스트 메서드에 전달할 파라미터를 어노테이션의 배열로 지정한다.
- 테스트 메서드는 배열 길이 만큼 실행되고, 배열의 요소가 하나씩 인자 값으로 전달된다. 
- TYPES는 인자값으로 사용할 타입의 복수명사(ints, string, bytes, etc로) 선언해야 한다.
	- ex) int 형 타입을 사용하는 경우 아래와 같이 선언

```java
@ParameterizedTest
@ValueSource(ints = {1, 2, 3, 4})
```

#### @CsvSource
`@CsvSource(value= {"1, 2", "2, 4", "3, 6}`
- 테스트 메서드에 2개의 인수를 전달해야하는 경우 사용한다..
- 하나는 테스트 메서드의 인자값, 나머지는 expect 값으로 사용한다.
- CSV의 구분자는 기본적으로 쉼표, delimiter(char형) 또는 delimiterString(String 형) 옵션을 이용해서 구분값을 지정 가능하다.

```java
@ParameterizedTest
@CsvSource(value= {"1, 2", "2, 4", "3, 6}
```

#### @NullAndEmptySource
- 인자에 null, 이나 empty가 인자값으로 전달된다.

## @RepeatedTest

지정된 횟수(value)만큼 반복적으로 실행되도록 설정하는 어노테이션 

만약 10회 반복해서 테스트해야하는 경우, For 구문을 활용해서  test code를 구성할 수 도 있지만, 코드의 가독성이 떨어진다. 이럴때 사용할 수 있다.  

## syntax

`@RepeatedTest(value=10, name=RepeatedTest.LONG_DISPLAY_NAME`)
- value : 반복 횟수 1부터 시작 
- name : RepeatedTest.LONG_DISPLAY_NAME : test 명 명시
	- SHORT(default) : "repetition" + 현재 횟수 + "of" + value
	- LONG : @DisplayName + SHORT_DISPLAY_NAME

![[Pasted image 20240426161251.png]]

### 예시 




## @TestFactory 
동적으로 테스트 메서드를 생성하는 팩토리 메서드를 정의


일반적으로 테스트 어노테이션으로 생성되는 테스트 메서드들은 생성될때 컴파일 시점에 static하게 메소드의 행위나 값들이 정적으로 결정이 되어버림
테스트 팩토리의 경우 컴파일 시점에 정적으로 확정되는 것이 아닌 런타임 시점에 테스트 메서드가 결정됨, 어떤 행위를 할건지, 사용할 파라미터는 무엇인지? 등 

언제
- 동적으로 런타임에 다양한 test case를 검증해야 하는 경우
- 데이터를 읽어서, 데이터를 기반으로 테스 메서드를 생성 & 실행해야하는 경우
- 인수 테스트(E2) 처럼 사용자 시나리오 테스트를 시행하는 경우 
- 가독성이 좋아짐. 

### syntax
`@TestFactory`
- Test case를 생성하는 팩토리임을 알리는 기능
- @TestFactory 어노테이션이 붙은 메서드는 Private , static으로 선언할 수 없음.

```java
@TestFactory
List<DynamicNode> testFactory()
```
- Return Type : Stream, Collection, Iterable, Iterator
	- <`T`> : DynamicNode
- 리턴 타입이 Collection 타입을 반환하지 않는 경우 JUnitException이 발생하면서 테스트가 Fail됨. 

dynamicTest Syntax
```java
public static DynamicTest dynamicTest(String displayName, Executable executable) {  
    return new DynamicTest(displayName, null, executable);  
}
```
- 첫번째 인자 : test case name 
- 두번째 인자 : Test method

```java
@TestFactory  
Stream<DynamicTest> isUnderTenTest() {  
    List<Integer> numbers = new ArrayList<>();  
    numbers.add(1);  
    numbers.add(2);  
    numbers.add(3);  
  
    return numbers.stream()  
            .map(num -> DynamicTest.dynamicTest(  
                            num + "가 10미만인지 검사",  
                            () -> {  
                                boolean result = num < 10;  
                                assertThat(result).isTrue();  
                            }  
                    )  
            );  
}
```

![[Pasted image 20240426163219.png]]

displayname으로 인해 실패한 테스트에 대한 디버깅이 for 문 보다는 용이 


## 예시 
- 인수테스트(E2E) 진행시, 필요한 데이터를 먼저 저장하고, 조회하는 순서로 테스트 코드를 작성할 경우 가독성이 좋지 않음. 
- dynamicTest메서드를 통해서 인수테스트의 절차(?)를 나눈다면서 test 이름도 설정하고, 가독성 또한 높아짐. 
- 순서가 필요한 테스트 단계를 하나의 테스트 내부에서 나눔으로써 가독성을 높일 수 있음.


## @Timeout 

단위 테스트에 대한 시간 제한을 설정하는 어노테이션 

실행시간이 중요한 기능의 단위 테스트에 적용

주어진 시간 안에 테스트가 끝나지 않으면 TimeoutException 에러가 발생하고 실패한다.

```java
@Test
@Timeout(TIME_VALUE)
```
- `@Test` 어노테이션과 함께 선언해야 한다. 
- 첫번째 인자 : Test Method가 실행될 수 있는 최대 시간
- 주의) 시간 단위는 ms가 아닌 s 이다. 


## @ExtendWith

- Test Class에서 공통적으로 사용할 확장 기능을 설정하는 어노테이션 
- 일종의 익스텐션을 Import 하는 역할 

`@ExtendWith(Mockito.class)`
- Mock 객체를 쉽게 사용할 수 있도록 기능을 제공하는 프레임워크 기능을 JUnit 에서도 사용할 수 있도록 확장해주는 역할 

`@ExtendWith(SpringTest.class)`
- JUnit의 테스트 코드에서 Spring ApplicationContext 기능을 JUnit 에서도 사용할 수 있도록 확장해주는 역할
- JUnit4에서는 다음과 같이 작성해야 했지만, JUnit5 부터는 @SpringBootTest 만 적어도 동일한 효과


## TDD & BDD(Behavior Driven Develop)

- 사용자의 행동까지 생각하고 Test 하면서 개발하는 방법론 
- BDD는 TDD에서 파생된 개발 방법론 

- 주로 프로젝트에서 BDD의 Test case로 시나리오(통합 테스트)를 검증하고, 시나리오에서 사용되는 각 모듈들은 TDD의 test case로 검증을 하는 방식임. 
- 예시) 로그인 기능 
	- BDD 방식(ID와 Password가 주어졌을때, 로그인 시도를 하고 로그인 정보가 유효하다면 Access Token을 반환한다. )

### Example 

BDD 방식의 로그인 시나리오 테스트(통합 테스트)
- Given : ID & Password가 주어졌을때, 
- When : try login
- Then : Return Access Token 

TDD 방식의 로그인 기능 테스트(단위 테스트)
- 회원 여부를 확인하는 test case를 통과하는 단위 테스트 작성함으로 로그인 기능의 모듈인 verifyId(String id)와 verifyPassword(String password)를 구현


## 마무리

## 테스트 코드 간에는 의존성이 있어서는 안된다.

각각의 테스트 코드는 독립적으로 반복 가능해야한다.
그리고 테스트 코드 간에는 서로 의존성이 존재해서는 안되는데, 그 이유는 다음과 같다. 

먼저, 테스트 코드가 독립적이면 하나의 테스트가 실패하더라도 다른 테스트에 영향을 미치지 않습니다. 이는 특정한 테스트 케이스의 실패로 인해 전체 테스트 스위트가 중단되는 상황을 방지하고, 개발자가 더 빠르게 문제를 해결할 수 있도록 도와줍니다.

또한, 독립적인 테스트 코드는 코드의 변경이나 리팩토링과 같은 작업을 수행할 때 안정성을 보장합니다. 의존성이 없는 테스트 코드는 시스템의 다른 부분에 영향을 주지 않으므로, 코드 변경으로 인한 테스트의 실패가 예상치 못한 문제로 이어지는 것을 방지합니다.

반면에 의존성을 가지는 테스트는 안 좋은 방법입니다. 의존성이 있는 테스트는 특정한 순서에 의존하거나 다른 테스트의 결과에 영향을 받을 수 있습니다. 이는 테스트의 예측 가능성을 떨어뜨리고, 코드 변경이나 리팩토링 시에 문제를 발생시킬 수 있습니다. 또한, 특정한 환경이나 상태에 따라 테스트의 결과가 달라질 수 있으므로 신뢰할 수 없는 테스트가 될 수 있습니다.

따라서 효과적인 테스트 코드를 작성하기 위해서는 테스트 간의 독립성을 유지하고 의존성을 최소화하는 것이 중요합니다.
