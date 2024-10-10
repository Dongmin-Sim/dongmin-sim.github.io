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

일반적으로 객체의 속성 값들은 원시타입(Primitive type)이다. 이러한 원시타입들을 묶어 새로운 도메인 개념을 표현하는 역할을 한다. 일종의 Wrapper 역할을 한다.

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
위의 도메인 객체에서는 작성자와 관련된 속성(`authorName`, `authorEmail`) 들을 묶어 "작성자"라는 도메인 개념으로 표현할 수 있을 것 같다. 

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
#### 값 객체의 특징1: 식별자의 유무와 동등성 비교

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

#### 값 객체의 특징 2: 불변 타입

또 하나 엔티티와 다른 점은 **속성의 상태가 변경**될 수 있는 엔티티와는 다르게 값 객체는 속성의 상태가 변경될 수 없다. 즉, **불변(immutable) 타입**으로 구현된다. 

왜 **값 객체**는 **불변타입**으로 구현하는 것으로 방향이 잡혔을까 ?

그 이유는 **값 객체의 정체성**에 따른 특성 때문이라고 나는 생각한다. 앞서 값 객체는 **속성 값의 동등성** 비교를 통해 서로 다른 값 객체를 구분한다고 했다. **식별자**의 의미보다 **속상 값 자체**에 값 객체의 의미가 있기 때문일 것이다. 

값 객체의 속성 값을 중요시하는 역할에서 불변타입은 꽤나 좋은 이점들을 가져다준다.

첫번째로 한번 생성된 속성 값은 변경될 수 없으니 값 객체를 **신뢰성** 있게 사용할 수 있다. 
신뢰성 있게 사용한다는게 무슨말일까? 다음은 불변상태가 아닌 VO를 가정한 예시이다.

```java
Author author = new Author("홍길동", "hong@email.com");
Post post = new Post(author...);

author.setName("탐관오리");

System.out.println(post.author.getName()) /// ???
```
만약 `Author`에 `.setName()`과 같이 `name` 속성을 변경할 수 있는 메서드가 제공된다면 코드 어딘가에서 `name` 속성을 변경할 수 있는 "**가능성**"이 생기게 된다. 따라서 개발자는 `author`의 `name` 객체에 접근할 때, 해당 값이 예상하는 값일 것이라고 확신할 수 없게 된다. 값 객체의 변경 가능성이 신뢰성에 문제가 발생하는 포인트가 되어버린다. 

이는 **참조 투명성**이라는 개념과도 연관된다. 값이 변경될 수 없으니 동일한 입력값에 대해 언제나 동일한 결과 값을 반환한다. 외부 상태에 영향을 미치거나 받지 않는다. 

값이 변할 수 있는 가변 객체의 경우 멀티스레드 환경에서 동시 접근시 값이 예상치 못하게 변경될 가능성이 있지만 값이 변하지 않는 불변타입이기 때문에 여러 스레드가 동일한 값 객체에 접근해도 변하지 않고 안전하게 사용이 가능하다. 이를 **스레드 안전(Thread-safe)** 하다고 한다.

따라서 값 객체에는 속성 값을 변경할 수 있는 `setter` 를 **제공하지 않는다**.

#### 정리
값 객체의 특성을 정리하면 다음과 같다.

- 값 객체는 식별자가 존재하지 않는다. 
	- 속성 값 자체로 동등성 비교를 한다.
	- `equals` & `hashCode` 를 오버라이드 한다.
- 값 객체는 불변타입을 보장한다. 
	- 덕분에 값 객체를 신뢰성있게 사용할 수 있고, 
	- 참조투명성과 스레드 안전한 이점을 갖는다. 
	- 따라서 `setter` 를 제공하지 않는다.


---
## 둘의 차이점

엔티티와 값 객체를 정확히 동일 레벨에 두고 비교하기는 어렵다. 엔티티와 값 객체는 서로 상호 보완적인 관계를 갖는다. 값 객체는 엔티티에 속하면서 엔티티가 표현하는 도메인 모델을 더욱 풍부하게 도와주는 역할일 수 있겠다. 

다음과 같이 정리해볼 수 있다.

| 구분     | 엔티티(Entity)           | 값 객체(VO, Value Object) |
| ------ | --------------------- | ---------------------- |
| 식별성    | 고유한 식별자를 통해 식별        | 속성 값에 의해 식별            |
| 동일성 판단 | 동일한 식별자일때 `==`        | 속성 값이 동등할 경우 `equals`  |
| 상태     | 가변상태                  | 불변상태                   |
| 역할     | 도메인 모델의 중요 상태와 행위를 표현 | 도메인의 특정 속성이나 개념을 표현    |

