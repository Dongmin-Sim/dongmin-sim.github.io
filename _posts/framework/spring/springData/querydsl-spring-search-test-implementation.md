---
title: QueryDSL을 활용한 검색 기능의 구현, TDD를 곁들인
author: codongmin
date: 2024-05-19T19:11:00
categories: 
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며


지난 글에서는 querydsl과 탄생배경, 사용하는 이유에 대해서 알아보는 시간을 가졌다. 
👉 링크 [Querydsl 이란 ?](https://dongmin-sim.github.io/posts/what-is-querydsl/)

위 글의 연장선에서 자바의 대표적인 웹 프레임워크인 스프링에서는 이를 어떻게 활용하는지 간단한 예시를 통해 설명해보고자 한다. 


## Spring에서 어떻게 쓰지? 
Spring Custom Repository 이란?

다음 공식 문서를 확인해보자 공식문서에는
https://docs.spring.io/spring-data/jpa/docs/2.1.3.RELEASE/reference/html/#repositories.custom-implementations
"Spring 데이터 저장소에 대한 사용자 정의 구현"이라는 파트에서 사용자가 기본적인 쿼리 이상을 사용하고싶을때 다음과 같은 방식을 사용할 수 있다고 한다.

**커스텀 리포지토리**를 만드는 이유는 다음과 같습니다:

1. **복잡한 쿼리 로직**: 복잡한 쿼리 로직이나 동적 쿼리를 작성할 수 있습니다.
2. **재사용성**: 특정 엔티티에 대한 복잡한 쿼리 로직을 재사용할 수 있습니다.
3. **분리된 책임**: 기본 JPA Repository의 단순 CRUD 메소드와 복잡한 쿼리 로직을 분리하여 코드의 책임을 분명히 할 수 있습니다.

### 3.2 Spring Custom Repository 사용방법

예를 들어 ProductRepository 가 있다고 가정했을때, 다음과 같이 구현할 수 있다.

`ProductRepositoryCustom` 인터페이스와 이를 구현하는 `ProductRepositoryImpl` 클래스를 따로 만드는 이유는 **커스텀 쿼리 메소드**를 JPA 리포지토리에 추가하기 위해서입니다. JPA Repository 인터페이스 자체는 단순한 CRUD 메소드만을 제공하기 때문에, 복잡한 쿼리 로직을 구현하려면 커스텀 리포지토리가 필요합니다. QueryDSL과 같은 복잡한 쿼리 라이브러리를 사용하여 동적 쿼리를 작성할 때 특히 유용합니다.

이를 단계별로 이해해 보겠습니다.

- **Step 1: 기본 JPA Repository의 한계**

기본 JPA Repository는 다음과 같은 기본 메소드들을 제공합니다:

- `save()`
- `findById()`
- `findAll()`
- `delete()`

기본적으로 제공되는 메소드 외에 특정 필드로 검색하는 메소드(`findByNameContaining` 등)를 메소드 이름으로 작성할 수 있습니다. 하지만, 이러한 메소드 이름 기반 쿼리는 복잡한 동적 쿼리를 작성하는 데 한계가 있습니다.

- **Step 2: 복잡한 쿼리 요구 사항**

예를 들어, 제품의 이름으로 검색하고, 검색어가 포함된 모든 제품을 반환하는 기능을 추가한다고 가정해보겠습니다. 이는 단순한 쿼리처럼 보이지만, 더 복잡한 조건이나 동적 필터링이 필요하다면 메소드 이름만으로는 한계가 있습니다.

이 경우 QueryDSL을 사용하여 복잡한 쿼리를 작성할 수 있으며, 이를 위한 별도의 커스텀 리포지토리 인터페이스가 필요하다.

- **Step 3: 커스텀 리포지토리 구현 방법**

간단하다. 구현하고자하는 메서드를 커스텀 인터페이스로 정의하고, 이 메서드의 동작방식을 구현하는 클래스를 작성하면된다.

**3.1. 커스텀 인터페이스 정의**
**3.2. 커스텀 인터페이스 구현**

```java
package com.example.demo.repository;
import com.example.demo.entity.Product;
import java.util.List;

public interface ProductRepositoryCustom {
	List<Product> findByNameContainingCustom(String name);
}
```

이제 `ProductRepositoryCustom` 인터페이스를 구현하는 클래스를 생성합니다. 여기서 QueryDSL을 사용하여 복잡한 쿼리를 작성합니다.

```java
package com.example.demo.repository;
import com.example.demo.entity.Product;
import com.example.demo.entity.QProduct;
import com.querydsl.jpa.impl.JPAQueryFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository public class ProductRepositoryImpl implements ProductRepositoryCustom {

	@Autowired
	private JPAQueryFactory queryFactory;

	@Override
	public List<Product> findByNameContainingCustom(String name) {
		QProduct product = QProduct.product;
		return queryFactory.selectFrom(product)
				.where(product.name.containsIgnoreCase(name))
				.fetch();
	}
}
```

마지막으로, 기존의 `ProductRepository` 인터페이스에 커스텀 인터페이스 상속을 추가하면 된다.

```java
package com.example.demo.repository;

import com.example.demo.entity.Product;
import org.springframework.data.jpa.repository.JpaRepository;

public interface ProductRepository extends ProductRepositoryCustom {

}
```

## 4. Spring Custom repository - JPA가 동작하는 방식

`ProductRepositoryImpl` 클래스는 Spring Data JPA의 확장 기능을 사용하여 `ProductRepository` 인터페이스의 구현체로 자동으로 사용됩니다. Spring Data JPA는 커스텀 리포지토리 구현체를 자동으로 인식하고 이를 주입하기 때문에, `ProductRepositoryImpl`을 명시적으로 호출하거나 사용하는 코드를 작성할 필요가 없습니다. 대신, `ProductRepository` 인터페이스를 사용하는 곳에서`ProductRepositoryImpl`의 구현체가 사용됩니다.

Spring Data JPA는 `ProductRepository`와 같은 인터페이스가 있을 때, 인터페이스의 이름 규칙을 따라 해당 인터페이스를 구현하는 구현체를 자동으로 찾고 주입합니다. 따라서 `ProductRepository`를 사용할 때, Spring이 자동으로 `ProductRepositoryImpl`의 구현체를 주입합니다.

이를 단계별로 설명하면 다음과 같습니다:

1. **ProductRepository 인터페이스**는 `JpaRepository`를 확장하고, 커스텀 리포지토리 인터페이스 `ProductRepositoryCustom`도 확장합니다.
2. **ProductRepositoryCustom 인터페이스**는 커스텀 쿼리 메소드를 정의합니다.
3. **ProductRepositoryImpl 클래스**는 `ProductRepositoryCustom`을 구현하고 QueryDSL을 사용하여 커스텀 쿼리 메소드를 작성합니다.
4. Spring Data JPA는 `ProductRepository` 인터페이스를 빈(bean)으로 주입할 때, `ProductRepositoryImpl`의 구현체를 사용하여 커스텀 메소드를 호출합니다.

### 4.1 아니 이게 어떻게 가능해 - Spring Data JPA의 마법

뭔가 말만들으면 이게 가능한가 싶다.

두개의 구현체가 존재한다는 거 아닌가?
그렇다면 런타임시에 ProductRepository인터페이스에 주입되는 빈은 어떤 객체란 말인가?

ProductRepository는 jpaRepository만 extends 하여 정상적으로 잘 동작하였다.
이는 사실 JpaRepositoryFactory를 통해 우리가 작성한 ProductRepository인터페이스를 참조하여 jpaRepository를 구현하고 실제로는 SimpleJpaRepository구현체가 주입이 되는데

custom repository 인터페이스를 상속받은 ProductRepositoryImpl 클래스도 동작을 한다.

SimpleJpaRepository 구현체와 ProductRepositoryImpl 구현체가 합체라도 했다는 건가?
이 부분이 가장 흥미로운 부분이다.

이것을 알기 위해서는 실제로 Repository 가 어떻게 생성되는지를 확인해볼 필요가 있다.

#### 4.2 Repository 가 생성되는 과정

(사실 이것에 더 가깝다. 합체 밈)

JpaRepositoryFactory는 개발자가 작성한 인터페이스를 기반으로 JpaRepository 구현체를 대신 생성해준다.

#### 프록시 생성 & 인터페이스 스캔

#### 프록시 구성

https://brunch.co.kr/@anonymdevoo/40

#### 리포지토리 통합

(
Spring Data JPA는 이 두 구현을 혼합하여 하나의 빈으로 만들어 사용합니다. 이는 Spring Data JPA의 마법과도 같은 기능으로, 인터페이스 기반의 프로그래밍을 통해 구현체를 자동으로 연결하고, 필요한 메서드들을 적절히 위임합니다. 따라서 `ProductRepository`를 사용할 때, 기본 JPA 기능과 커스텀 메서드 모두를 사용할 수 있게 됩니다.

. 이 작업은 주로 `RepositoryFactoryBeanSupport`와 같은 클래스에서 수행됩니다.
아래는 Spring Data JPA의 구현체 혼합 과정을 담당하는 주요 클래스와 메서드를 설명합니다.

1. `RepositoryFactoryBeanSupport` 클래스

- 이 클래스는 Spring Data JPA에서 리포지토리 빈을 생성하고 초기화하는 과정을 담당합니다. `RepositoryFactoryBeanSupport`는 `FactoryBean` 인터페이스를 구현하며, 리포지토리 빈을 생성하기 위해 사용됩니다.

2. `JpaRepositoryFactory` 클래스

- 이 클래스는 JPA 리포지토리의 구현체를 생성하는 역할을 합니다. 여기서 커스텀 리포지토리 구현체와 기본 JPA 리포지토리 구현체를 혼합합니다.

3.  `getRepository` 메서드

- `JpaRepositoryFactory` 클래스에서 `getRepository` 메서드를 통해 실제 리포지토리 구현체를 생성합니다.

Spring Data JPA는 `JpaRepositoryFactory`와 `JpaRepositoryFactoryBean`을 통해 기본 JPA 리포지토리 구현체와 커스텀 리포지토리 구현체를 결합합니다. 이러한 결합 과정은 Spring Data JPA가 리포지토리 인터페이스를 통해 빈을 생성하고 초기화하는 과정에서 자동으로 수행됩니다.
)

위의 설정과 코드 작성 후, Spring Data JPA는 `ProductRepository` 빈(bean)을 주입할 때 `ProductRepositoryImpl`을 사용하여 `searchByNameContainingIgnoreCase` 메소드를 자동으로 호출할 수 있습니다. 이는 Spring의 DI(Dependency Injection)와 리포지토리 확장 기능 덕분에 가능한 것입니다.

따라서 `ProductRepositoryImpl`의 구현체는 직접적으로 호출되지 않지만, `ProductRepository` 인터페이스를 통해 간접적으로 사용되며, 이는 Spring이 자동으로 처리합니다.

##### 결론

`ProductRepositoryImpl`은 `ProductRepository` 인터페이스를 통해 자동으로 사용됩니다. Spring Data JPA는 `ProductRepository` 인터페이스에 정의된 커스텀 메소드를 찾고, 해당 메소드를 구현한 `ProductRepositoryImpl` 클래스를 자동으로 주입하여 실행합니다. 이렇게 함으로써, 개발자는 구현체를 명시적으로 호출할 필요 없이 인터페이스를 사용하기만 하면 됩니다.

Spring Data JPA의 리포지토리 생성 과정은 `JpaRepositoryFactory`와 관련이 있습니다. `JpaRepositoryFactory`는 리포지토리 인터페이스의 인스턴스를 생성하고 필요한 구현체를 결합하는 역할을 합니다. 이 과정에서 `RepositoryFragment`도 중요한 역할을 합니다. 이를 통해 커스텀 리포지토리 메서드와 기본 JPA 메서드가 함께 동작할 수 있게 됩니다.

### JpaRepositoryFactory와 getRepository 메서드의 역할

`JpaRepositoryFactory`는 Spring Data JPA에서 리포지토리 인스턴스를 생성하는 핵심 클래스입니다. 이 클래스의 `getRepository` 메서드는 다음과 같은 일들을 처리합니다:

1. **프록시 생성**:

   - `getRepository` 메서드는 프록시 인스턴스를 생성합니다. 이 프록시는 리포지토리 인터페이스의 메서드 호출을 가로채어 적절한 구현체로 위임합니다.

2. **프래그먼트 처리**:

   - 리포지토리 인터페이스는 여러 부분으로 나뉘어질 수 있습니다(기본 JPA 메서드, 커스텀 메서드 등). `JpaRepositoryFactory`는 이러한 부분들을 `RepositoryFragment`로 관리합니다.
   - 각 프래그먼트는 리포지토리 인터페이스의 일부를 구현하는 역할을 합니다.

3. **프래그먼트 결합**:

   - `getRepository` 메서드는 여러 `RepositoryFragment`를 결합하여 최종 리포지토리 구현체를 만듭니다. 이는 기본 JPA 메서드와 커스텀 메서드를 하나의 리포지토리 인스턴스로 결합하는 과정입니다.

### RepositoryFragment의 역할

`RepositoryFragment`는 리포지토리 구현의 일부분을 나타냅니다. Spring Data JPA는 리포지토리 인터페이스의 다양한 구현을 `RepositoryFragment`로 나누어 관리합니다. 각 `RepositoryFragment`는 특정 기능을 담당하며, `JpaRepositoryFactory`는 이를 결합하여 완전한 리포지토리 구현체를 만듭니다.

#### 예를 들어, 다음과 같은 리포지토리 인터페이스가 있을 때:

java

코드 복사

`@Repository public interface ProductRepository extends JpaRepository<Product, Long>, ProductRepositoryCustom {     Optional<Product> searchByNameContainingIgnoreCase(String keyword); }`

Spring Data JPA는 이 인터페이스를 두 개의 `RepositoryFragment`로 나눕니다:

1. `JpaRepository`에서 제공하는 기본 CRUD 메서드를 구현하는 프래그먼트.
2. `ProductRepositoryCustom`의 커스텀 메서드를 구현하는 프래그먼트.

### JpaRepositoryFactory의 getRepository 메서드 동작 과정

`JpaRepositoryFactory`의 `getRepository` 메서드는 다음과 같은 단계를 거쳐 리포지토리 인스턴스를 생성합니다:

1. **프록시 생성 준비**:

   - 리포지토리 인터페이스를 기반으로 프록시 생성 준비를 합니다. 이 과정에서 리포지토리 인터페이스의 메서드 목록을 분석합니다.

2. **프래그먼트 생성 및 등록**:

   - 리포지토리 인터페이스의 각 부분을 담당하는 `RepositoryFragment`를 생성합니다.
   - 기본 JPA 메서드를 구현하는 프래그먼트와 커스텀 메서드를 구현하는 프래그먼트를 생성합니다.

3. **프록시 생성**:

   - 리포지토리 인터페이스를 구현하는 프록시 객체를 생성합니다.
   - 프록시 객체는 각 메서드 호출을 적절한 프래그먼트로 위임합니다.

4. **프래그먼트 결합**:

   - 여러 `RepositoryFragment`를 결합하여 최종 리포지토리 구현체를 만듭니다.
   - 기본 JPA 메서드는 Spring Data JPA가 자동으로 생성한 구현체로 위임되고, 커스텀 메서드는 커스텀 리포지토리 구현체로 위임됩니다.

### 예제

#### 1. ProductRepository 인터페이스:

java

코드 복사

`@Repository public interface ProductRepository extends JpaRepository<Product, Long>, ProductRepositoryCustom {     Optional<Product> searchByNameContainingIgnoreCase(String keyword); }`

#### 2. JpaRepositoryFactory의 getRepository 메서드:

java

코드 복사

`@Override @SuppressWarnings("unchecked") public <T> T getRepository(Class<T> repositoryInterface, RepositoryFragments fragments) {     // 프록시 생성 준비     RepositoryComposition composition = fragments.append(RepositoryComposition.of(getRepositoryFragments(repositoryInterface)));      // 프록시 생성     Object repository = getTargetRepositoryViaReflection(repositoryInterface, composition);      // 리포지토리 인스턴스를 프록시로 감싸기     T result = (T) getProxy(repositoryInterface, repository);      return result; }`

이 과정에서 `RepositoryComposition`은 `RepositoryFragment`들의 모음을 관리하고, 최종 리포지토리 구현체를 구성하는 데 사용됩니다.

### 결론

`JpaRepositoryFactory`와 `RepositoryFragment`는 Spring Data JPA가 리포지토리 인터페이스의 다양한 부분을 결합하여 최종 리포지토리 구현체를 생성하는 데 중요한 역할을 합니다. 이를 통해 기본 JPA 메서드와 커스텀 메서드가 하나의 리포지토리 구현체에서 함께 동작할 수 있습니다.

## 5. TDD로 검색기능 구현 (querydsl 활용)

간단한 검색기능을 구현하는 예시를 통해 querydsl을 활용하는 방법을 알아보자

### 1. 목표

원하는 상품을 찾기 위해 상품 검색 기능 추가

**제약사항**

- [ ] 제품의 **이름**으로 검색했을때 검색어가 포함된 모든 상품들을 리턴
- [ ] 검색시 검색어의 대소문자를 구분하지 않는다.
- [ ] 검색어가 2글자 미만일 경우 상품을 리턴하지 않는다.

### 2. TDD

테스트 시작 🔴
![Desktop View](/assets/posts/framework/spring/springData/test1.png){: width="972" height="589"}_이미지 설명_

아마 이대로 테스트를 돌리면, 구현하지 않은 메서드에 대해 컴파일 오류가 발생할 것이다. 일단 컴파일 에러를 잡기 위해 메서드를 구현한다.

Spring Custom Repository 를 생성하고, 메서드를 작성한다.
![Desktop View](/assets/posts/framework/spring/springData/custom.png)
ProductRepository 에서 Custom Repositor인 Custom을 상속받으면 일단 컴파일 에러는 해결된다.
![Desktop View](/assets/posts/framework/spring/springData/productRepository.png)

다시 테스트를 돌린다.

그러면 다음과 같인 BeanCreationException 이 터지게 되는데 잘 살펴보면 `productRepository` 빈을 생성하지 못했다는 예외이다.

```
Error creating bean with name 'productRepository' defined in ProductRepository defined in @EnableJpaRepositories declared on JpaRepositoriesRegistrar.EnableJpaRepositoriesConfiguration: Could not create query for public abstract java.util.List
ProductRepositoryCustom.ourProductSearchQuery(java.lang.String);
~
Reason: Failed to create query for method public abstract java.util.List ProductRepositoryCustom.ourProductSearchQuery(java.lang.String); No property 'ourProductSearchQuery' found for type 'Product'
```

하단의 빈 생성 에러에 대한 이유도 프린트해주는 것을 알 수 있는데, 빈 생성 예외의 원인 Spring Data JPA가 메서드를 해석할 수 없기 때문에 발생한 것으로 보인다.
Spring Data JPA는 인터페이스에 선언된`ourProductSearchQuery` 의 이름을 기반으로 쿼리를 자동 생성하려고 시도했지만, 매칭되는 키워드를 `Product` 엔티티에서 찾을수 없었기 때문에 오류가 발생했다.

Spring Data JPA에서 메서드의 이름을 보고 쿼리를 자동 생성하고
생성할 수 없으면 이와 관련된 impl 구현체를 참조하는 것으로 보인다.

![Desktop View](/assets/posts/framework/spring/springData/impl1.png)

{: width="972" height="589"}_이미지 설명_

Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'productRepositoryImpl' defined in file [/Users/coding_min/Github/CareerBuilder/build/classes/java/main/com/careerbuilder/careerbuilder/domain/product/repository/ProductRepositoryImpl.class]: Unsatisfied dependency expressed through constructor parameter 0: No qualifying bean of type 'com.querydsl.jpa.impl.JPAQueryFactory' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}

