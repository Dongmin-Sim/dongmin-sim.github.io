---
title: Querydsl이란?
author: codongmin
date: 2024-05-19T17:11:00
categories:
tags:
image:
  path: /assets/img/thumbnail/spring-data.png
  lqip:
  alt: Spring Data
preview: |
  본 글에서는 Querydsl 의 개요와 특징에 대해서 설명합니다.   
  본 글에서는 Querydsl 의 개요와 특징에 대해서 설명합니다.
---

## 들어가며

백엔드 개발에 있어서 애플리케이션과 데이터베이스는 **뗄레야 뗄 수 없는 관계**를 가지고 있다. 사용자가 요청한 정보를 저장하고, 다시 꺼내오는 작업의 대부분을 서버 사이드에서 맡는다. 때문에 이러한 작업을 수행하기 위해서는 데이터베이스, 혹은 사용자가 요청한 정보를 가지고 있는 매체에 접근하는 것이 **필수적일** 수 밖에 없다.

현대 웹 개발에서 이러한 **데이터 저장과 관리**를 담당 매체는 주로 **데이터베이스**이다. 데이터베이스와 대화를 하기 위해서는 그들이 사용하는 **언어(:SQL)** 를 통해서 접근해야 한다. 때문에 그 중간에서 백엔드 파트에서 사용하는 언어와 그들의 언어를 **통역**해주는 여러 장치들도 **필연적**으로 생겨날 수 밖에 없었다.

**통역**에는 많은 비용이 소모된다. 왜냐하면 서로 사용하는 언어의 구조가 다르기 때문이다. 때문에 두 언어간 통역을 위해서는 여러가지 **기술**이 필요하다. 주로 백엔드 사이드에서 사용되는 언어인 객체지향 언어와 데이터베이스 세계의 언어를 서로 연결지어주는 **ORM(Object-Relational Mapping)** 기술이 대표적이다. 자바 진영에서는 이러한 기술을 구현한 **Hibernate** 프레임워크가 대표적이다.

예를 들어 Hibernate 프레임워크는 객체-관계 세계를 효율적으로 이어주기 위해 그들만의 변환 언어를 사용하는데, 그것을 **객체지향 쿼리 언어**라고 한다. 관계 세계의 SQL을 기반으로 하지만, 객체 세계의 언어도 혼합된 언어를 의미한다. 그리고 이 언어를 **HQL(Hibernate Query Language)** 라고 부른다.

HQL은 비교적 두 세계를 잘 이어주는 역할을 했지만, 보다 사용하기가 어려운 언어였다. 사용자의 **실수**로 인해 번역되지 못한 경우도 발생하였고, 조금 복잡한 내용을 전달할 때는 사용자가 이를 위해 **준비**해야하는 점들이 많았다.

오늘 소개하고자 하는 **Querydsl**은 이러한 문제들을 보안하기 위해 탄생했다.

본 글은 querydsl의 전반적인 이해를 돕기 위해 작성되었다.

