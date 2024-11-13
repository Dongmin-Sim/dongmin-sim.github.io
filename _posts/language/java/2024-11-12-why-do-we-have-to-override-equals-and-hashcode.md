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

`equals()` 와 `hashcode()` 메서드는 기본적으로 `java`의 `Object` 클래스에서 제공하는 메서드로, 이 메서드들은 객체의 고유한 값을 기준으로 동등성을 판단한다. 하지만 비즈니스 로직을 구현하기 위한 도메인 객체에서는 논리적 동등성을 기준으로 비교해야하는 경우가 많다. 이를 올바르게 재정의하지 않을 경우 예상치 못한 오류를 만날 수 있다.

프로젝트를 진행하면서 도메인 객체의 비교를 위해서 `equals()` 와 `hashcode()`를 재정의해서 사용하곤 했는데, 이번 글을 통해 `equals()` 와 `hashcode()`에 대해서 깊게 알아보려고 한다. 이 글에서는 다음과 같은 내용들을 다룬다. 
- `equals()` 와 `hashcode()`의 정의 
- `equals()` 와 `hashcode()` 동작 방식 
- `equals()` 와 `hashcode()` 오버라이드 해야하는 이유
- `equals()` 와 `hashcode()` 오버라이드하지 않았을때 발생할 수 있는 문제점 
- `equals()` 와 `hashcode()` 오버라이드하는 기준

`equals()` 와 `hashcode()`를 오버라이드 해야하는 이유를 명확히 알고있지 않거나, 원리를 모른채 습관적으로만 오버라이드를 했던 경험이 있다면 해당 글의 개념을 익히는데 도움이 될 수 있다. 

`equals()`와 `hashcode()` 를 올바르게 재정의하는 것은 애플리케이션 서비스의 안정성과 성능을 보장하기 위해 꼭 필요한 작업이다. 

## equals()와 hashCode()란 무엇인가?

### 객체의 비교 방식 

일반적으로 객체를 비교하는 방식에는 크게 2가지가 있다. 
- 동일성 비교 
- 동등성 비교

두 객체가 물리적으로 동일한지를 판단하는 것을 동일성 비교라고 하고, 
두 객체가 논리적으로 동일한지를 판단하는 것을 동등성 비교로 구분한다

자바에서는 `==` 이라는 비교연산자를 통해서 동일성 비교를 할 수 있다. 
`==` 비교 연산자는 참조형 타입의 객체에 대해 메모리 주소를 비교하도록 동작한다. 

값타입의 경우에는 값 자체를 비교하고, 참조형 타입의 경우 두 객체가 같은 인스턴스인지 여부를 판단한다.

그렇다면 자바에서 두 객체가 논리적으로 동일한지는 어떻게 판단할 수 있을까?
### equals
`equals()` 메서드는 두 객체가 서로 논리적으로 동등한지 비교를 하기 위한 메서드다. 
`java`에서 모든 부모 객체가 되는 `Object`의 메서드이다.

기본적으로 이 메서드를 오버라이드하지 않을 경우 두 객체의 참조값을 기준으로 비교를 한다. 
즉, `==` 연산자와 동일한 방식으로 비교하게 된다.

```java
public boolean equals(Object obj) {  
    return (this == obj);  
}
```

### hashcode()
`hashcode()` 메서드는 객체를 식별할 수 있는 값을 반환하는 메서드이다. 
이 역시 `Object` 클래스에 정의된 메서드이다.





## 어떻게 동작하나요?



## 왜 equals()와 hashCode()를 재정의해야 하나요?

재정의해야하는 이유 

둘중 하나만 재정의했을때 발생할 수 잇는 문제점


## equals()와 hashCode() 오버라이드와 어떤 문제가 발생하나요?

## equals를 잘못 재정의할 경우



## hashcode를 잘못 재정의할 경우
### 컬렉션이 엉망진창?


hashMap 동작 원리 
put 과정 스택프레임

1. map.put(k, v) 
2. -> HashMap::put(k, v); 
3. -> HashMap::hash(key) 
```java
static final int hash(Object key) {  
    int h;  
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);  
}
```
4. -> key.hashcode()(여기서 오버라이드한 hashcode 메서드 참조) 
5. ->HashMap::putVal(hash(key), key, value, false, true) 진입 
```java
Node<K,V>[] tab; Node<K,V> p; int n, i;  
if ((tab = table) == null || (n = tab.length) == 0)  
    n = (tab = resize()).length;  
if ((p = tab[i = (n - 1) & hash]) == null)  
    tab[i] = newNode(hash, key, value, null);  
else {  
    Node<K,V> e; K k;  
    if (p.hash == hash &&  
        ((k = p.key) == key || (key != null && key.equals(k))))  /// equals 비교 
        e = p;  
    else if (p instanceof TreeNode)  
        e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);  
    else {  
        for (int binCount = 0; ; ++binCount) {  
            if ((e = p.next) == null) {  
                p.next = newNode(hash, key, value, null);  
                if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
                    treeifyBin(tab, hash);  
                break;  
            }  
            if (e.hash == hash &&  
                ((k = e.key) == key || (key != null && key.equals(k))))  
                break;  
            p = e;  
        }  
    }  
    if (e != null) { // existing mapping for key  
        V oldValue = e.value;  
        if (!onlyIfAbsent || oldValue == null)  
            e.value = value;  
        afterNodeAccess(e);  
        return oldValue;  
    }  
}  
++modCount;  
if (++size > threshold)  
    resize();  
afterNodeInsertion(evict);  
return null;
```



hashCode" 를 잘못 오버라이딩하면 "HashMap" 등 hash 콜렉션의 성능이 떨어질 수가 있습니다. 어떤 케이스일 때 그럴 수 있을까요?

1. **해시 충돌 증가**: 동일한 해시 코드를 반환하는 객체들이 많아지면 성능 저하가 발생합니다.
2. **복잡한 연산 사용**: 불필요하게 복잡한 연산을 포함하면 성능 저하를 유발합니다.
3. **고르지 않은 분포**: 특정 값에 치우친 해시 코드 분포는 일부 버킷에 데이터가 몰리게 합니다.
4. **불변성 원칙 위반**: 가변 필드를 사용하여 해시 코드를 계산하면, 필드 값 변경 시 문제가 발생합니다.

- 기존 "HashMap" 의 시간복잡도는 얼마이고, "hashCode" 를 잘못 오버라이딩 했을때의 시간복잡도는 얼마일까요?


## equals()와 hashCode()를 올바르게 재정의하는 방법은?


## 재정의하지 말아야할 때는 언제인가요?


## 마무리