이에러는 !! 때문에 발생한다. 이렇기 때문에 설정 정보를 추가해준다.

![Desktop View](/assets/posts/framework/spring/springData/test2.png){: width="972" height="589"}_이미지 설명_

테스트가 동작한다 하지만, 기대한 값이 2개지만 실제값은 0개로 테스트가 통과되지 않는다.

![[Pasted image 20240521173424.png]]
`assertThat(productList).hasSize(2);` 이 단언 메서드를 통과하지 못했다.

위 단언메서드를 통과할 정도로만 구현해보자.
![Desktop View](/assets/posts/framework/spring/springData/impl2.png){: width="972" height="589"}_이미지 설명_

`assertThat(productList).hasSize(2);` 통과했지만 다음 단언메서드인 `assertThat(productList).extracting(Product::getName).containsExactly("apple phone", "apple watch");` 를 통과하지 못했다.
![[Pasted image 20240521173949.png]]
이 역시도 테스트 코드를 통과할 정도로만 구현해보자
![Desktop View](/assets/posts/framework/spring/springData/impl3.png){: width="972" height="589"}_이미지 설명_

드디어 첫번째 테스트를 통과했다.🟢
일단, 일어나서 엉덩이 춤을 춰주자
![[Pasted image 20240521175609.png]]