---
## 값 객체(VO, value object)의 이점

원시타입의 속성을 값 객체로 한번 더 감싸는 듯한 행위는 굳이 불필요한 행위를 하는 것처럼 보일 수 있다. 
값 객체로 감싼다는 행위가 도대체 어떤 이점들을 가져다 줄 수 있을까? 

### 1. 캡슐화로 인한 간결하고 유연한 코드

원시타입 속성을 별도의 객체로 한번 더 래핑함으로써 이 공간에 **원시타입 속성**과 **값과 관련된 로직**들을 **같이 보관**할 수 있다. 이를 **캡슐화**라고 한다. 

속성과 밀접한 행위들을 외부로 노출시키지 않는다. 이를 통해 코드의 중복을 방지하고, 더 간결하고 유연한 코드를 작성할 수 있다. 바로 뒤에 언급하겠지만, 작은 단위에서의 테스트 진행이 매우 수월해진다.

예를 들어 "피드의 컨텐츠의 길이는 200자를 넘어서는 안된다" 라는 비지니스 로직이 존재한다라고 했을때, 이러한 요구사항을 만족하는지 확인하려면 어떻게 해야할까? 

**원시타입 속성 사용**
```java
class Feed {
	String content;
}

class FeedService {
	Feed createFeed(String content) {
		if (검증 로직) {
			throw new Exception;
		}
		Feed feed = new Feed(content);
		repository.save(feed);
	}
}
```
피드를 생성하고 저장하는 로직을 작성하는데 있어 다음과 같은 구조의 코드 작성이 필요해진다. 
나름 깔끔하고 괜찮은 코드지만, 여기에 몇 가지 질문을 통해 고민해볼만한 점들을 도출해볼 수 있다. 

첫번째로 던질 질문은 `FeedService`의 `createFeed` 는 어떤일을 하고 있을까?
- 피드를 생성하기 위해 컨텐츠를 **검증하는 일** 
- 피드를 **생성하고 저장**하는 일

이어지는 질문으로, 만약 검증 로직이 하나가 아니라 5개, 10개 혹은 그보다 더 많이 늘어난다면? 
- 검증 로직이 늘어날수록 `createFeed` 메서드의 **검증 코드**도 같이 늘어나게 된다. 
	- 어떤 일을 하는 메서드인지 파악하기가 어려워진다.
- 만일 다른 곳에서 피드를 생성해야하는 일이 있을 경우, 검증 로직을 또 작성해주어야 한다. 중복된 코드가 작성된다. 
- 만일 중복된 코드에서 수정해야 하는 일이 발생한다면 동시에 수정해야하는 포인트들이 많아진다.
- `createFeed` 메서드에 검증 코드가 위치함으로써 서로 강하게 결합된다.

결론적으로 `createFeed`가 해야하는 일이 많아지게 된다. 비지니스 로직은 언제든지 변할 수 있다. 만일 변경된다면 이와 관련된 코드들도 같이 변경되어야 해서 유지보수가 어렵고 코드가 난잡해지고 가독성이 낮아질 우려가 있다.

**값 객체 사용**
```java
class Feed {
	FeedContent content;
}
// ...

class FeedContent {
	String content;

	public FeedContent(String content){
		checkContentLength(content);
	}

	private void checkContentLength(content) {
		if (검증 로직) {
			throw new Exception;
		}
	}
}
// ...

class FeedService {
	Feed createFeed(String content) {
		Feed feed = new Feed(content);
		repository.save(feed);
	}
}
```

값 객체를 사용하면 값과 관련된 로직들을 한 곳에 집중시킬 수 있다. 이 덕분에 `FeedService` 의 `createFeed` 메서드는 (굳이? 알지 않아도 되는) 검증 로직이 아니라 피드를 생성하고 저장하는 것에만 집중할 수 있다. 뿐만 아니라 검증 로직이 변경된다면, FeedContent 값 객체의 검증로직 부분만 수정해주면 된다.

검증 로직을 값 객체 안으로 옮김으로써 서로의 책임을 적절하게 분할한 셈이다. 

### 2. 테스트 용이

값 객체를 사용해 값과 값과 관련된 로직을 한 곳에 집중시키면 따라오는 이점이 한 가지 더 생긴다.
바로 테스트가 쉬워진다는 것이다. 

위의 코드에서 **원시타입 속성**을 사용했을 때, "피드의 컨텐츠의 길이는 200자를 넘어서는 안된다" 라는 로직이 잘 동작하는지를 테스트하기 위해서는 어떤 것들이 필요할까? 
- Feed 객체에 들어갈 문자열과
- FeedService의 createFeed 메서드가 필요하다. 

