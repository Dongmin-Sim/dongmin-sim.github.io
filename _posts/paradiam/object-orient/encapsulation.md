---
title: 캡슐화
author: codongmin
date: 2024-04-16T14:12:00
categories: 
tags: 
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 캡슐화 (encapsulation)

객체의 **상태**와 **행동**을 하나로 묶고 내부의 **구현내용**은 일부 숨기는 것을 의미한다. 
캡슐화의 목적은 변경에 영향을 통제하기 함에 있다. 

상대적으로 변경 가능성이 높은 부분은 내부로 숨기고, 변경 가능성이 적은 부분은 외부로 드러냄으로써 **변경의 영향을 최소화**할 수 있다. 

외부로 드러난 부분을 **인터페이스**, 내부로 숨겨진 부분을 상세 **구현**이라고 부른다. 

캡슐화가 중요한 이유는 **불안정한** 부분과 **안정적인** 부분을 **분리**해내어 **변경의 여파를 통제**할 수 있음에 있다.
변경의 여파를 통제할 수 있다는 것은 요구사항의 변경에 **유연한 대처**를 예상할 수 있다는 뜻과도 일치한다. 


객체의 캡슐화는 언어적인 측면에서 지원하는 접근제어자나 데이터 수정자를 통해 가능하다. 

```java
private 


setType()

```