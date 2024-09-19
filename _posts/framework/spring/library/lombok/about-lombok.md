---
title: lombok
author: codongmin
date: 2024-03-17T22:16:00
categories:
tags:
  - build
  - ci/cd
image:
  path: /assets/img/thumbnail/java.png
  lqip:
  alt: Gradle이란?
---

## 들어가며

## lombok이란

> "Never write another getter or equals method again"

- Boilerplate code를 줄여주는 도구
- 생상성 향성에 기여해준다.

- spring initializer 지원
- 의존성 추가, 버전관리 등 설정에 큰 신경 쓰지 않아도 됨

## 장점

## 자주 사용하는 기능

### @Data

- @Getter, @Setter, @RequiresArgsConstructor, @ToString, @EqualsAndHasCode
- 편함
- 그래서 조심해야함. 원하지 않는 기능이나, 필요하지 않는 기능도 세트로 들어가기 때문에

### @Value

- 불변 객체를 만들 때 씀
- @Getter, @FieldDefaults(makeFinal=true, level=AccessLevel.PRIVATE), @AllArsConstructor, @ToString @EqualsAndHashCode

## but, 단점

- 각종 예상치 못한 동작

  - 사용자의 의도와 컴파일러의 눈을 피해가는 동작
  - final 필드에도 builder 어노테이션으로 설정이 가능함.

- 과도한 애노테이션, 관례 기반 코드 스타일
  - annotation processing과 annotation의 동작 예측이 어려움
- 명시적이고 테스트가 쉬운 코드로 회귀하려는 움직임
- 롬복 해방!

- @ToString 순환 참조 문제
  - 서로가 서로를 참조하고 있는 경우 print를 할경우 계속 서로를 참조하면서 스택오버 플로우 문제가 생김.
  - 주로 스프링 JPA 에서 잘 발생함.
  - 시리얼라이즈, 할때도 발생할 가능성
  - exclude 옵션으로 해결 가능 필드명적시

```java
public class Seat {
	@ToString.exclude privat final Car car;
}
```

## lombok 대안들

- kotlin data classes

- java 14 record 를 사용한 불변객체

```java
public record Student(
        String name,
        Integer age,
        Grade grade
) {
    private enum Grade {
        A, B, C, D, E, F
    }
}
```

- 필드에 접근할 때도 get, set 네이밍이 제거됨, 바로 프로퍼티에 접근하듯이 접근이 가능함.

record + static factory method

```java
public record StudentRecord(
        String name,
        Integer age,
        Grade grade
) {

    public static StudentRecord of(String name, Integer age, Grade grade) {
        return new StudentRecord(name, age, grade);
    }
    public enum Grade {
        A, B, C, D, E, F
    }
}
```