1. [**Querydsl이란**](#what-is-langchain-framework-),
2. Querydsl을 [**왜 쓸까**](#llm-)
3. Querydsl의 [**동작원리**](#llm-)

순으로 글이 전개되며, 본 글 이후에 이어지는 글에서는 Spring 환경에서 Querydsl을 활용하는 방법을 다룰 예정이다.

## 1. Querydsl은 뭘까

이 도구의 이름에 DSL이 붙었다. DSL, 줄임말 같다. 이를 풀어보면 Domain-Specific Language.
도메인 특화 언어라는 뜻이다.
앞에 `Query-`가 붙었으니 질의를 위한 특화 언어라는 것을 유추해 볼 수 있다.

### DSL ?

우리가 사용하는 일반적인 언어들은 **범용 언어**라고 볼 수 있다. 그 외에 특정 도메인의 개념과 규칙을 따르는 언어를 **도메인 특화 언어**라고 칭한다.

{% include embed/youtube.html id='mcn3iTXE8Co' %}

위와 같은 영상을 본적이 있는가? 이는 경매사 분들이 진행하는 방식과 언어이다. 이를 DSL의 개념에 비유해볼 수 있을 것 같다. 이 분들이 사용하는 언어는 "경매"라는 **특수한 목적**을 달성하기 위해 사용되는 언어이다. 우리가 일반적으로 대화를 위한 범용적인 언어와 비슷한것 같지만, 특수 목적을 달성하기 위해 **더 유용한 형태**와 **구조**를 갖는다.

DSL을 이점은 여기에 있다.

- DSL을 서로 아는 사람들끼리 대화하면 작업이보다 **효율적으로** 이루어진다.
- 특정 도메인 문제를 표현하는 방식이 존재하기 때문에 보다 훨씬 **명확해질** 수 있다.(불필요한 복잡한 요소들을 분리할 수 있다.)
- DSL을 전문적으로 다루는 계층이 존재하면, 이를 이용하는 양쪽 계층은 본인들의 주요 **관심사에** 집중할 수 있다. 일종의 **브릿지 역할**

다시 돌아와서 이 도구의 이름만 보자면 querydsl은 **질의**를 위한 목적으로 탄생한 언어, 도구라고 이해할 수 있겠다. 그런데 그런 도구가 얘 하나 뿐인가, 앞서 "들어가기"에서 언급한 것처럼 Hibernate의 HQL이라는 언어도 쿼리를 하기 위해 탄생했는데 이 친구는 뭐가 다르기에 QueryDSL이라는 이름을 달았을까?

### HQL 가볍게 살펴보기

`Querydsl`이 탄생하게 된 배경에는 `HQL(Hibernate Query Language)`을 지나간다. 왜냐하면 `Querydsl`는 HQL이 가지는 **단점들을 보안**하기 위해 탄생되었기 때문이다.

`HQL`은 `Hibernate ORM` 프레임워크에서 사용하는 질의 언어이며 다음과 같은 특징들을 갖는데, 그전에 한 가지 예시를 통해 설명을 이어가고자 한다.

사용자가 작성한 게시글 중 **특정한 키워드**를 포함하는 게시글을 **검색**하는 경우를 생각해보자. 이때, 게시글의 작성 날짜가 **최근 일주일 이내인 경우**를 기준으로 검색하려고 한다.

이를 `HQL` 방식으로 작성하면 다음과 같다.

```java
public List<Post> findPostsByUserAndKeyword(Session session, String username, String keyword) {
    String hql = "SELECT p FROM Post p JOIN p.user u " +
                 "WHERE u.username = :username " +
                 "AND p.contnet LIKE :keyword " +
                 "AND p.createdAt >= :oneWeekAgo";

    Query<Post> query = session.createQuery(hql, Post.class);
    query.setParameter("username", username);
    query.setParameter("keywrod", "%" + keyword + "%");
    query.setParameter("oneWeekAgo", LocalDateTime.now().minusWeeks(1));

    return query.list();
}
```

**SQL에 친숙**하다면 문자열 형태의 `hql` 가 의미하는 바를 쉽게 이해할 수 있을 것이다. SQL을 알고 있다면 이 새로운 언어가 표현하는 바를 익히는데 그리 어렵지 않을 것이고, `Post` 클래스를 통해 각각의 파라미터들이 어떤 것을 의미하는지 이해하기 쉽다. 쿼리의 결과도 `Post` 클래스로 매핑도 진행해주는 것으로 보인다.

매우 훌륭하다!! 👍

정리해보자면 HQL은 다음과 같은 특징을 갖는다.

- SQL 문법과의 유사성 : SQL에 익숙하면 적응하기 쉬움
- 문자열 기반 : 문자열 조합을 통한 쿼리 작성, 간결하고 직관적이다.
- 객체 중심의 쿼리 작성 : 객체의 프로퍼티와 쿼리 파라미터와의 매핑, 쿼리 결과의 객체 매핑

정말 편리하고 사용하는데 있어 큰 불편함이 없을 것 같지만 살짝의 아쉬운 부분이 존재한다.

관찰력이 좋은 분들은 발견했을지도 모르겠지만, 위 코드에는 의도된 네이밍이 아니라면 **에러를 발생**시키는 부분이 존재한다. 물론 어플리케이션을 실행하기 전까지, 해당 쿼리가 실행되지 전까지는 **알아차릴 수 없다**는 단점이 존재한다.

## 2. QueryDSL 왜 쓰지

`hql`문자열과 파라미터를 세팅하는 부분을 확인해보자, `contnet`, `query.setParameter("keywrod", "%" + keyword + "%")` 는 개발자의 **실수로 작성된 오타**이다. 아쉽게도 이 오타는 애플리케이션이 **컴파일되는 시점**까지도 드러나지 않는데. 그 이유는 해당 부분이 문자열이기 때문이다.

실제로 해당 로직이 실행되어서, 작성된 문자열 쿼리가 나가야 비로소 오타로 인한 에러가 감지된다. 런타임시에 감지되는 에러라는 의미이다. 개발자 입장에서는 런타임 오류보다 컴파일 오류가 낫다.

다음 코드를 살펴보자. 앞서 HQL로 작성된 코드와 동일한 일을 하는 코드이다.

```java
public List<Post> findPostsByUserAndKeyword(EntityManager entityManager, String username, String keyword) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(entityManager);
    QUser user = QUser.user;
    QPost post = QPost.post;

    LocalDateTime oneWeekAgo = LocalDateTime.now().minusWeeks(1);

    return queryFactory.selectFrom(post)
                       .join(post.user, user)
                       .where(user.username.eq(username)
                             .and(post.content.containsIgnoreCase(keyword))
                             .and(post.createdAt.goe(oneWeekAgo)))
                       .fetch();
}

```

이 코드에서는 SQL을 찾아볼 수는 없다. 하지만 네이밍 등으로 유추해보는 것이 얼추 가능해보인다. 처음 봤을때 느껴지는 직관적인 느낌들은 다음과 같다.

- QueryFactory 뭔가 쿼리를 만들어주는 것 같아보인다.
- QUser ? QPost ? 사용하던 엔티티에 먼가 부가적인 클래스 같은데, 일단은 정확한 용도는 모르겠다.
- 최근 일주일 이내를 나타내는 자바코드가 직관적이어서 이해가 쉽다.
- 메서드 체이닝으로 이루어진 쿼리가 이루어져있다. post에서 유저로 조인하고, 기타 조건들도 and로 묶여 이해가 쉽다. 코드는 잘읽힌다.

한번 정리해보면
QueryDSL은 쿼리 작성 시 컴파일 타임에 문법과 타입을 검증할 수 있도록 합니다. 이를 통해 런타임 오류를 줄이고, 안전한 코드 작성을 도와줍니다.

앞서 SQL도 가독성이 그리 나쁘지 않았지만, Java 표현식을 사용한 코드는 보다 직관적인 가독성을 갖는다. 마치 글을 읽듯이 흐르듯이 이해하는 것이 가능하다.

한 가지 더 부가적인 장점은 IDE를 활용한 자동완성이 지원된다는 것이다.

2. **메타 모델 기반 쿼리** : 엔티티 클래스에 대한 메타 모델을 생성하여, 이를 기반으로 쿼리를 작성합니다. 이는 코드의 자동 완성과 리팩토링을 쉽게 할 수 있게 합니다.
3. **표현식 기반 쿼리**: 쿼리를 문자열이 아닌 Java 표현식으로 작성할 수 있습니다. 이는 코드의 가독성과 유지보수성을 높입니다.

위의 예시는 JPA를 사용한 예시였지만, Querydsl은 이 뿐만아니라 SQL, JPQL, MongoDB, Lucene 다양한 데이터 소스를 지원한다.

**동적 쿼리 작성 용이**: 조건에 따라 동적으로 쿼리를 생성하고 조합할 수 있는 기능을 제공합니다.

### 주요 차이점

|     |     |     |
| --- | --- | --- |
|     |     |     |

그런데 아까봤던 `Q-` 로 시작하는 클래스들은 대체 무엇이었을까?

## Querydsl 동작원리

Querydsl은 다음과 같은 과정으로 쿼리를 생성한다.

먼저 querydsl apt(annotation processing tool) 이라는 친구가 컴파일 시점에 코드를 스캔하여 `@Entity` 어노테이션이 붙은 클래스들을 탐지한다. +(@Table)

그리고 감지된 클래스(엔티티)들을 대상으로 하여

---

## 다음으로

이어지는 글로 Spring에서는 어떻게 사용할 수 있는지 알아보려고 한다.
