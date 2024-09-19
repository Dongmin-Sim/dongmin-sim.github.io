---
title: Java 진영의 영속성 프레임워크(Persistence Framework)
author: codongmin
date: 2024-05-01T00:07:00
categories:
  - spring
  - spring data
tags:
  - iBATIS
image:
  path: /assets/img/thumbnail/spring-data.png
  lqip:
  alt: 스프링 데이터
---

## 들어가며

JPA 라는 기술이 대부분 표준으로 자리 잡았지만, 그 이전에는 어떤 기술들이 주로 쓰였고 현재까지도 주로 쓰이는지 확인해보려 한다.

## Persistenct Framework

Persistence Framework(지속성 프레임워크)는 소프트웨어 개발에서 **데이터**를 **영구적**으로 저장하고 **관리**하기 위한 **도구나 라이브러리**를 의미합니다.

1. **객체-관계 매핑(Object-Relational Mapping, ORM) 기능**: 데이터베이스의 테이블과 자바 객체 간의 매핑을 자동화하여 객체 지향 애플리케이션을 개발할 수 있도록 해야 합니다. 이를 통해 개발자는 SQL 쿼리를 직접 작성하는 대신 객체를 통해 데이터베이스에 접근할 수 있습니다.
2. **영속성 관리(Persistence Management)**: 객체의 상태를 데이터베이스에 저장하고, 필요할 때 다시 읽어올 수 있도록 해야 합니다. 또한 영속성 컨텍스트를 관리하여 객체의 변화를 추적하고 데이터베이스에 반영할 때 일관성을 유지해야 합니다.
3. **트랜잭션 관리(Transaction Management)**: 데이터베이스 작업을 트랜잭션으로 묶어서 원자성, 일관성, 독립성, 지속성(ACID)를 보장해야 합니다. 이를 통해 데이터베이스 작업의 일관성과 안정성을 유지할 수 있습니다.
4. **쿼리 언어(Query Language) 지원**: 객체를 사용하여 데이터베이스에 쿼리를 수행할 수 있는 쿼리 언어를 제공해야 합니다. 이를 통해 SQL 쿼리 작성을 줄이고 객체 지향적으로 데이터베이스에 접근할 수 있습니다.
5. **성능 및 확장성**: 대규모 데이터베이스 작업을 처리하고, 성능을 최적화할 수 있어야 하며, 확장이 용이해야 합니다.
6. **다양한 데이터베이스 지원**: 다양한 데이터베이스 시스템과의 호환성을 지원하여 벤더 종속성을 최소화해야 합니다.

이는 데이터베이스와의 상호 작용을 단순화하고 개발자가 데이터를 효율적으로 다룰 수 있도록 돕는 것을 목적으로 합니다. 대표적인 Persistence Framework로는 Hibernate, Entity Framework, Django ORM 등이 있습니다. 이러한 프레임워크는 데이터베이스와의 상호 작용을 추상화하여 개발자가 보다 쉽게 데이터를 다룰 수 있도록 도와줍니다.

자바 진영에서는 다양한 Persistence Framework가 있습니다. 가장 대표적인 것은 Hibernate입니다. Hibernate는 자바 언어를 위한 ORM(Object-Relational Mapping) 프레임워크로, 객체 지향 프로그래밍 언어와 관계형 데이터베이스 사이의 데이터를 자동으로 매핑해주는 기능을 제공합니다. 이를 통해 개발자는 SQL 쿼리를 직접 작성하는 대신 자바 객체를 통해 데이터베이스와 상호 작용할 수 있습니다.

또한, Java Persistence API(JPA)도 많이 사용됩니다. JPA는 자바에서 관계형 데이터베이스를 다루기 위한 API 표준입니다. 이를 구현한 구현체로는 Hibernate가 있으며, EclipseLink, Apache OpenJPA 등이 있습니다.

그 외에도 MyBatis, Spring Data JPA, Apache Cayenne 등 다양한 Persistence Framework가 있습니다. 개발자는 프로젝트의 요구 사항과 선호도에 따라 적합한 Persistence Framework를 선택할 수 있습니다.

