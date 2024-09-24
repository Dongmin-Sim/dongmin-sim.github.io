---
title: VO(Value Object) 알아보기
author: codongmin
date: 2024-09-19T21:37:00
categories:
  - ddd
tags:
  - ddd
  - vo
preview: 값 객체(Value Object)의 정의와 이점에 대해 설명합니다.
---

## 들어가며

최근 개발중인 서비스에서 값 객체(Value Object)를 활용하여 개선시켰던 경험이 있어 이 내용을 글로 남기고 싶었다. 

이를 위해 값 객체의 개념을 찾다보니, 엔티티(Entity) 그리고 더 나아가 도메인 주도 개발(DDD)라는 설계 패러다임까지 연관된 개념으로 연결되는 것을 확인했다. 값 객체와 엔티티 이 둘은 어떤 것을 의미하고 이와 연관된 도메인 주도 개발(DDD)은 또 무엇일까?

본 글에서는 이들의 개념을 살피기 위해 도움을 줄 수 있는 도메인 모델에 대해 간략하게 언급한다. 이어서 엔티티와의 비교를 통해 값 객체란 어떤 것을 의미하는지 알아본다. 또 값 객체를 활용하여 어떤 이점을 취할 수 있는지를 다뤄보려고 한다. 값 객체를 적절하게 사용할 수 있다면 우리가 원하는 비지니스 로직을 적절하게 캡슐화하면서 테스트가 용이한 구조로 만들 수 있다. 이것이 어떻게 가능한지 차근차근 알아보자.

## 도메인 모델

값 객체를 언급하기 위해서는 "도메인"모델에 대해서의 이해가 선행적으로 필요하다. 도메인 모델이 무엇인지 간략하게만 짚고 넘어가자. 

도메인 모델이란 개념모델을 의미

## 엔티티(Entity)와 값 객체(VO, value object)란?

도메인 모델을 성격에 따라 2가지의 개념으로 분류해볼 수 있다. 엔티티와 값 객체이다.
### 엔티티(Entity)란?

엔티티는 다음과 같은 속성을 갖는 도메인 모델을 의미한다. 
- 고유함을 확인할 수 있는 **식별자**를 갖는다. 

서로 다른 엔티티는 서로 다른 식별자를 갖는다. 즉, 엔티티를 구별할 수 있는 속성인자를 갖는다. 이것이 값 객체와 다른 가장 큰 특성이다. 

- 엔티티는 엔티티의 라이프 사이클 내에서 가지고 있는 **속성의 상태가 변경**될 수 있다. 즉, 가변성을 가진다.
엔티티는 시간의 흐름에 따라 엔티티가 가지고 있는 속성의 상태값이 변경될 수 있다. 이 또한 값 객체와 구별되는 또 다른 특성이다.

예시를 통해 엔티티만이 갖는 속성을 이해해보자. 

![Desktop View](/assets/posts/architecture/ddd/vo/user.png)

다음은 간단한 SNS 서비스에서의 User 모델을 간략화한 도식이다. 
여기서 User 모델은 엔티티로 볼 수 있다. 앞서 언급한 엔티티의 2가지 속성을 충족한다.

첫째로, **고유한** 자신을 나타내기 위한 `id` 값이 존재한다. 여기서 `id` 값은 앞서 설명한 **식별자**에 해당된다. 만약 다른 User 모델이 존재한다면 `id` 값을 기준으로 **동등성을 비교**하여 서로 같은 모델인지 아닌지를 확인할 수 있다. 

두번째로, User 모델은 시간의 흐름에 따라 속성의 **상태가 변경**될 수 있다. User가 다른 User를 팔로우한다면 followingCount가 증가할 것이고, 누군가 해당 User를 팔로우한다면 `followerCount` 가 증가된다. 

### 값 객체(Value Object)란?

값 객체란 도메인 모델에서 **개념적**으로 의미가 **동일**한 **속성 값들**의 묶음을 의미한다. 

일반적으로 객체의 속성 값들은 원시타입(Primitive type)이다. 이러한 원시타입들을 묶어 새로운 도메인 개념을 표현하는 역할을 한다. 

블로그 포스트를 나타내는 도메인 객체를 통해 값 객체의 구체적인 사례를 살펴보자. 
- 포스트는 구별을 위한 식별자 `id`를 갖는다. 위에서 살펴본 엔티티에 해당한다. 
- 포스트 엔티티는 작성자의 이름, 작성자의 이메일을 속성으로 갖는다. 
- 포스트 엔티티는 포스트의 좋아요 수를 속성으로 갖는다. 
- 포스트 엔티티는 내용, 수정 여부, 시간을 속성으로 갖는다. 

```java
public class Post {  
    private final Long id;  
    private final String authorName;  
    private final String authorEmail;
    private final String content;
    private final int likeCount;
    private final boolean isEdit;
    private final DateTime dateTime;
    ...
}
```

물론 다음과 같이 원시타입을 이용해서 객체의 속성을 정의하는 것이 문제가 되진 않는다. 하지만 값 객체를 이용하면 표현력이 풍부한 코드를 만들 수 있다. 