리팩토링 🟠
지금 시점에서는 별다른 리팩토링 포인트가 보이지 않는다.새로운 테스트 케이스를 하나 추가해보자

테스트 케이스를 추가하여 테스트 🔴
여기서는 해당 메서드가 일반화된 기능을 동작하도록 다른 테스트 케이스를 추가한다.

- 새로운 검색키워드를 넣고, 이것이 검색되는지를 확인하자.

![Desktop View](/assets/posts/framework/spring/springData/test3.png){: width="972" height="589"}_이미지 설명_

예상했던 대로 당연하게 실패한다.

![[Pasted image 20240521182015.png]]

이제는 단순히 특정값을 넘기는 것 만으로는 테스트를 통과할 수 없다.

![Desktop View](/assets/posts/framework/spring/springData/impl4.png)
이제 querydsl 문법을 통해 구현해보자

테스트를 통과했다.🟢
![[Pasted image 20240521175609.png]]

리팩토링 🟠
구현 부분에서는 딱히 리팩토링할 부분은 보이지 않는다. 다만 테스트 코드에 여러 테스트 케이스가 추가되면서 코드가 꽤 뚱뚱해졌다. 이를 간단하게 리팩토링해보자

1. 설정 부분을 별도의 클래스로 빼서 Import
2. prodcut를 생성하고 저장하는 부분
3. 테스트 대상 메서드와 assertions을 진행하는 부분을 메서드화할 수 있어보인다.