## SQL mapper vs ORM

1. **SQL 매퍼(SQL Mapper)**:

   - SQL 매퍼는 SQL 쿼리와 자바 메소드를 매핑하여 데이터베이스와 상호작용하는 방식을 강조합니다. 개발자는 직접 SQL 쿼리를 작성하고, 이를 자바 메소드에 매핑하여 데이터베이스에 접근하고 작업합니다.
   - 대표적인 예로는 MyBatis나 jOOQ와 같은 라이브러리가 있습니다.
   - SQL 매퍼를 사용하면 개발자가 데이터베이스에 대한 세부적인 제어를 할 수 있으며, 복잡한 쿼리를 더욱 효과적으로 작성할 수 있습니다.

대표적으로, SQL mapper, JdbcTemplate MyBatis 를 알아보고자 한다.

- 개발자는 SQL만 작성하면 해당 SQL의 결과를 객체로 매핑해주는 역할
- JDBC를 직접 사용할 때 발생하는 여러가지 중복을 제거해주고, 개발자에게 여러가지 편리한 기능을 제공함.

2. **ORM (Object-Relational Mapping)**:

   - ORM은 객체와 데이터베이스 간의 매핑을 중심으로 동작하는 개념으로, 객체를 데이터베이스 테이블에 자동으로 매핑하여 객체 지향적인 애플리케이션 개발을 지원합니다.
   - 개발자는 SQL 쿼리 대신 객체를 통해 데이터베이스에 접근하고 작업할 수 있습니다.
   - ORM을 사용하면 객체 간의 관계를 표현하고, 데이터베이스와의 상호작용을 객체 지향적으로 처리할 수 있습니다.
   - 대표적인 ORM 프레임워크로는 Hibernate, EclipseLink, Doctrine 등이 있습니다.

   - JdbcTemplate이나 MyBatis 같은 SQL 매퍼 기술은 SQL을 개발자가 직접 작성해야하지만, JPA를 사용하면 기본적인 SQL은 JPA가 대신 작성하고 처리해준다. 개발자는 저장하고 싶은 객체를 마치 자바 컬랙션에 저장하고 조회하듯이 사용하면 ORM 기술이 데이터베이스에 해당 객체를 저장하고 조회해준다.
   - JPA는 자바 진영의 ORM 표준이고, Hibernate는 JPA에서 가장 많이 사용하는 대표적인 구현체이다. 자바에서 ORM을 사용할 때는 JPA 인터페이스를 사용하고, 그 구현체로 하이버네이트를 사용한다고 생각하면 된다.
   - 스프링 데이터 JPA, QueryDsl은 JPA를 더 편리하게 사용할 수 있게 도와주는 프로젝트이다. 실무에서는 JPA를 사용하면 이프로젝트도 꼭 함께 사용하는 것이 좋다.

요약하면, 영속성 프레임워크는 객체와 데이터베이스 간의 매핑을 중심으로 동작하며 객체 지향적인 방식으로 데이터베이스에 접근합니다. 반면에 SQL 매퍼는 SQL 쿼리와 자바 메소드를 직접 매핑하여 데이터베이스와 상호작용하는 방식을 강조합니다.

---

## SQL Mapper

### Apache iBATIS

SQL 데이터 베이스와 객체간의 매핑을 지원해주는 persistence Framework.

#### 특징

- 지원 언어 : java, .NET, Ruby
- SQL문을 **별도로 XML** 문서로 작성하여 프로그램 코드와 **분리**한 형식

#### 역사

- ## 2001년 Clinton Begin 이 개발
- 2004년 iBATIS 2.0 릴리스 - 아파치 소프트웨어 재단에 기증되어, 6년 정도 운영
- 2010년 iBATIS 3.0 릴리스 - MyBatis로 프로젝트 변경, 아파치 attic 프로젝트로 분류됨(소프트웨어 수명 만료)
- DAO 패턴이 발전하던 시기
  - Data Access Object 패턴 : 애플리케이션 비지니스 레이어와 영속성 레이어를 추상화된 API를 이용하여 분리하는 패턴
  - DB 접근 구현 클래스를 Dao 라고 네이밍하느 관례가 많았다고 한다.