어떤 값들을 묶어 값 객체로 표현할지는 요구사항에 따라서 달라진다. 
위의 도메인 객체에서는 작성자와 관련된 속성들을 묶어 "작성자"라는 도메인 개념으로 표현할 수 있을 것 같다. 

```java
public class Author {
	private final String name;  
    private final String email;
	
	public Author(String name, String email) {
		this.name = name;
		this.email = email;
	}

	public String getName(){
		return name;
	}
	
	public String getEmail(){
		return email;
	}
}
```

위와 같이 "작성자(Author)"를 표현하는 값 객체를 만들 수 있다. 

```java
public class Post {  
    private final Long id;  
    private final Author author;  
    private final String content;
    private final int likeCount;
    private final boolean isEdit;
    private final DateTime dateTime;
    ...
}
```
이전 보다 Post가 가지고 있는 속성이 "작성자"와 연관되어있다는 것을 명확하게 표현할 수 있다.
#### 값 객체의 특징

Author 객체를 보자. 엔티티와는 다른 부분이 존재한다. 
값 객체는 **식별자가 없다.** 그렇다면 서로 다른 값 객체는 어떻게 비교할 수 있을까?

서로 다른 "Author" 값 객체의 비교는 식별자가 아닌 각각의 **속성의 값**으로 **동등성 비교**를 한다. 

> [!NOTE] 동등성(equality) 비교와 동일성(identity) 비교
> 두 객체가 **논리적으로** 같은지를 확인하는 비교를 말한다. 주로 객체가 가지고 있는 **속성 값**이 같은지를 비교한다.
> 
> 이와 대조적인 개념으로 **동일성(identity)** 비교가 있다. 동일성 비교는 객체가 **물리적**으로(상대적) 같은지 확인하는 비교를 말한다. 객체가 할당된 **메모리 주소**가 실제로 일치하는지를 비교한다. 

각각의 속성값들이 서로 일치하는지 비교해야하기 때문에 객체는 `equals` 와 `hashCode` 메서드의 오버라이드가 필요하다. 

```java
public class Author {
	private final String name;  
    private final String email;
    ...
	@Override 
	public boolean equals(Object o) { 
		if (this == o) return true; 
		if (o == null || getClass() != o.getClass()) return false; 
		Author author = (Author) o;
		return Objects.equals(name, author.name) && 
		Objects.equals(email, author.email); 
	}
	
	@Override 
	public int hashCode() { 
		return Objects.hash(name, email); 
	}
}
```

또 하나 엔티티와 다른 점은 **속성의 상태가 변경**될 수 있는 엔티티와는 다르게 값 객체는 속성의 상태가 변경될 수 없다. 즉, **불변(immutable) 타입**으로 구현된다. 

왜 **값 객체**는 **불변타입**으로 구현하는 것으로 방향이 잡혔을까 ?

그 이유는 **값 객체의 정체성**에 따른 특성 때문이라고 나는 생각한다. 앞서 값 객체는 **속성 값의 동등성** 비교를 통해 서로 다른 값 객체를 구분한다고 했다. **식별자**의 의미보다 **속상 값 자체**에 값 객체의 의미가 있기 때문일 것이다. 

값 객체의 속성 값을 중요시하는 역할에서 불변타입은 꽤나 좋은 이점들을 가져다준다.

첫번째로 한번 생성된 속성 값은 변경될 수 없으니 값 객체를 **신뢰성** 있게 사용할 수 있다. 
신뢰성 있게 사용한다는게 무슨말일까? 다음은 불변상태가 아닌 VO를 가정한 예시이다.

```java

```

1. 값의 일관성이 유지된다. 
2. 값을 예측할 수 있다. 


참조 투명성 

스레드 안전 
멀티스레드 환경에서 동시 접근 시, 객체가 불변이므로 동기화 없이도 안전하게 사용가능



따라서 값 객체에는 속성 값을 변경할 수 있는 `setter` 를 제공하지 않는다.


#### 정리


---
## 둘의 차이점

두 개념의 관계 설명하고, 비교 

엔티티, 값 객체 비교? 



---
## 값 객체(VO, value object)의 이점

### 1. 도메인 로직 캡슐화,

도메인 로직을 포함할 수 있다. 
캡슐화 할 수 있다.  도메인 로직이란 무엇일까? 

그렇다면 도메인 로직을 구분하는 방법은 무엇일까 ?

**이 로직을 바꾸면 비즈니스 규칙이 바뀌는가?**
- **Yes**: 도메인 로직.
- **No**: 비도메인 로직 (기술적 구현, UI 관련 등).

값 객체는 내부에서 도메인 로직을 구현함으로써 
캡슐화할 수 있다. 값 객체와 연관된 도메인 로직(검증 로직 등)을 하나로 집중시킬 수 있다. 

단순한 연산을 비지니스 로직을 반영하는 행위로 표현할 수 있다. 

###  중복 제거
불변타입이 가져다 주는 이점

### 2. 명확한 의미를 표현 
가독성 증가, 코드의 의미 이해가 쉬워짐. 

### 2. 캐싱 & 재사용 가능
불변타입이 가져다 주는 이점

### 3. 테스트 용이



## 마무리