![Desktop View](/assets/posts/framework/spring/springData/refactor1-config.png){: width="972" height="589"}_이미지 설명_

설정을 담당하는 클래스를 재사용이 가능하게끔 별도 클래스로 빼주었다.

이를 적용하기 위해 `@Import` 를 통해 적용시켜주고,
product를 생성하고 저장하는 부분은 `createProduct()`로
테스트 대상 메서드와 assertions을 진행하는 부분은 `assertThatSearchByNameContainingIgnoreCase()`로 메서드를 분리주었다.

![Desktop View](/assets/posts/framework/spring/springData/refactor1-impl.png){: width="972" height="589"}_이미지 설명_

전과 비교했을때 뚱뚱한 코드가 훨씬 간결해지고, 코드를 이해하는데 훨씬 직관적으로 변경되엇다.
assertion 메서드는 파라미터를 봣을때 어느정도 의미 전달이 돼어보인다.
"apple"이라는 단어와 2개의 숫자 그리고 이어지는 2개의 제품명이 SearchByNameContaining이라는 메서드명과 함께
이 키워드로 검색했을때 2개의 제품과 제품명일 동일한지를 확인하는 메서드임을 유츄해볼 수 있다. 만약 헷달린다면, 아래 메서드 선언부를 참고하면 금방 이해가 된다.