#### 사용 예시

```xml
<select id="getProduct" parameterClass="'"java.lang.Long" resultCalss="com.exmaple.Product">
	select PROD_ID as id,
		PROD_DESC as description
	from PRODUCT
	where PROD_ID=#value#
</select>
```

- 미리 설정해둔 별도의 SQL 문을 xml 코드로 가지고 있고, 조건들을 파라미터화 해서 사용하는 방식
-

```java
Product resultProduct = (Product)sqlMapClient.queryForObject("getProduct", 1);
```

---

### MyBatis

#### 특징

- 다양한 프레임워크와의 연동을 지원
- 스프링, 스프링 부트 연동을 지원
  - spring : org.mybatis:mybatis-spring
  - spring-boot : org.mybatis.spring.boot:mybatis-sprint-boot-starter
- ORM : 자바 객체와 DB 테이블을 매핑
- MyBatis : 자바 메소드를 SQL 실행 결과와 매핑
- 어노테이션 방식의 query 문을 지원

#### 역사

- iBATIS 3.0 에서 출발한 persistence framework
- 현재까지도 현역인 프로젝트

#### 예시

- xml 방식

```xml
<?xml version="1.0 encoding="UTF-8" ?>
<!DOCTTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.exmaple.BlogMapper">
	<select id="selectBlog" resultType="Blog">
		select * from Blog where id=#{id}
	</select>
</mapper>
```

- annotation 방식

```java
package org.mybatis.exmaple;

public interface BlogMapper {
	@Select("select*from Blog where id=#{id}")
	Blog selectBlog(int id);
}
```

```java
BlogMapper mapper = session.getMapper(BlogMapper.class);
Blog blog = mapper.selectBlog(1);
```

- SQL의 결과를 BlogMapper 인터페이스의 selectBlog 메서드에 매핑하는 방식
- 자바 객체와 데이터베이스의 테이블과의 매핑관계를 갖지는 않는다.

---

### JDBC API (Spring JDBC)

- 스프링에서 제공하는 jdbc 기반 persistence framework

  - spring-boot-starter-jdbc

- jdbcTemplate : Spring JDBC에서 제공하는 템플릿 클래스, 쿼리 실행과 결과 전달 기능을 제공

#### 특징

JDBC(Java Database Connectivity) API는 Java 언어로 데이터베이스에 접근하고 쿼리를 실행하기 위한 표준 인터페이스입니다. 이 API는 Java 어플리케이션과 다양한 데이터베이스 사이의 통신을 지원합니다. Spring 프레임워크는 Spring JDBC라는 모듈을 제공하여 JDBC를 더 쉽게 사용할 수 있도록 도와줍니다.

Spring JDBC의 주요 특징은 다음과 같습니다:

1. **간결한 코드**: Spring JDBC는 일반적인 JDBC보다 코드를 더 간결하게 작성할 수 있도록 돕습니다.
2. **예외 처리**: SQLException 등의 예외 처리를 보다 간편하게 해줍니다.
3. **자원 관리**: 연결, 문장, 결과 집합 등의 자원을 자동으로 관리하여 메모리 누수를 방지합니다.
4. **객체 매핑**: ResultSet에서 자동으로 객체를 매핑하는 기능을 제공하여 개발자가 직접 매핑을 작성할 필요가 없습니다.

#### 역사

- Spring JDBC의 역사는 Spring 프레임워크의 초기 버전부터 시작되었습니다. 초기 버전의 Spring은 JDBC를 좀 더 쉽게 사용할 수 있도록 돕는 여러 가지 유틸리티 클래스와 기능을 제공했습니다.

