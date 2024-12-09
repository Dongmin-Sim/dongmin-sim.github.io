---
title: equals를 재정의할 때 왜 hashcode도  재정의해야 하나요?
author: codongmin
date: 2024-11-12T19:04:00
categories:
  - Java
  - Common
tags:
  - Java
  - Object
  - Equals
  - HashCode
preview: equals 와 hashcode 메서드에 대해 알아보고 이 두 메서드의 관계와 두 메서드를 일관성있게 재정의해야하는 이유에 대해 설명합니다.
image:
  path: /assets/img/thumbnail/why-do-we-have-to-override-equals-and-hashcode-thumbnail.png
  lqip: 
  alt: equals, hashcode를 왜 재정의해야 하나요?
---

## **들어가며**

프로젝트를 진행하면서 도메인 객체의 `equals()` 와 `hashcode()`를 **습관적으로 재정의**해서 사용하곤 했다. 이번 글을 통해 `equals()` 와 `hashcode()`과의 관계를 알아보고 왜 이 둘을 일관성있게 재정의해야만 하는지에 대해서 알아보려고 한다.

`equals()` 와 `hashcode()`를 오버라이드 해야하는 이유과 두 메서드 간의 관계를 명확히 알고있지 않거나, 나처럼 원리와 이유를 모른채 습관적으로만 오버라이드를 했던 경험이 있다면 본 글에서 관련 개념을 익히는데 도움이 될 수 있다. 

반면에 이 글의 대상이 아닌 독자는 다음과 같다. 
- `equals()`와 `hashcode()` 메서드에 대한 기본적인 이해와 두 메서드 간의 관계를 잘 알고 있는 사람.
- `Hash` 기반 자료구조의 동작 방식과 `hashcode()`메서드와의 관계를 이해하고 있는 사람.
- `equals()`와 `hashcode()` 메서드를 재정의하지 않았을때 발생할 수 있는 문제에 대해 이해하고 있는 사람.


🙏 **시작하기 전에**  
이 내용을 이해하려면 **해시 자료구조**에 대한 전반적인 이해와, **해시 충돌**이라는 개념에 대해서 이해가 있으면 좋다. 만약 잘 모르겠거나 기억이 나지 않는다면, 이에 대해서 설명해둔 글이 있어 한번 읽어보는 것을 권한다. 

