---
title: 객체와 클래스, 그리고 객체를 만드는 방법들
author: codongmin
date: 2024-11-05T21:06:00
categories:
  - Java
tags:
  - 객체
  - 자바
preview: 객체와 클래스간의 관계, 클래스를 이루는 구성요소, 객체를 생성하는 방법들에 대해 알아봅니다.
---

# 0. 들어가며

프로그래밍 언어를 배우다보면 곧잘 만들게 되는게 클래스, 객체다. 조금 더 공부해 보다보면 생성자, 내부 클래스, 오버로딩 등 객체를 생성하는 과정속에서 새롭게 접하는 키워드들이 많다. 나의 경험에 비추어봤을때, 아 이런게 있구나 그런데 이건 대체 언제 사용하는거지? 일단 한번 봐두자 하고 당장의 흥미에 밀려 곧잘 잊어버렸다. 

글에서는 자바 언어의 객체의 생성에 관한 기초가 되는 클래스와 구성요소, 단순히 기본 생성자를 사용해 겍체를 만드는 방법 외에 다양한 방법으로 객체를 만들 수 있는 방법들을 다루고자 한다. 자바 언어의 기초내용이 어떻게 다른 프로그래밍 개념들과 연결될 수 있는지 중점으로 전개하는 것을 목표로 하고 있다.

이 글은 자바 기본서를 한번 정도 읽어보았지만 잘 감이오지 않거나, 과거의 나처럼 기초 내용에 흥미가 잘 가지 않는다면 도움이 될 수 있다.


## 0.1 객타듀밸리
앞으로 설명할 내용들을 마치 마을을 운영하는 게임에서의 캐릭터나 오브젝트 같은 존재로 상상해볼 수 있다. 마을은 잘 운영하기 위해서 캐릭터는 다른 캐릭터들과 거래를 하거나 게시판과 상호작용을 하기도 하고, 힘을 합쳐 마을의 어려운 일들을 처리하기도 한다.

앞으로 나올 내용들에서는 프로그램(혹은 애플리케이션)에서의 객체가 어떻게 구성되어있으며, 어떻게 생성되는지를 마을 운영 게임에 비유하면서 하나씩 살펴본다. 전지적 플레이어 시점이 되어 마을의 캐릭터들을 만들어보자.