- 그러나 Spring JDBC 모듈이 명시적으로 도입된 것은 Spring 2.0 버전부터입니다.
- Spring 2.0에서는 JDBC를 더욱 편리하게 사용할 수 있도록 JdbcTemplate과 NamedParameterJdbcTemplate과 같은 클래스들이 도입되었습니다.

- Spring 3.0에서는 Spring JDBC 모듈이 더욱 발전하였고, JDBC를 사용하는데 있어서의 편의성과 유연성이 더욱 향상되었습니다. RowMapper와 ResultSetExtractor와 같은 인터페이스를 이용하여 객체 매핑이나 결과 집합 처리를 더욱 유연하게 할 수 있게 되었습니다.

#### 예시

- JDBC 템플릿

```java
@SpringBootApplication
public class RelationalDataAccessApplications implements CommandLineRunner {

	public static void main(String args[]) {
		SpringApplication.run(RelationalDataAccessApplication.class, args);
	}
	@Autowired JdbcTemplate jdbcTemplate;

	@Override
	public void run(String...strings) throws Exception {
		jdbcTemplate.execute("DROP TABLE customers IF EXISTS");
		jdbcTemplate.execute("CREATE TABLE customers(id SERIAL, first_name VARCHAR(255), last_name VARCHAR(255))");

		List<Object[]> splitUpNames = Array.asList("John Woo", "Jeff Dean")
				.steram()
				.map(name->name.split(" "))
				.collect(Collectors.toList())

		jdbcTemplate.batchUpdate(
			"INSERT INTO customers(first_name, last_name) VALUES(?,?)"
			,splitUpNames
		);

		jdbcTemplate.query(
			"SELECT id, first_name, last_name FROM customers WHERE first_name=?",
			new Object[]{"Josh"},
			(rs, rowNum) -> new Customer(
				rs.getLong("id"),
				rs.getString("fist_name"),
				rs.getString("last_name")
			).forEach(customer->System.out.println(customer.toString()));
		)
	}
}
```

- JDBC 템플릿

```java
import javax.sql.DataSource;
import org.springframework.jdbc.core.JdbcTemplate;

public class EmployeeDAO {
    private JdbcTemplate jdbcTemplate;

    public void setDataSource(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    public int getEmployeeCount() {
        String sql = "SELECT COUNT(*) FROM employees";
        return jdbcTemplate.queryForObject(sql, Integer.class);
    }

    public String getEmployeeName(int employeeId) {
        String sql = "SELECT name FROM employees WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new Object[]{employeeId}, String.class);
    }

    public void updateEmployeeName(int employeeId, String newName) {
        String sql = "UPDATE employees SET name = ? WHERE id = ?";
        jdbcTemplate.update(sql, newName, employeeId);
    }
}

```

---

### 정리

- 프로그램 코드에서 완전히 SQL를 분리하지 못함.
  - 개발자가 여전히 SQL을 알아야함,
  - 프로그램이 특정 데이터베이스에 종속된 SQL을 알아야함. 코드와 특정 DB 간의 의존성이 발생
  - XML 관리, SQL을 분리하는 목적으로 만들었지만, 관리해야할 속성이 하나더 늘어남.
- type-safe 하지 않음
  - 쿼리 실행 겨로가는 대체로 map, resultset 구조로 넘어오는데, 결국 매핑은 개발자가 구현,
  - map 구조가 데이터 클래스와 비교해 갖는 단점은
  - 어떤 "필드(key)"가 있음을 보장하지 않고,
  - 각 데이터의 타입을 보장하지 않음

## ORM

### JPA

JPA interface --- 구현체 hibernate --- spring-data-jpa( hibernate )

### Hibernate

Hibernate는 JPA(Java Persistence API)의 구현체 중 하나입니다. JPA는 자바에서 영속성을 관리하기 위한 표준 인터페이스를 제공하는 API이며, 객체와 관계형 데이터베이스 간의 매핑을 표준화하고 ORM 기능을 통합하는 데 중점

- 자바 생태계를 대표하는 ORM 프레임워크
- 스프링 부트에서 채택한 메인 ORM
- JPA 표준 스펙을 구현한 구현체
- 고성능 , 확장성, 안정성을 표방
  - Hibernate ORM
  - Hibernate Validator
  - Hibernate Reactive

