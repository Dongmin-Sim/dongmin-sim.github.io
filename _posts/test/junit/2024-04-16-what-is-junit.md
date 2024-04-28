---
title: JUnit 기본 소개
author: codongmin
date: 2024-04-16T17:08:00
categories:
  - test
  - junit
tags:
  - test
  - junit
---

## JUnit이란

JUnit은 자바 언어로 작성된 오픈 소스 테스팅 프레임워크이다.
JUnit은 단위 테스트를 작성하고 실행하는 데 필요한 많은 기능을 제공하며, 테스트 케이스를 쉽게 작성하고 실행할 수 있는 도구.

JUnit은 테스트 케이스를 작성하기 위한 여러 어노테이션과 assert 메서드를 제공하여 테스트 결과를 확인하고 검증할 수 있다. 또한 JUnit은 테스트 환경을 공통적으로 세팅하고, 여러 테스트 케이스에 이를 적용하여 한 번에 실행할 수 있는 기능도 제공한다.

## 테스트 메서드

내가 테스트하고자 하는 테스트 대상을 검증하기 위한 메서드를 의미한다.

테스트 메서드의 기본 문법은 다음과 같다.

```java
@Test
void sumTest() {
	// 테스트 하고자 하는 로직
	...
	// 검증 로직
	...
}
```

- **@Test** 어노테이션을 사용한다.
- Return type은 **void** 이어야 하는데 그 이유는 다음과 같다.
  - JUnit 프레임워크의 테스트 실행흐름과 관련이 있는데,
    - Test Method 실행
      - AssertionFailedError 예외가 발생하면 Fail
      - AssertionFailedError 예외가 발생하지 않으면
        - **Assert 메서드**의 true/false 여부로 Success/Fail로 테스트가 종료된다.
    - 발생되는 Exception을 기준으로 테스트의 성공 여부를 결정짓기 때문에 굳이 return 값이 필요 없다.
    - 때문에 @Test 어노테이션은 이를 ( return type값을 void로) 강제함.
    - return type : void가 아닐 경우 에러 발생
- 접근 제어자는 붙여주지 않아도 된다.
- 메서드에는 별도의 파라미터가 없지만, 파라미터를 사용하기 위한 별도의 테스트 어노테이션이 존재한다. (이는 별도로 다룸)

## Assert 메서드란?

TestCase의 실행 or test 결과를 검증해주는 메서드로 다음 내용들을 검증하기 위해 사용한다.

- 시나리오 대로 Test Code가 실행되는가?
- Expect Result(예상)와 Actual Result(실제) 가 동일한가?

### 자주 사용하는 assert 메서드

#### 단일 파라미터 메서드

| method                         | description         |
| ------------------------------ | ------------------- |
| assertTrue(boolean condition)  | 조건 값이 true인지  |
| assertFalse(boolean condition) | 조건 값이 false인지 |
| assertNull(Object actual)      | 값이 Null 인지      |
| assertNotNull(Object actual)   | 값이 Null 아닌지    |
| fail()                         | 테스트의 실패 처리  |

- fail() 세부설명
  - 테스트를 실패시키고자 할때 사용된다.
  - 실패하는 Test 시나리오를 테스트할 때 사용한다.
  - 일련의 테스트 코드 상의 흐름을 테스트 하는 경우에 사용된다.
    - fail()하위로 흐름이 넘어가는 것을 막고 싶을때,
  - 예) 테스트 로직 대상이 반드시 실패해야하는 분기를 타지 않을 때 이를 강제하고자 할때 사용

#### 다중 파라미터 메서드

| method                                        | description              |
| --------------------------------------------- | ------------------------ |
| assertEquals(expected, actual)                | 기대값과 실제값이 같은지 |
| assertNotEquals(unexpected, actual)           | 특정값과 실제값이 다른지 |
| assertSame(Object expected, Object actual)    | 두 객체가 동일한지       |
| assertNotSame(Object expected, Object actual) | 두 객체가 다른지         |

- `assertThrows(Class<T> expectedType, Executable executable)`
  - Exception 의 발생 유무와 특정 Exception 발생의 검증이 필요한 경우 사용
  - assert할 예외타입은 그 예외타입의 후손(예외타입을 상속받은) 예외까지 모두 잡을 수 있다.

```java
@Test
void throwExceptionAssertTest() {
	// 테스트 로직
	....

	// NullPointerException이 발생하는지 검증
    Assertions.assertThrows(NullPointerException.class, new Executable() {
        @Override
        public void execute() throws Throwable {
            throw new IndexOutOfBoundsException();
        }
    });
}
```

## JUnit 테스트 라이프 사이클

JUnit의 테스트 라이프 사이클은 아래서 설명할 어노테이션들을 기준으로 관리된다. 이러한 어노테이션 기반의 라이프 사이클을 통해, 다음과 같은 여러 장점을 얻을 수 있다.

