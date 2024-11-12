---
title: equals, hashcode를 왜 재정의해야 하나요?
author: codongmin
date: 2024-11-12T19:04:00
categories: 
tags: 
preview: 
image:
  path: /assets/img/thumbnail/why-do-we-have-to-override-equals-and-hashcode-thumbnail.png
  lqip: 
  alt: equals, hashcode를 왜 재정의해야 하나요?
---

## 들어가며

객체비교란, 
왜중요한지?


## equals()와 hashCode()란 무엇인가?

## 어떻게 동작하나요?

## 왜 equals()와 hashCode()를 재정의해야 하나요?

재정의해야하는 이유 

둘중 하나만 재정의했을때 발생할 수 잇는 문제점


## equals()와 hashCode()를 재정의하지 않으면 어떤 문제가 발생하나요?


### 컬렉션이 엉망진창?

hashCode" 를 잘못 오버라이딩하면 "HashMap" 등 hash 콜렉션의 성능이 떨어질 수가 있습니다. 어떤 케이스일 때 그럴 수 있을까요?

1. **해시 충돌 증가**: 동일한 해시 코드를 반환하는 객체들이 많아지면 성능 저하가 발생합니다.
2. **복잡한 연산 사용**: 불필요하게 복잡한 연산을 포함하면 성능 저하를 유발합니다.
3. **고르지 않은 분포**: 특정 값에 치우친 해시 코드 분포는 일부 버킷에 데이터가 몰리게 합니다.
4. **불변성 원칙 위반**: 가변 필드를 사용하여 해시 코드를 계산하면, 필드 값 변경 시 문제가 발생합니다.

- 기존 "HashMap" 의 시간복잡도는 얼마이고, "hashCode" 를 잘못 오버라이딩 했을때의 시간복잡도는 얼마일까요?


## equals()와 hashCode()를 올바르게 재정의하는 방법은?


## 재정의하지 말아야할 때는 언제인가요?


## 마무리