👉 [해시 자료구조와 Java HashMap이 해시 충돌을 해결하는 방법](https://dongmin-sim.github.io/posts/hash-data-structure/)

<br>

--- 

<br>
## **1. equals()와 hashCode()란**

`equals()` 와 `hashcode()` 메서드는 `java`에서 모든 부모 객체가 되는 `Object`의 메서드이다.

### **1.1 객체의 비교 방식: 동일성 동등성** 

일반적으로 객체를 비교하는 방식에는 크게 2가지가 있다. 
- 동일성 비교 (`identity`)
- 동등성 비교 (`equality`)

두 객체가 **물리적으로 같음**을 판단하는 것을 **동일성** 비교라 하고,   
두 객체가 **논리적으로 같음**을 판단하는 것을 **동등성** 비교라 한다.

#### **1.1.1 동일성 (identity)**
자바에서는 `==` 이라는 **비교연산자**를 통해서 **동일성 비교**를 할 수 있다.   

`==` 비교 연산자는 참조형 타입의 객체에 대해 메모리 주소를 비교하도록 동작한다. 메모리 상에서 같은 위치를 차지하고 있는지를 판단한다.

기본형 타입의 경우에는 값 자체를 비교하고, 참조형 타입의 경우 두 객체가 같은 인스턴스인지 여부를 판단한다.

#### **1.1.2 동등성 (equality)**

그렇다면 자바에서 두 객체가 **논리적으로 동등한지는** 어떻게 판단할 수 있을까?  
그전에 "논리적으로 동등하다"라는 말은 무슨말일까? 

내 생각에 **논리적으로 동등**하다는 것은 **특정 조건**을 기준으로 두 객체가 **개념적**으로, **의미적**으로 "같음"을 의미한다. 두 객체가 물리적으로 같거나 다를 수 있지만, **개념적으로 동일한 의미**를 가지는 경우를 말한다. 

**특정 조건**이 가질 수 있는 특징과 예시들을 다음과 같이 정리해봤다.
1. 물리적 차이와 무관하고
2. 공통된 추상적 속성을 가지고 있을때,

- 같은 나무로 만들어지지 않았지만, 같은 제목과 내용을 가진 2개의 책은 논리적으로 서로 같다. 
	- 같은 나무로 만들어졌어도, 다른 제목과 다른 내용이라면 두 책은 논리적으로 서로 다르다.
- "빨강"이라는 색상과 `RGB(255, 0, 0)` 은 서로 같은 색상이다. (물론 어떤 빨강이냐에 따라 다르겠지만)

|       | 동일성            | 동등성              |
| ----- | -------------- | ---------------- |
| 의미    | "물리적으로 같음"을 판단 | "논리적으로 같음"을 판단   |
| 비교 기준 | 단편적            | 복합적, 맥락에 따라 달라짐. |
| 비교 수단 | `==`           | `equals()`       |

### **1.2 equals()**
일단, `equals()` 메서드는 두 객체가 서로 **논리적으로 동등한지** 비교를 하기 위한 메서드다.   

다음 예시로 `euqals()` 메서드의 필요성을 살펴보자
```java 
public class Money {  
    private BigDecimal amount;  
    private String currency;  
  
    public Money(BigDecimal amount, String currency) {  
        this.amount = amount;  
        this.currency = currency;  
    }  
}
```

위 클래스는 금액과 통화를 표현하기 위한 클래스로, 금전 데이터를 하나의 객체로 추상화하여 다루고자 설계되었다. 금액을 다루다보면 서로 같은지 다른지, 어떤 금액이 더 큰지 비교해야하는 순간이 꼭 필요하다. 

왼손과 오른손에 각각 `5,000`원씩 들려있다면 이 둘은 서로 '**같은**' 돈이라고 볼 수 있는가?   

이 돈이 만들어진 '날짜'나 만들어질때 들어간 '재료'와 같은 비교 조건들은 우리가 돈을 비교하는 본 목적과는 거리가 있다. 우리가 기대하는 것은 돈이 가진 **추상적 의미의 가치**를 비교하고 싶기 때문이다.

따라서 우리가 기대하는 현실세계에서의 답은 `yes` 일 것이다. 

이를 `Money`객체로 구현해 다음과 같이 비교한다면 어떤 결과가 나올까??
```java
public static void main(String[] args) {  
    Money moneyA = new Money(BigDecimal.valueOf(5000), "KRW");  
    Money moneyB = new Money(BigDecimal.valueOf(5000), "KRW");  
  
    System.out.println(moneyA.equals(moneyB)); // false
}
```

의도하고 기대했던 결과(`expected`)값은 `true`이지만,   
실제 비교 결과(`actual` )는 `false`이다. 

앞에서 "논리적으로 동등하다는 것은 특정 조건을 기준으로 두 객체가 개념적으로 '같음'을 의미"한다고 했다. 
`equals()`메서드는 "논리적으로 같음"을 "**어떤 조건**"으로 판단할지를 담당하는 메서드이다. 

결과 값이 기대와 다른 이유는 `equals()` 메서드를 오버라이드하지 않을 경우 두 객체의 참조값을 기준으로 비교를 기본동작으로 정의되어 있기 때문이다.

아래 코드는 `java.lang.Object`의 `equals()` 코드를 가져온 것이다. 코드에서 보이는 것과 같이  `==` 연산자와 동일한 방식으로 비교하게 된다.
```java
public boolean equals(Object obj) {  
    return (this == obj);  
}
```

따라서 개발자가 의도하는 객체의 올바른 비교를 위해서는 반드시 `equals()`를 "어떤 조건"(주로 객체의 값)으로 "논리적으로 같음"을 비교할 것인지 재정의해줘야만 한다. 

### **1.3 hashcode()**
`hashcode()` 메서드는 객체를 식별할 수 있는 고유한 **해시 값**을 반환하는 메서드이다. 

이때 반환하는 정수 값은 **객체의 해시 값**이라고 부르며, 이 해시 값은 주로 해시 기반 자료구조에서 객체를 식별해 효율적으로 관리하기 위해 사용된다. 

이 역시 `Object` 클래스에 정의된 메서드이다. 
```java
@IntrinsicCandidate  
public native int hashCode();
```

`hashCode()`메서드를 오버라이딩하지 않을 경우 기본동작에서 반환되는 정수 값은 객체의 메모리 주소, 혹은 이를 이용한 값을 반환한다. 사실 이는 JVM의 세부 구현 사항에 따라 다른데, `native` 예약어는 해당 메서드가 네이티브(C/C++) 코드로 구현되어있다는 것을 의미한다. 

- `@IntrinsicCandidate` : JVM이 해당 메서드를 호출할 때, 바이트 코드가 아닌, 네이티브 코드로 실행한다는 것을 의미. 
- `natvie` : Java가 아닌 네이티브(C/C++) 코드로 구현되어있음을 의미

중요한 것은 `hashcode()` 메서드는 해시 자료구조를 염두해둔, 메서드라는 것이다. 이를 재정의해서 사용자가 의도한 해시값을 리턴하도록 구현할 수 있다. 

때문에 `hashcode()`를 단순히  `객체 메모리 주소`를 반환하는 메서드라고 이해하는 것은 적절하지 않다.

### **1.4 정리** 
지금까지 `equals()`와 `hashcode()` 메서드에 대해서 알아봤다.

`equals()` 는 **논리적 동등성** 비교를 위한 메서드이기 때문에,   
"객체간의 의도된 비교를 위해서는 `equals()` 메서드를 적절하게 재정의해야한다"라는 것을 알아보았다. 

`hashcode()` 는 해시 기반 자료구조에서 객체를 식별하는 고유한 **해시 값을 반환**하기 위한 메서드이다. 

이 둘은 반대되는 역할을 하는 메서드도 아니고 전혀 다른 역할을 하는 메서드이다.   
얼핏보기엔 전혀 관련이 없어보이는 메서드들인 것 같다. 

그런데 두 메서드들은 왜 같이 언급되는걸까? 

<br>

---
<br>
## **2. equals와 hashcode의 관계, 그리고 발생할 수 있는 문제점** 

앞에서 살펴보았듯이 `equals()` 메서드는 개발자의 의도와 필요에 따라 재정의되어야 한다. 
그리고 `equals()` 메서드가 재정의된다면, `hashcode()` 메서드도 함께 **일관성 있게 재정의**되어야만 한다. 

이는 **객체를 비교해야하는 상황**에서 꼭 필요로 하다. 특히, 해시 기반 컬렉션에서 정상적으로(+개발자의 의도대로) 동작하는 것을 보장하기 위해서는 반드시 두 메서드를 일관되게 재정의해야만 한다.

**해시 기반 컬렉션의 동작과정**에서 `equals()`와 `hahscode()`를 활용하기 때문이다. 

### **2.1 해시 기반 컬렉션 동작방식에 기인한 문제 발생** 

해시 기반 컬렉션에서  `equals()`와 `hahscode()`와 관련된 문제가 발생하는 가장 큰 원인은, 해시 기반 컬렉션의 동작방식과 깊은 연관이 있다. 정확히 말하면 동작과정에서 객체를 서로 비교해야만 하기 때문이다.

![Desktop View](/assets/posts/language/java/2024-11-12-why-do-we-have-to-override-equals-and-hashcode/hashtable.png){: width="972" height="589" .w-75}_해시테이블 (출처:[해시 테이블 - 위키피디아](https://en.wikipedia.org/wiki/Hash_table))_

해시 기반 자료구조는 그림과 같이 `key`와 이에 대응되는 `value` 값을 저장하도록 설계되었으며, 이러한 특성 덕분에 빠르게 데이터를 조회할 수 있다. 

```java
public static void main(String[] args) {
    Map<String, String> phoneBook = new HashMap<>();
    
    priceTable.put("Sandra Dee", "521-9655"); // 데이터 삽입
}
```

`Java`의 대표적인 해시 기반 컬렉션인 `HashMap` 에서 데이터를 삽입할 때 다음과 같은 방식으로 동작한다.

#### 🗂️ 1. 삽입 위치 결정

해시 자료구조는 해시 함수를 통해 **`key` 객체의 해시 값을 계산**해서, 실제로 데이터가 저장될 버킷의 **인덱스 위치를 결정**한다.   

이때 해시 값을 계산하기 위해서 객체에 정의되어 있는 `hashcode()` 메서드를 사용된다. 

#### 💥 2. 해시 충돌 발생 시 객체 비교

> 해시 자료구조에 대해 생소하거나, 왜 충돌이라는 것이 발생하는지 알고싶다면   
👉 [해시 자료구조와 Java HashMap이 해시 충돌을 해결하는 방법](https://dongmin-sim.github.io/posts/hash-data-structure/)  

![Desktop View](/assets/posts/language/java/2024-11-12-why-do-we-have-to-override-equals-and-hashcode/hashtable-chaining.png){: width="972" height="589"}_해시 충돌 발생 시 (출처:[해시 테이블 - 위키피디아](https://en.wikipedia.org/wiki/Hash_table))_

해시함수에서 서로 다른 `key`에 대해 동일한 해시값을 반환하여, 버킷의 인덱스 주소가 같아져버리는 상황을 해시충돌이라한다. 해시 충돌이 발생할 경우 다음과 같은 순서로 해시 충돌을 해결하려한다.

- 만일, 인덱스 주소의 버킷에 이미 `Node`[^fn-nth-1]가 존재하는 경우. **동일 비교 시행** 
- 버킷에 연결된 모든 노드를 순차 탐색하면서 기존 값과 새로 삽입하려는 값과 **동일 비교 시행** [^fn-nth-2]
	- 만일 동일하면 중복 객체로 판단 후 새로운 `value`로 수정. (업데이트) 
	- 순회 결과 동일한 객체가 없다면, 새로운 객체 저장. (새로삽입)

이때 **동일 비교 시행**하는 방법으로 아래의 조건문을 사용한다.
이 조건의 결과가 참인 경우는, `hashMap`에 존재하는 값과 새로 삽입하려는 값이 같다는 것을 의미한다.
```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict){
    Node<K,V> e; K k;
    ...
    if (e.hash == hash &&  ((k = e.key) == key 
        || (key != null && key.equals(k))))  
        break;
    ...
    // (객체 수정 로직)
}
```

**동일 비교 시행**의 조건문을 나열해보면 다음과 같다. 다음 1, 2번 조건을 모두 만족해야 동일하다고 판단한다.
1. 노드의 해시 값과 새로운 `hash`값[^fn-nth-3]이 동일한지 판단한다. 
2. 다음 두 조건 중 하나라도 만족하는지 확인한다.
	- 노드의 `key`와 현재 키가 동일하거나 
	- 키가 `null` 값이 아니면서, `key`의 `equals`가 동일해야 한다.


이를 그림으로 표현하면 다음과 같다. 

![Desktop View](/assets/posts/language/java/2024-11-12-why-do-we-have-to-override-equals-and-hashcode/push.png){: width="972" height="589"}_`HashMap`의  데이터 삽입 과정 흐름도_

**동일 비교 시행**에서 동일하다고 판단되는 객체가 있다면, 이를 업데이트하고 그렇지 않다면 비로소 `HashMap`에 새로운 객체로 저장하게 된다.

`HashMap`에서는 값을 저장하고, 수정하는데 있어  `equals()` 와 `hashcode()`를 통해 노드간 동일 비교를 하게 된다. 때문에 `hashCode()` 와 `equals()` 를 반드시 일관되게 재정의해야 한다. 그렇지 않으면 해시 기반 컬렉션을 다룰 때 전혀 예상치 못한 문제와 마주할 수 있기 때문이다.  

> 위의 흐름도가 꼬이게 되면 어떤 일이 발생될지 생각해보자 🤔
{: .prompt-tip }

다음에는 `equals()` 와 `hashCode()` 를 일관성 있게 재정의하지 않았을 때 발생할 수 있는 문제점을 알아본다. 

### **2.2 두 메서드를 잘못 오버라이드하면?**


#### equals를 잘못 오버라이드하면?
동등한 객체이여야 하지만 `equals()`가 참이 아니라면 어떻게 될까?

![Desktop View](/assets/posts/language/java/2024-11-12-why-do-we-have-to-override-equals-and-hashcode/wrong-equals.png){: width="972" height="589" .w-75}_잘못된 equals_
![[wrong-equals.png]]
- 논리적으로 동등함을 보장하지 못한다면, 중복 값이 저장될 수 있다. 
- 같은 해시값을 가지는 바람에 버킷에 중복 저장되어 버킷의 길이가 길어질 수 있다.
- 이 경우 `HashMap`에 저장한 객체를 조회하는 것이 불가능할 수 있다.
	- `hash`값은 같기 때문에 `HashMap`에 저장할 때 사용한 객체를 `key`로 조회하면 조회되지만,
	- 모든 필드가 동일한 객체를 새로 생성해서 `key`로 조회하면 조회되지 않는다.

#### hashcode를 잘못 오버라이드하면?
동등한 객체이여야 하지만 다른 `hashCode()`가 나온다면 어떻게 될까?

![Desktop View](/assets/posts/language/java/2024-11-12-why-do-we-have-to-override-equals-and-hashcode/wrong-hashcode.png){: width="972" height="589" .w-75}_잘못된 hashCode_

- 이 경우 역시 논리적으로 동등한 두 객체가 중복 저장될 수 있다. 
- 두 객체가 다른 해시 코드를 가져 다른 버킷에 저장될 수 있다. 
- 이 경우도 `HashMap`에 저장한 객체를 조회하는 것이 불가능할 수 있다.
	- `hash`값은 같기 때문에 `HashMap`에 저장할 때 사용한 객체를 `key`로 조회하면 조회되지만,
	- 모든 필드가 동일한 객체를 새로 생성해서 `key`로 조회하면 조회되지 않는다.

<br>

--- 

<br>
## **3. equals()와 hashCode()의 재정의 규칙**

 객체 간 비교가 필요해 개발자의 필요에 맞게 `equals()`를 재정의했다면,앞서 살펴본 문제들을 예방하기 위해서는 `hashcode()`도 **일관된 재정의 규칙**을 따라야만 한다. 이때 지겨야만하는 사항들을 contract로 명시해두었다. 이 내용들은 [Java 공식 문서](https://docs.oracle.com/en/java/javase/19/docs/api/java.base/java/lang/Object.html#hashCode())에 있는 내용들이며, 다음과 같다.

### **3.1 `equals()`가 true를 반환하는 두 객체는 반드시 같은 `hashCode()` 값을 가져야 한다.**

앞에서 살펴보았듯이, `HashMap` 에서 먼저 `hashCode()`를 사용해 객체를 저장할 버킷(메모리 위치)을 결정하고, 이후 동일한 버킷 내에서 `equals()`를 사용해 객체의 동등성을 확인한다. 만약 이 규칙이 어겨지면, 논리적으로 동일한 객체임에도 불구하고 **서로 다른 버킷에 저장**될 수 있다. 중복이 될 수도 있다는 뜻이다.

만일 `equals()` `true`를 반환하는 두 객체가 다른 `hashCode()` 를 가질때는 어떻게 될까?
위 재정의 규칙의 반대 상황을 가정해서 테스트해보자. 

필드로 `id`와 `name`을 가지는`Product` 객체가 있고 두 필드를 기반으로 `equals` 를 재정의했다.   
`hashCode()`는 별도로 재정의하지 않았다. [^fn-nth-4]
```java
HashMap<Product, BigDecimal> productPriceTable = new HashMap<>();  
  
Product car1 = new Product(1, "car");  
Product car2 = new Product(1, "car");  
  
System.out.println("equals = " + car1.equals(car2));  
System.out.println("hashcode = " + (car1.hashCode() == car2.hashCode()));

------------------
> equals = true
> hashcode = false
```

이후 `Product` 를 `key` 로 가지는 `HashMap`을 정의하고, 논리적으로 동일한 두 `Product` 객체를 삽입하면 어떻게 될까? 

1️⃣ : `productPriceTable`에 하나의 `car`만 삽입된다.   
결과적으로 `productPriceTable`에는 중복이 제거된 한 개의 `car`만 존재한다.  

2️⃣ : `productPriceTable`에 두 `car` 모두 삽입된다.   
결과적으로 `productPriceTable`에는 중복된 두 개의 `car`들이  존재한다.  

일반적으로 기대하는 결과는 1️⃣ 번일 것이다. 하지만 결과를 확인해보면 다음과 같다. 
```java
productPriceTable.put(car1, BigDecimal.valueOf(1000));  
productPriceTable.put(car2, BigDecimal.valueOf(1500));  
  
productPriceTable.forEach((key, value) -> {  
    System.out.println("key: " + key + " value: " + value);  
});
------------------
> key: Product{id=1, name="car"}, value: 1000
> key: Product{id=1, name="car"}, value: 1500
```

결과적으로 equals()를 재정의함에도 불구하고, 2️⃣ 번 결과가 나온 것을 확인할 수 있다.
따라서 `equals()` 가 `true`를 반환하는 두 객체는 반드시 같은 `hashCode()`값을 가져야만 한다.

같은 필드 값을 가지는 객체를 생성해서 조회를 해보자.
```java
Product findCar = new Product(1, "car1");  
BigDecimal findCarPrice = productPriceTable.get(findCar);  

System.out.println("findCar equals car1 = " + findCar.equals(car1));  
System.out.println("findCarPrice = " + findCarPrice);
------------------
> findCar equals car1 = true
> findCarPrice = null
```

저장했던 `car1` `key`와 논리적으로 동등한 `key(findCar)` 이기 때문에 조회에 성공할 것 같지만, `hashCode` 가 다르기 때문에 조회도 불가능하다.

### 3.2 **`equals()`가 false인 두 객체는 서로 다른 `hashCode()` 값을 가질 필요는 없습니다.**

왜냐하면 서로 다른 `hashCode()`를 갖더라도, `equals()`를 통해 동일한 객체인지 비교하기 때문에, `equals()` 다른 객체가 꼭 `hashCode()`까지 다를 필요는 없다.

즉, 다른 객체가 동일한 해시 코드를 가질 수 있지만, 그 가능성을 최대한 줄이는 것이 좋다. 왜냐하면 해시 충돌(hash collision)이 빈번하게 일어나기 때문이다. 해시충돌이 많아질수록 해시 테이블의 성능이 저하될 수 있다.


> As far as is reasonably practical, the hashCode method defined by class Object returns distinct integers for distinct objects.
{: .prompt-info }

다음 주석은 `hashCode()`에 적힌 내용으로. `Java`의 `Object` 클래스에 정의된 [`hashCode` 메서드](https://docs.oracle.com/en/java/javase/19/docs/api/java.base/java/lang/Object.html#hashCode())가 가능한 한 실용적인 범위 내에서 서로 다른 객체에 대해 서로 다른 정수 값을 반환하도록 설계되었음을 이야기한다. 

### 3.3 **동일한 객체는 항상 동일한 `hashCode()` 값을 반환해야 함.**

프로그램 실행 중에 객체의 상태가 변경되지 않았다면, 동일한 객체는 항상 같은 해시 코드를 반환해야 한다. 
만일 동일한 객체임에서 서로 다른 `hashCode()` 를 반환할 경우 앞서 살펴보았던 "`hashCode()`를 잘못 오버라이드하면?"처럼 저장했던 객체가 중복저장되거나, 조회할 수 없게 될 수 있다. (매번 랜덤한 해시값을 반환하는 `hashCode()` 를 상상해보자)

이러한 규칙들을 외우고 직접 재정의하는 것도 좋지만, 특별한 경우가 아니라면 `Intellj`나 기타 IDE의 자동 생성 기능을 이용해서 재정의하는 것을 추천한다.

<br>

---

<br>
## **마무리**

지금까지 `equals()` 와 `hashCode()` 메서드와 이 두 메서드의 관계에 대해서 알아보고 `equals()` 가 재정의될 때 `hahsCode()`를 일관되게 재정의해야하는 이유에 대해서 알아보았다. 다루었던 내용들을 가볍게 복기해보자.

`equals()`는 개발자가 원하는 특정 기준에 맞추어 객체를 논리적으로 비교하기 위해서 개발자가 재정의할 수 있었다. `hashCode()`는 객체 고유의 식별자에 해당하는 해시값을 반환하기 위한 메서드였다. 

이  두 메서드가 일관되게 재정의하지 않았을때 발생할 수 있는 문제들이 대부분 객체들간의 비교상황에서 생겨난다는 것을 해시 기반 컬렉션을 예시로 설명했다. 해시 기반 컬렉션의 동작 원리를 자세히 알아보면서 어떻게 두 메서드가 연관되어있는지를 알아보았다. 해시 기반 컬렉션에서 주의해서 재정의하지 않으면 함께 발생할 수 있는 문제점들 또한 알아보았다.

이후에는 `hashCode()`를 적절하게 재정의해야하는 규칙을 살펴보면서 마무리했다.

물론 모든 `equals()`와 `hashCode()`를 재정의하지는 않아도 된다. 객체간의 비교가 필요한 경우에 위와 같은 내용들을 고려해서 재정의한다면 이들 객체를 비교하는 상황에서 의도하지 않은 에러나 예외를 방지할 수 있을 것이다. 

<br>

---

<br>

[^fn-nth-1]: 보관되는 `key`와 `value`을 `Node`에 담아보관한다. 이 경우에는 해당 `key`로 값이 존재하는 경우로 해시 충돌이 발생한 경우를 의미한다.
[^fn-nth-2]: Java의 `HashMap`은 충돌이 발생할 경우 일정 비율까지는 링크드 리스트 구조를 가지는 체이닝 방식으로 충돌을 해결한다. 하지만 Java JDK 1.8 이후부터는 일정 비율을 넘어갈 경우, 관리 효율을 위해 레드-블랙 트리로 변환하는 과정을 거친다. 순차탐색 이전에 링드리스트인지, 레드-블랙트리인지 확인하는 조건문 결과에 따라 서로 다른 방식으로 값을 순회한다.
[^fn-nth-3]: `key` 객체의 해시값을 의미한다. 보다 자세하세 설명하면 `keyVal(int hash)`로 넘어오는 값은 `(h = key.hashCode()) ^ (h >>> 16)` 값이 넘어오게된다.
[^fn-nth-4]: `hashCode` 를 재정의하지 않을 경우 객체 고유의 식별자를 반환한다. 