#### 특징

- Hibernate Query Language
  - 하이버네이트가 사용하는 SQL 스타일의 비표준 쿼리 언어
  - 객체 모델에 초점을 맞춰 설계되었음
  - JPQL의 바탕이 됨, (JPQL은 HQL의 서브셋)
  - JPQL은 HQL이 될 수 있지만, 그 역은 성립하지 않음.

```java
Query query = entityManager.createQuery(
	"select p from Person p where p.name like :name"
);

TypedQuery<Person> typedQuery = entityManager.createQuery(
	"select p from Person p where p.name like :name", Person.class
);
```

> [!NOTE] JPQL이란
> JPA(Java Persistence API)에서 사용되는 객체 지향 쿼리 언어
> SQL과는 다르게 데이터베이스 **테이블**이 아닌 **엔티티**와 **필드**를 대상으로 쿼리를 작성합니다.
>
> JPQL은 **엔티티 클래스**와 그 안에 정의된 필드를 기반으로 쿼리를 작성하며,
> 데이터베이스 테이블이 아닌 **영속성 컨텍스트**에 로드된 **엔티티** 객체를 대상으로 쿼리를 수행합니다. 이를 통해 **객체지향적**인 쿼리 작성이 가능하며, 객체 간의 **관계**를 쉽게 다룰 수 있습니다.

- criteria query
  - type-safety를 제공하는 JPQL의 대안 표현법

```java
CriteriaBuilder builder = entityManager.getCriteriaBuilder();

CriteriaQuery<Person> criteria = builder.createQuery(Person.class);
Root<Person> root = criteria.from(Person.class);
criteria.select(root);
criteria.where(builder.equal(root.get(Person_.name), "John Doe"));

List<Person> persons = entityManager.createQuery(criteria).getResultList();
```

- native SQL query
  - 특정 DB에 종속된 SQL도 사용가능

```java
List<Object[]> Persons = entityManager.createNativeQuery(
	"SELECT * FROM Person"
).getResultList();
```

설정 파일은 META-INF/persistence.xml 로 작성

```

```

### 예시

엔티티 클래스로 객체와 테이블 관계를 정의

```java
@Entity
@Table(name="EVENTS")
public class Evnet {

	@Id
	@GeneratedValue(generator="increment")
	@GenericGenerator(name="increment", strategy="increment")
	public Long getId() {
		return id;
	}
	...
}
```

![[Pasted image 20240501012948.png]]

---

### Spring Data JPA

spring 에서 제공하는 jpa 추상화 모듈.??

- JPA 구현체의 사용을 한 번 더, Repository라는 개념으로 추상화
  - 일종의 wrapper?
- JPA 구현체의 사용을 감추고, 다양한 지원과 설정 방법을 제공
- JPA 기본 구현체로 Hibernate 사용
- Querydsl 지원
  - Querydsl은 자바에서 타입 안전한 동적 쿼리를 작성할 수 있는 라이브러리

#### 특징

- JPA, 하이버네이트를 직접적으로 사용하지 않아도됨.
  - entity manager 를 직접 다루지 않아도 됨 .
  - JPQL을 직접 사용하지 않음.
  - persist(), merge(), close()를 직접 사용하지 않음.
  - 트랜잭션을 getTransaction(), commit(), rollback()으로 관리하지 않을 수 ㅣㅇㅆ음/
  - 하이버네이트를 한번 더 spring-data-jpa가 추상화하여서, 자세한 구현 부분은 감추고 spring-data-jpa 제공하는 편리한 방법으로 사용할 수 있음.
- spring-data-jpa를 사용하지만, 코드가 하이버네이트나 entity manager를 직접 사용하고 있다면,
  - 하이버네티트와 직접적인 연관관계가 맺어짐.
  - spring-data-jpa의 추상화 이점이 없어짐 .

#### 예시

-

### QuertDsl

---

## 마무리