테스트를 돌려 리팩토링 전후 차이가 없는지 확인한다.🟢
![[Pasted image 20240521175609.png]]
만약 여기서 통과를 하지 못했다면 리팩토링 과정 어딘가에서 실수를 했을 가능성이 있다. 그때는 다시 돌아가서 실수한 부분을 고치면 된다. 이게 바로 테스트 코드의 장점?!

### 테스트 순서에 대하여

테스트 코드를 작성하고 구현할때는 어떤 테스트를 먼저 진행할 것인지 테스트 순서도 어느정도 중요하다.
지금의 예시의 경우 그냥 기재한대로 진행했지만, 최대한 간단하고 빨리 끝낼 수 있는, 빨리 쳐낼 수 있는 테스트를 먼저 수행하는 것이 좋다.

왜냐하면 그래야 코드가 점진적으로 구현될 수 있기때문이다.
만약에 테스트하기 너무 큰 테스트의 경우 한번에 구현해야하는 양이 많기 때문에 , 각을 잡기 어려울 수 있다.

![Desktop View](/assets/meme/mathmatics.gif){: width="972" height="589"}_어디부터 손대야지...?_

지금 예시에서는 다음과 같은 순서로 테스트 코드 실행하고 구현했다.

- [ ] 제품의 **이름**으로 검색했을때 검색어가 포함된 모든 상품들을 리턴 -> 1st
- [ ] 검색 시 검색어의 대소문자를 구분하지 않는다. -> 2nd
- [ ] 검색어가 2글자 미만일 경우 상품을 리턴하지 않는다. -> 3th