![Desktop View](https://stardewvalleywiki.com/mediawiki/images/4/45/StardewValleyFair.png){: width="972" height="589"}_이미지 설명_


# 1. 클래스란?

**클래스**란 무엇인가요? 라는 질문을 하기전에 **객체란 무엇인가?** 에 먼저 답해야한다.   
왜냐하면 클래스는 객체를 만들기 위한 **템플릿**이기 때문이다. 

객체란 무엇인가? 라는 질문을 들으면 몽해지며 머리속이 추상적으로 변하는 느낌이 들며 입이 잘떨어지지 않는다. 한번 자신만의 정의를 떠올려보자

## 1.1 객체란?
객체에 대한 여러 정의들이 있지만, 나는 2가지로 구분한 객체의 정의*([객체지향의 사실과 오해](https://m.yes24.com/Goods/Detail/18249021))를 더 선호한다. 
**실물 세계의 객체**와 **프로그래밍 세계의 객체**로 구분하는 것이다. 

실물 세계에서의 객체는 말그대로 세상에 존재하는 모든 것들이다. 표지판, 핸드폰, 사람, 무형의 형태도 객체가 된다.  

프로그래밍 세계에서의 객체는 실물 세계에서의 객체와 비슷하지만 **다르다**. 

프로그래밍 세계에서의 객체는 **목적을 달성**하기 위해 스스로 **자신의 상태**를 살피고 **행동**하는 개체이다. 
여기서 목적은 애플리케이션의 동작하고자 목적일 수도 있고 비지니스 목적일 수도 있다. 

# 2. 클래스의 구성요소 - 캐릭터와 오브젝트

클래스는 객체를 만들기 위한 템플릿이다. 

앞서 객체는 목적을 달성하기 위해 스스로 자신의 상태를 살피고 행동하는 개체라고 정의했다.

![Desktop View](https://stardewvalleywiki.com/mediawiki/images/2/29/Character_creation_menu.png){: width="972" height="589" .w-50}_스타듀벨리 캐릭터 생성화면(출처: [스타듀벨리 위키](https://stardewvalleywiki.com/File:Character_creation_menu.png))_

객체가 **어떤 상태**를 가질 수 있는지 그리고 **어떤 행동**을 할 수 있는지 정의해 놓은 틀이다. 

## 2.1 변수와 메소드 - 특성, 행동
프로그래밍 세계에서는 객체가 **가질 수 있는 데이터/상태**를 변수라고 부르고, **어떤 행동/기능을 할 수 있는지** 정의해놓은 것을 메소드라고 부른다. 

예시를 통해 클래스를 이해해보자. 이 마을이 돌아가려면, 광산에서 채굴할 수 있는 석탄이 필요하고 석탄을 채굴 할 수 있는 광부 주민이 필요하다. 우리는 **광부 주민이 어떤 상태**를 가져야하는지, **어떤 행동**을 해야하는지 정의해놓아야 광부 역할의 주민들을 받을 수 있다.

#### 변수 
광부가 제대로 일을 할 수 있도록 광부에게 필요한 상태들을 정의해야한다. 이 상태들을 **변수**라고 한다. 
```java
class Miner {
	// 클래스 (정적) 변수: 모든 Miner 객체가 공유한다.  
	private static int totalMiners = 0;  
	private static String MinerPledge = "As a miner, I vow to dig deep, " +  
	        "endure the hardships of the earth, " +  
	        "and bring forth the treasures that will help our village thrive.";  
  
	// 인스턴스 변수: 광부를 부를만한 이름이 있어야한다.  
	private String name;  
	// 인스턴스 변수: 광부의 체력이 필요하다  
	private int health;  
	// 인스턴스 변수: 광부는 광산을 캘 수 있는 채굴 도구가 있어야 한다.  
	private MiningTool miningTool;  
	// 인스턴스 변수: 광부는 광산을 캘 수 있는 채굴 능력이 있어야 한다.  
	private MiningSkill miningSkill;
}
```

클래스에서 변수는 쓰임새에 따라 크게 2가지 종류로 구분할 수 있다.

1. 모든 객체가 공유하는 속성
2. 각각의 객체가 고유하게 가지는 속성과 

모든 객체가 공유하는 속성을 클래스(정적) 변수라고 하고 각각의 객체가 고유하게 가지는 속성을 인스턴스 변수라 한다.

#### 클래스 변수 
클래스 변수는 `static` 키워드를 사용해서 선언된다. 
클래스 변수는 프로그램이 실행되면서 클래스가 로드될때, 딱 한번 메모리에 할당되며, 해당 메모리의 주소를 모든 객체가 공유하는 특성의 변수이다. 

때문에 클래스 이름을 통해서 직접적으로 접근할 수 있다는 특징을 가지고 있다.
위에서 `static` 키워드가 붙은 totalMiners, MinerPledge 변수가 이에 해당된다.

#### 인스턴스 변수 
인스턴스 변수는 클래스 내부에 선언되고, 각 객체마다 고유한 값을 가질 수 있다. 
인스턴스 변수는 객체가 생성될 때마다 메모리에 할당되므로 객체마다 서로 다른 값을 가질 수 있다. 
`name`, ,`health`, `miningTool`, `miningSkill` 변수가 인스턴스 변수에 해당된다.


| 변수 종류     | 클래스 변수               | 인스턴스 변수             |
| --------- | -------------------- | ------------------- |
| 속성        | 모든 객체가 같은 값을 공유하는 변수 | 각 객체마다 고유한 값을 갖는 변수 |
| 메모리 할당 시점 | 객체 생성될때마다 할당         | 클래스 로딩시 한번 할당       |
| 사용방법      | `static` 키워드와 함께 선언  | 클래스 내부에 선언          |

#### 메소드
광부가 제대로 일을 할 수 있도록 광부가 할 수 있는 행동들을 정의해야한다. 이렇게 정의된 코드블록을 **메소드**라고 한다. 
```java

// 클래스(정적) 메서드 : 전체 광부들의 수를 반환하는 메서드(권장하지 않음)
public static int getTotalMiners() {  
    return totalMiners;  
}

// 인스턴스 메서드 : 광산에서 자원을 채굴하는 메서드  
public Mineral mining(Mine mine) {  
    // 도구 사용  
    useTool();  
  
    // 광산에서 자원 추출  
    Mineral extractedMineral = mine.extractMineral();  
  
    if (extractedMineral != null) {  
        return extractedMineral;  
    } else {  
        System.out.println("not enough mineral to extract.");  
        return null;  
    }  
}  
// 인스턴스 메서드 : 도구를 사용하는 메서드
private void useTool() {  
    if (miningTool.isBroken()) {  
        System.out.println("mining tool is broken!");  
        return;  
    }  
  
    miningTool.decreaseDurability();  
    System.out.println("mining tool decreases durability!");  
}
```

메소드도 변수와 마찬가지로 크게 2가지 종류로 나누니다. 
#### 클래스 메서드
클래스(정적) 메서드는 `static` 키워드를 사용해서 선언된다. 
클래스 메서드는 인스턴스의 변수에 접근할 수 없고 **오직 클래스 변수**에만 접근할 수 있다는 특징이 있다.
클래스 메서드 역시 클래스 이름을 통해서 직접적으로 접근할 수 있다는 특징을 가지고 있다.

#### 인스턴스 메서드 
인스턴스 변수는 클래스 내부에 선언되고, 각 객체마다 다른 동작을 가질 수도 있다.
이 메서드는 객체가 생성된 후, 생성된 객체를 통해서 호출되는 메서드를 의미한다.

| 변수 종류 | 클래스 변수                              | 인스턴스 변수                        |
| ----- | ----------------------------------- | ------------------------------ |
| 속성    | 인스턴스를 생성하지 않고도 호출 가능, 주로 공통된 동작을 수행 | 인스턴스를 통해 호출되며, 각 객체마다 다른 동작 가능 |
| 사용방법  | `static` 키워드와 함께 선언                 | 클래스 내부에 선언                     |


### 2.2 생성자 - 캐릭터 만들기
객체가 생성될 때, 객체의 초기화하는 역할을 담당하는 메소드다.

이제 주민을 받아볼 차례다. 광부 주민을 마을로 불러오기 위해서는 광부 주민을 창조해야한다.
아쉽게도 광부 클래스만으로는 광부 주민이 스스로 짜잔! 하고는 생성되지 않는다. 

캐릭터가 마을에서 제대로 활동할 수 있도록 준비해주는 과정이 있어야한다. 광부가 적절한 체력과 도구, 채굴에 적합한 능력을 가지고 기능할 수 있도록 초기 광부의 상태를 지정해주어야 한다.

광부 주민을 위한 온보딩 과정을 생성자에서 담당한다고 생각하자! 

두 가지 생성자는 매개 변수가 있느냐 없느냐 따라 두 가지 생성자로 나뉜다.
두 생성자가 반환하는 객체의 타입은 해당 클래스로 차이가 없다. 

#### 기본생성자
만일 객체를 생성하는데, 객체가 가지는 상태들을 주입할 필요가 없을 때 필요하다. 
이때, 상태에 초기화되는 값들은 타입에 따라 기본값이나 null 값으로 채워진다.
- 사용자가 임의로 지정해줄 수 있음.

```java
class Miner {
	public Miner(){ // 기본 생성자
	    // 여기서 상태를 초기화해줄 수 있음.
	} 
}
```

#### 매개변수가 있는 생성자
만일 객체를 생성하는데, 객체가 가지는 상태들을 외부에서 주입해주고 싶을 때, 혹은 객체의 필수 속성 값을 주입받아야할 때 사용할 수 있다.

```java
class Miner {
	private String name;
	private int health;  
	private MiningTool miningTool;  
	private MiningSkill miningSkill;

	public Miner(String name, int health, MiningTool miningTool, MiningSkill miningSkill) {  
	    this.name = name;  
	    this.health = health;  
	    this.miningTool = miningTool;  
	    this.miningSkill = miningSkill;  
	}
}
```



❓ 그렇다면 "smith"라는 이름의 광부를 만들고 싶다면 어떤 생성자를 사용해서 만드는게 적절할까?


## 3. 중첩 클래스 
클래스 안에 또 다른 클래스를 만들 수 있다. 이를 중첩 클래스라고 부른다. 틀 안에 또 틀이 있는 셈이다. 
두 클래스를 구분하기 위해서, 내부에 있는 클래스를 '중첩 클래스', 중첩 클래스를 포함하고 있는 형태의 클래스를 '외부 클래스'라고 부르겠다. 

주로 중첩 클래스가 외부 클래스가 표현하는 구성요소일 경우 중첩하여 사용해 표현한다. 두 클래스 간의 관계가 밀접할 경우, 클래스 내부에 위치시켜 더 직관적으로 코드를 작성할 수 있다. 중첩 클래스는 외부 클래스와의 관계를 명확하게 보여줄 수 있으며, 외부 클래스의 자원에 쉽게 접근할 수 있다는 장점이 있다.

때문에 중접클래스의 특징을 다음으로 정리해볼 수 있다.

- **외부 클래스 자원**에 **접근**할 수 있다. 
	- 중첩 클래스는 외부 클래스의 인스턴스 변수나 메서드에 직접 접근할 수 있다.
- 외부 클래스의 **생명주기에 종속되진 않**는다.
	- 중첩 클래스는 독립적으로 생성될 수 있으며, 외부 클래스가 없어도 존재할 수 있다.

또한, **정적 중첩 클래스**는 `static` 키워드를 사용하여 선언되며, 외부 클래스의 인스턴스 없이도 사용될 수 있다. 정적 중첩 클래스는 외부 클래스의 인스턴스 변수에는 접근할 수 없고, 오직 정적 변수나 메서드만 사용할 수 있다.

**광부 캐릭터**를 예로 들어보자. 광부는 다양한 도구를 사용하여 광산에서 자원을 채굴한다. 이때, 광부와 그가 사용하는 **도구**는 밀접한 관계를 가진다. 따라서 도구는 광부 객체 안에 포함될 수 있으며, 이 도구를 **중첩 클래스**로 정의할 수 있다.

```java
public class Miner {
	...
	private MiningTool miningTool;  

	// 기타 메서드
	...

	// 중첩된 도구 클래스  
	public class MiningTool {  
		private String toolName;  
		private int durability;  
  
		public MiningTool(String toolName, int durability) {  
			this.toolName = toolName;  
			this.durability = durability;  
		}  
		  
		public void useTool() {  
			if (durability > 0) {  
				// 외부 클래스의 변수에 접근 가능.
				System.out.println(name + "이(가) " + toolName + "을(를) 사용합니다.");  
				durability--;  
			} else {  
				System.out.println(toolName + "이(가) 고장났습니다.");  
			}  
		}
	}  
}
```

## 4. 객체를 만드는 다양한 방법들 

지금까지 객체의 정의와 객체를 이루는 구성요소들을 살펴보았다.
마을을 운영하는 게임의 캐릭터를 구성해보는 과정에 비유해 객체에 대해서 알아보았다. 

이후에는 이 내용들을 활용해서 객체를, 캐릭터를 만들어낼 수 있는 여러 방법들에 대해서 알아본다. 

### 4.1 생성자(기본, 매개변수) 이용

제일 기본적인 방법으로 new 키워드를 사용해 객체를 생성할 수 있다. 
클래스 내부에 별도의 생성자가 존재하지 않는다면, 컴파일 단계에서 기본생성자가 자동으로 추가된다. 

별도의 반환 타입이 없으며, 생성자의 이름은 클래스의 이름과 동일하다. 

```java
public class Miner {
	private String name;
	private int health;


	public Miner(){}; // 기본 생성자
	public Miner(String name, int health) { // 매개변수를 갖는 생성자
		this.name = name;
		this.health = health;
	}
	
	public static void main(String[] args) {
		Miner miner = new Miner()
	}
}
```

매개변수를 가지는 생성자를 통해서도 객체를 생성할 수 있다. 다만 이 경우에는 컴파일 단계에서 기본 생성자가 자동으로 추가되지는 않는다. 


> [!tip] 시그니처(Signature)란? 
> 여기서 말하는 시그니처란 메서드의 고유 생김새를 의미한다. 
> 시그니처는 **메서드명** , 메서드의 **매개 변수**로 구성된다.

### 4.2 생성자 + 수정자

생성자를 통해서 객체를 생성한 후에 필요한 상태를 수정자(setter)를 통해서 지정해주는 방법도 있다. 
이러한 객체 생성 방식을 [자바빈즈](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EB%B9%88%EC%A6%88) 패턴이라고 한다. 

```java
public class Miner {  
    private String name;  
    private int health;
    private MiningTool miningTool;
    private boolean isExpert;  
  
    public Miner() {  
    }  
  
    public void setName(String name) {  
        this.name = name;  
    }  
	
	public void setHealth(int health) {  
	    this.health = health;  
	}
  
    public void setMiningTool(String miningTool) {  
	    this.miningTool = miningTool;  
	}
  
    public void setExpert(boolean expert) {  
        isExpert = expert;  
    }  
  
    public static void main(String[] args) {  
        Miner miner = new Miner();  
        
        miner.setName("smith");  
        miner.setHealth(100);
        miner.setMiningTool(new MiningTool("hammer", 30));  
        miner.setExpert(true);  
    }
}
```

비교적 간단하고, 내가 원하는 값을 골라서 상태를 바꿔줄 수 있는 장점이 있지만,
이 방식의 경우 객체를 만들기 위해서 메서드를 여러개 호출해야하는 단점도 존재한다. 

메서드를 여러개 호출해야한다는 점 말고도 다른 특징이 존재하는데, 수정자를 통해서 객체내의 상태를 외부에서 변경할 수 있으므로, 이러한 패턴을 사용한 클래스는 불변상태로 만들 수 없다는 특징이 있다.  

불변 상태란 생성된 후에 상태를 변경할 수 없는 것을 말한다.
(불변상태에 자세한 내용은 별도의 글을 통해서 설명해둘 예정. )


### 4.3 정적 팩토리 메서드 생성
객체를 생성하는 일이 자주 일어나고, 유연한 방식으로 객체를 생성해야하는 경우가 많다면 "정적 팩토리 메서드"라는 것을 사용해서 객체를 생성해보는 것을 고려해볼 수 있다.

만일 같은 매개변수로 다른 객체를 생성하고 싶다거나, 구분하고 싶은 생성자에 의미를 부여하고 싶다면 어떻게 할까?

왜 그런짓을? 이라고생각할 수 있는데 , 생성자의 경우 여러 개의 생성자를 만들 수는 있지만 이들을 구분할 수 있는 것은 매개변수의 타입과 순서뿐이다. (물론 IDE를 통해서 확인이 가능하지만) 사용자가 실수할 가능성이 생긴다. 

```java
public static void main(String[] args) {
	Miner miner = new Miner() // ??? 어떤 매개변수를, 어떤 순서로 넣어야할지 파악하기 쉽지 않음.
}
```

자바에서는 정적 메서드를 통해서 객체를 반환할 수 있다. 생성자와 동일한 역할을 하는 정적 메서드를 "정적 팩토리 메서드"라고 부른다. 이는 자바 표준 명명은 아니고 관례적인 표현이다. 코드의 패턴은 다음과 같다.

```java
class Miner {
	private String name;
	...
	private MiningSkill miningSkill;

	private Miner(String name, MiningSkill MiningSkill) {
		this.name = name;
		this.MiningSkill = MiningSkill;
	}

	// 초급 광부를 생성하는 정적 팩토리 메서드
	public static Miner createBeginnerMiner(String name) {  
	    return new Miner(name, MiningSkill.BEGINNER);  
	}
	
	// 중급 광부를 생성하는 정적 팩토리 메서드
	public static Miner createIntermidiateMiner(String name) {  
	    return new Miner(name, MiningSkill.BEGINNER);  
	}

	// 전문가 광부를 생성하는 정적 팩토리 메서드
	public static Miner createAdvancedMiner(String name) {  
	    return new Miner(name, MiningSkill.BEGINNER);  
	}

}
```


```java
public static void main(String[] args) {
	Miner smith = Miner.createAdvancedMiner("smith");
	Miner jonney = Miner.createIntermidiateMiner("jonney");
}
```

- 이 방식의 장점은 **반환될 타입의 특성**을 메소드 이름을 통해서 예측하기 쉽다는 장점이 있다. 
- 시그니처가 같지만 다른 의미의 여러 생성자를 만들 수 있는 장점이 있다.
	- 생성자는 반드시 객체의 이름과 동일해야하기 때문에, 같은 시그니처로 오버로딩이 불가능하다. 

❓ `createIntermidiateMiner` 는 어떤 `Miner` 를 반환할 것으로 예상되는지?

> 뿐만 아니라 정적 팩토리 메서드는 위의 장점 뿐만 아니라 런타임시 반환될 객체의 타입이나 인터페이스를 통해서 유연한 생성 전략을 채택할 수 있게 도와주는 장점을 가진다. 이러한 내용은 객체의 속성, 인터페이스와 같은 개념을 설명할 때 다루려고 한다.

## 마무리하며

본 글에서는 클래스와 객체의 관계, 객체에 대해서 알아보았다.

객체는 **목적을 달성**하기 위해 스스로 **자신의 상태**를 살피고 **행동**하는 개체이며, 
객체의 상태와 행동을 정의해 놓은 틀을 클래스라고 불림을 알 수 있었다. 

클래스는 객체가 가져야할 상태과, 행동을 변수와 메서드로 정의할 수 있었고, 
객체를 만들기 위해 이를 초기화하는 생성자에 대해서도 알아봤다. 

이를 바탕으로 다양한 객체의 생성방식을 알아봤다.
제일 기본이 되는 생성자를 직접 이용해서 생성하는 방법, 수정자를 이용해서 객체를 완성하는 방법 
정적 생성자(팩토리 메서드)를 이용해서 만드는 방법들을 알아보았다.

다음에는 객체들 간의 관계에서 나오는 개념들을 살펴보고 관련된 개념을 사용하는 예시를 살펴보려한다.