1. 테스트 수명주기 관리 용이
   - 클래스/메서드 수준의 설정과 정리 작업을 통해 테스트의 시작 - 종료 주기의 관리가 용이하다.
2. 중복 코드 방지
   - 여러 테스트 코드에서 반복적으로 필요한 코드를 한 곳에서 관리할 수 있다.

![Desktop View](/assets/posts/test/junit/what-is-junit5/junit-lifecycle.png){: width="972" height="589"}_테스트라이프 사이클_

위의 라이프 사이클이 정말로 수행되는지를 간단한 테스트 코드를 통해 확인할 수 있다.

![Desktop View](/assets/posts/test/junit/what-is-junit5/lifecycle-testcode.png){: width="972" height="589"}_테스트 코드_

print되는 메시지로 미루어보았을때, 대략적인 실행결과를 예측해볼 수 있다.

![Desktop View](/assets/posts/test/junit/what-is-junit5/result.png){: width="972" height="589"}_실행결과_

### 1. @BeforeAll (static)

- 한 클래스의 모든 테스트 메서드가 실행되기 전에, 특정 작업을 수행해야 하는 경우, 해당 어노테이션이 붙은 메서드에 정의하면 된다.
- 주로 환경 세팅 등이 이에 해당될 수 있다.
- 주의 ) 해당 어노테이션은 static 메서드로 선언해야 한다는 것.

### 2. @BeforeEach

- **테스트 메서드**에 국한되며 테스트를 실행하는데 필요한 준비작업을 할때, 사용함.
- 임시 파일을 생성한다거나, 테스트 메서드에서 사용할 객체를 생성하는 행위 등이 이에 해당될 수 있다.

### 3. @Test

- 실테 테스트가 필요한 method 또는 테스트 코드가 실행되고, Junit에서 제공하는 assert 메서드를 통해 개발자가 의도했던 테스트 결과가 나오는지 확인
  

### 4. @AfterEach

- **테스트 메서드** 종료 후, 정리할 것이 있을 때 사용
- 사용한 리소스를 반환하거나, 임시로 사용한 파일을 삭제하는 행위 등을 해당 어노테이션이 붙은 메서드에서 진행.

### 5. @AfterAll (static)

- 모든 테스트가 끝나는 시점으로 테스트 환경에 부가적으로 필요했던 인스턴스의 리소스 반환이나 종료 등을 해당 어노테이션이 붙은 메서드에서 진행.
- 주의 ) 해당 어노테이션은 static 메서드로 선언해야 한다는 것.

해당 메서드들의 실행 순서는 다음과 같다.

```
(static) ExampleClass.beforeAll
------------------------
	| ExampleClass.Constructor
  I	|-----------------------
  N	|	| ExampleClass.beforeEach
  S	|	------------------------
  T	|			|ExampleClass.test(테스트 메서드)
  A	|	------------------------
  N	|	| ExampleClass.afterEach
  C	|-----------------------
  E	| ExampleClass.(Deconstructor)
------------------------
(static) ExampleClass.afterAll
```

자바는 별도의 소멸자 메서드를 지원하지는 않지만, 추측컨테 afterEach <---> afterAll 사이에 소멸자가 호출되지 않을까 싶다.

위의 결과로 보았을 때 beforeAll, afterAll 에 static 메서드가 강제되는 이유도 어렴풋이 짐작해볼 수 있다.
두 메서드 모두 테스트 클래스의 인스턴스 객체 생성 이전에 세팅이 된다. 따라서 해당 객체의 각 테스트 메서드들이 공통적으로 참조해야하거나(환경 세팅), 테스트에 필요한 기초작업들을 세팅하는 기능으로 사용될 수 있다.

이러한 어노테이션 기반의 테스트 지원 문법은, 일종의 테스트 관심사와 테스트 준비에 필요한 관심사를 분리해주는 기능이라고 생각해볼 수 있다.
테스트를 준비하는 데 필요한 코드들을 따로 분리하는 기능을 제공함으로써 테스트 로직 코드를 조금 더 간결하게, 로직에만 집중할 수 있다.

## @Nest 중첩 클래스

위와 같은 구조로 테스트 클래스 안에 중첩 클래스를 두어 별도의 테스트 그룹으로 환경을 세팅하는 것도 가능하다. 예를 들면 다음과 같은 구조로도 가능하다.

![Desktop View](/assets/posts/test/junit/what-is-junit5/nest.png){: width="972" height="589"}_@nest 예시 코드_

중첩클래스도 결국 내부에서 동일한 라이프사이클을 갖기 때문에, 중첩클래스 내부에서도 동일하게 동작한다.
다음 실행 결과는 레벨 별로 묶어 실행 결과를 표시한것이다.

![Desktop View](/assets/posts/test/junit/what-is-junit5/nest-result.png){: width="972" height="589"}_실행결과_

때문에 테스트 클래스 내부에서 별도의 묶음 처리가 필요한 경우 다음과 같이 @Nest 어노테이션을 활용하여 테스트 구조를 묶고 분리할 수 있다.