하지만 다음과 같은 순서가 훨씬 구현이 쉽고 점진적으로 코드가 구현될 수 있다.
왜냐하면 "검색어가 2글자 미만일 경우" 일종의 기본적인 유효성 검증 성격을 띄기 때문에 제일 먼저 수행함으로써 구현이 훨씬 간편하기 때문이다.

- [ ] 검색어가 2글자 미만일 경우 상품을 리턴하지 않는다. -> 1th
- [ ] 검색 시 검색어의 대소문자를 구분하지 않는다. -> 2nd
- [ ] 제품의 **이름**으로 검색했을때 검색어가 포함된 모든 상품들을 리턴 -> 3th

> "검색어가 2글자 미만일 경우 상품을 리턴하지 않는다." 같은 데이터의 유효성 검증은 어디서 이루어지는 것이 좋을까 에 대한 고민도 같이 해보면 좋을 것 같다. 본 예시에서는 설명을 위해 추가해봤다.

### 굳이?

글을 여기까지 읽으신 분들 중 spring jpa에 익숙하신 분들은 고개를 갸우뚱 하실 수도 있을 것 같다.

![Desktop View](/assets/posts/spring/springData/querydsl-search-test-implementation/findAllByNameContainingCase.png){: width="972" height="589"}_JPA 쿼리 메서드_

Spring Data JPA에서 제공하는 쿼리 메서드를 사용하면 한 줄로 위에서 구현한 쿼리를 사용할 수 있다.

이를 통해 말하고 싶었던 점은 언제 querydsl 과 jpa 적절히 사용해야하는가에 대한점 ?


https://docs.spring.io/spring-data/jpa/reference/jpa/specifications.html

https://brunch.co.kr/@anonymdevoo/40
https://velog.io/@jmjmjmz732002/SpringBoot-QueryDSL-JPA-1-%EC%82%AC%EC%9A%A9-%EC%9D%B4%EC%9C%A0-%EC%83%9D%EA%B0%81%ED%95%B4%EB%B3%B4%EA%B8%B0


## 마무리