```java
@Test
void givenFeedServiceAndOverLengthContent_whenCreatedFeedContent_thenThrowsException() {
	// given
	FeedRepository repository = new FeedRepository();
	FeedService feedSevice = new FeedService(repository...); // 기타 FeedService에 필요한 의존관계 주입
	String overLengthContent = 'a'.repeat(201);

	// when & then
	asserThrow(Exception.class, () -> feedService.createFeed(overLengthContent));
}
```

테스트 하고자 하는 것은 "피드 컨텐츠의 길이" 이지만, 피드를 생성하고 저장하는 `createFeed` 메서드가 필요하다. 조금 이질감이 든다. 테스트의 대상이 `FeedService`의 `createFeed` 가 되어버린다. 
뿐만 아니라 "피드의 컨텐츠의 길이"을 테스트할 때 뒤따라오는 `createFeed`의 '피드를 생성하고 저장'하는 기능이 정상적으로 동작해야만 한다. 추가적으로 테스트에 의존관계가 생기는 것은 적절하지 않다.  

> **TODO** : why? 테스트에서 의존관계 bad(call out으로 빼기)

반면, 값 객체를 사용했을때는 "피드의 컨텐츠의 길이는 200자를 넘어서는 안된다" 라는 로직을 테스트하기 위해 값 객체인 `FeedContent`만 필요하다. 검증로직이 `FeedContent`의 `checkContentLength`안에 위치하기 때문이다. 그럼 다음과 같이 테스트 코드를 작성해볼 수 있다. 

```java

@Test
void givenOverLengthContent_whenCreatedFeedContent_thenThrowsException() {
	// given
	String overLengthContent = 'a'.repeat(201);

	// when & then
	asserThrow(Exception.class, () -> new FeedContent(overLengthContent));
}
```
훨씬 간단해졌다. 이제는 테스트를 위해 `FeedService`가 필요하지 않다.

### 3. 명확한 의미를 표현 

원시타입 대신 값 객체를 사용하면, 딱딱한 원시타입에 자신들이 원하는 의미를 표현할 수 있다. 즉, 원시타입을 래핑하는 과정을 통해서 한 단계 높은 차원에서의 의미 부여가 가능해진다. 

예를 들면 Integer 타입의 속성에 1을 더하는 행위를 값 객체를 사용하면 "좋아요 수를 1 증가시킨다"라는 비지니스 행위로 표현이 가능하다. 
```java
Post.likeCount = Post.likeCount + 1;
Post.likeCount = Post.likeCount - 1;
```
위의 코드는 "likeCount라는 변수에 1을 증가/감소시킨다"라고 읽힌다. 어떻게 하고자 하는지가 더 드러나는 반면
```java
Post.likeCount.like();
Post.likeCount.unlike();
```
값 객체를 활용한 코드는 "포스트에 좋아요/안좋아요" 행위를 하는 것으로 읽힌다. 무엇을 하고자 하는지가 명확해진다.

이처럼 구체적인 구현 방식은 값 객체 안으로 숨김으로써 비지니스 로직들 더 잘 드러내는 코드가 가능해진다. 


### 4. 신뢰성이 높다.

값 객체는 불변타입이다. 별도의 속성 값을 변경할 수 있는 메서드를 제공하지 않는다. 상태를 변경하지 못함으로써 얻을 수 있는 이점은 항상 같은 값을 반환한다는 점에서 온다. 

(신뢰성이 높다는 것은?)
(참조 투명성)

개발자가 리턴 값을 예측할 수 있다. 
불변타입이 가져다 주는 이점
값의 리턴값이 그 값을 대체할 수있다. 
참조 투명성을 가질 수 있다. 


### 5. 캐싱 & 재사용 가능

(때문에 이런 것도 가능하다.)

신뢰성이 높다는 이점 때문에 값 객체를 재사용하는 것이 자유롭다. 



## 마무리

이해했는지 질문에 답하기
질문




---
도메인 로직과 애플리케이션 서비스 로직의 정의와 구분방법

도메인 로직이란? 
애플리케이션 서비스 로직이란?
그렇다면 도메인 로직을 구분하는 방법은 무엇일까 ?

**이 로직을 바꾸면 비즈니스 규칙이 바뀌는가?**
- **Yes**: 도메인 로직.
- **No**: 비도메인 로직 (기술적 구현, UI 관련 등).

https://velog.io/@eddy_song/domain-logic 참고 
