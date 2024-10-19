---
title: "자바 멀티쓰레드 All in One 9문답:(Part 1)"
author: codongmin
date: 2024-02-01 22:16:00 +0800
categories: [Java, 멀티스레딩]
tags: [Java, MultiThreading, OS, thread]

image:
  path: /assets/logo/geulttoUdemy.png
  lqip:
  alt: Responsive rendering of Chirpy theme on multiple devices.
---

## 들어가며

저는 멀티스레드라는 단어를 일종의 뷰어 프로그램을 개발해보면서 처음 접했습니다. 물론 이론적으로 스레드라는 단어를 들어본 적은 있었지만 이를 이용하여 개발을 해본 경험은 없었거든요. 제가 처음으로 멀티스레드를 고민해봤던 경험은 다음과 같았습니다.

실시간으로 입력되는 센서의 데이터를 보여주는 뷰어 프로그램을 만드는 요구사항이 있었습니다. 분석해보니 프로그램에 필요한 주요한 기능은 센서와 통신하는 부분, 각 센서들의 health check 기능, 데이터를 받아서 처리하는 기능, 화면 뷰에 띄워주는 기능들이 요구 되었습니다. 처음에 이를 모두 직렬적으로 짜다보니 센서의 통신이 문제가 생기면 프로그램이 모두 멈춰버리거나 연산이 많이 필요한 부분에서 병목이 걸려 응답성이 매우 불편해져버리는 단점이 있었습니다. 그래서 당시에 이를 병렬적으로 처리하고 싶은 마음에 겁도없이(?) 통신과, 처리, 뷰를 각각 따로 처리하는 로직을 이것저것 뒤져가며 구현했던것 같습니다.

![Desktop View](/assets/meme/dontTouchIt.png){: width="972" height="589" .w-50}_물론 이러면 안되겠죠..?_

당시에는 급한 마음도 있었고, 일단 구현이 먼저였던 터라 멀티스레드에 대해서 깊게 공부하고 적용해볼 시간이 적었었는데, 감사하게도 글또 과정에서 유데미와 협업하는 챌린지를 열어주셨습니다. 덕분에 유데미에 제공하는 강의 수강 기회를 받을 수 있었고 저는 Java 멀티스레딩 관련된 강의를 선택했습니다. 이런 기회를 만들어주신 글또 운영진, 유데미에게 너무 감사하다는 인사를 드리고 싶습니다.

강의 수강하면서 강의 내용과 개인적으로 공부한 내용들을 꾸려 이번 글을 작성해보았습니다. 글은 크게 2파트로 나누었고 이번 파트에서는 멀티스레드의 전반적인 개요와 특성에 대해 알아보고, 다음 파트에서는 조금 더 심화된 내용을 작성하려고 합니다.

글의 구조는 멀티스레드와 관련된 주제에 대해 문답형으로 구성하여 진행하는 방식으로 멀티스레드에 관련된 주제의 00가지의 질문을 던지고 답하며 이해해보려 합니다. 질문을 보고 한번 여러분들만의 답을 생각해보면서 글을 읽어도 좋을 것 같습니다. 준비한 질문들은 다음과 같습니다.

- 1. 스레드는 뭐고 왜 필요한거죠?
  - Q. 스레드는 무엇인가?
  - Q. 왜 멀티스레드가 필요할까?
- 2.스레드 사용하려면 어떻게 해야하죠? (Java)
  - Q. 스레드는 어떻게 생성하나요?
  - Q. 스레드를 언제 종료해야 할까?
- 3.멀티스레드는 위험한가요? 왜죠?
  - Q. 스레드 간 데이터 공유가 가능한가요?
  - Q. 공유자원에 여러 스레드가 접근하게 되면 어떻게 되나요?
  - Q. 임계 영역(Critical Section)은 무엇인가요?
- 4.어떻게 해결하죠?
  - Q. 임계영역을 보호하기 위한 방법은 무엇인가요?
  - Q. 어떤 연산이 원자적(Atomic)한지 판단할 수 있을까요?

---

## 1. 스레드는 뭐고 왜 필요한거죠?

### Q. 스레드는 무엇인가?

> A. 프로그램에서 작업을 실행하는 실행 주체이자 흐름, 작업 단위

위에서 제가 경험했던 프로그램의 예시를 들어서 설명해보려고 합니다.  
뷰어 프로그램은 다음와 같은 순서를 가지고 실행이 됩니다.

![Desktop View](/assets/posts/singleThread.png){: width="972" height="589"}_싱글스레드_

저는 개인적으로 "**흐름**"이라는 표현으로 이해하는 것이 더 편했습니다. 위의 표시된 단계들은 **"데이터를 눈으로 확인하기"**위한 프로그램의 목표를 수행하기 위해 필요한 일련의 **작업**들입니다. 위의 예시는 이 프로그램의 목표를 달성하기 위한 (1, 2, 3)단계가 프로그램 관점에서는 하나의 큰 작업 단위가 되는 것이지요. 프로그램은 이를 반복 수행합니다. 시작지점부터 끝지점까지의 실행 흐름이라고 볼 수 있습니다.

그리고 이러한 작업단위를 맡아 **수행하는 주체**가 바로 **스레드**가 됩니다. 스레드가 없다는 것은 이러한 작업들을 수행할 주체가 없다는 것을 의미하기 때문에 프로그램이 실행되기 위해서 반드시 하나 이상의 스레드가 있어야 합니다.

그리고 프로그램이 메모리에 올라가 프로세스에서 가장 먼저 작업을 실행하는 스레드를 "메인스레드" 라고 부릅니다. 이렇게 하나의 스레드만을 가지고 한번에 한작업을 수행하는 방식을 <u>싱글스레드(Single Thread)</u>라고 합니다. 굉장히 직관적인 이름입니다.

![Desktop View](/assets/posts/multiThread.png){: width="972" height="589"}_멀티스레드_

멀티스레드는 더 간단합니다. 프로세스에 스레드가 여러개 있으면 <u>멀티스레드</u>입니다. 프로그램의 작업 흐름이 하나에서 두개가 되거나 이보다 더 많아지는 것입니다.

### Q. 왜 멀티스레드가 필요할까?

> A. 크게 2가지 이유로 나누어 볼 수 있습니다. 바로 **응답성(Responsiveness)**과 **성능(Performance)**면에서 이점을 얻기 위해서 입니다.

#### ⚡️ 응답성

응답성(Responsiveness) 는 무엇을 의미할까요? 사용자의 **요청에 얼마나 빨리 반응하고 응답**하는지를 의미합니다.

일반적으로 좋은 응답성은 사용자가 애플리케이션을 효과적으로 사용할 수 있도록 도와주게 됩니다. 그렇다면 나쁜 응답성을 보여주는 예시는 어떤 것들이 있을까요?

다음과 같은 경험들이 떠오를 수 있습니다.

![Desktop View](/assets/posts/badResponsiveness.png){: width="972" height="589"}_좋지 않은 응답성의 예시_

- 고객 서비스를 받기 위해 하루 반나절 기다리는 일
- 메시지를 답장받는데 걸리는 시간이 늦어질때
- 애플리케이션에서 피드백이 없을때

이러한 좋지 않은 응답성은 프로덕트나, 애플리케이션의 고객들에게 불편한 경험을 심어줄수 있습니다.

![Desktop View](/assets/posts/responsivenessResearch.png){: width="972" height="589"}_Google SOASTA Research 2017_

실제로 이와 관련한 [구글 리서치 통계](https://www.thinkwithgoogle.com/marketing-strategies/app-and-mobile/page-load-time-statistics/)가 있습니다. 페이지의 로딩시간이 1초에서 <u>3초</u>로 길어질 경우 <u>이탈률이 32% 증가</u>한다는 통계입니다. 고객 이탈이 늘어날 경우 해당 애플리케이션의 수익 발생 가능성이 줄어들고 손해로 이어질 가능성이 높기 때문에 좋지 않은 응답성은 비지니스에 치명적입니다.

주로 사용자 UI가 있는 어플리케이션의 경우 응답성과 관련된 사용자 경험이 더욱 치명적으로 다가올 수 있습니다. 이러한 응답성은 각각의 작업을 별도로 분리한 멀티스레드를 적절하게 사용함으로써 더 나은 사용자 경험을 제공해줄 수 있습니다.

> 물론 항상 그렇다는 것을 보장할 수는 없습니다
> {: .prompt-info }

#### 🧭 성능

여러 개의 스레드로 빠른 멀티태스킹을 구현하면 마치 여러 개의 작업이 동시에 실행되는 것과 같은 효과로 보여집니다. 이러한 멀티태스킹을 동시성이라고 합니다. 다음 그림처럼 매우 빠르게(사람이 인지하지 못하는 수준) 조금씩 번갈아가며 작업을 처리하는 것을 의미합니다.

![Desktop View](/assets/posts/concurrencyParallelism.png){: width="972" height="589" .w-50}_동시성과 병렬성 차이 예시_

여기서 설명하는 동시성은 병렬성과는 다릅니다. 병렬성을 설명하려는 것은 아니지만, 만일 아래 그림처럼 멀티코어로 여러개의 작업 처리한다면 완전하게 동시에 실행할 수 있을겁니다.

> 동시성은 "실행되어지는 것처럼" 보이는 것이지 물리적으로 동시에 실행된다는 의미는 아닙니다.
> {: .prompt-warning }

이러한 동시성을 활용하여 멀티스레딩이 **성능에 미치는 영향**을 다음과 같이 정리해볼 수 있습니다.

![Desktop View](/assets/posts/multiThreadConcurrency.png){: width="972" height="589"}_동시 처리_

1. **여러 작업을 "동시에" 처리할 수 있습니다.**

   - 위 그림은 싱글코어에서 싱글스레드와 멀티스레드의 작업 방식을 비교하여 표현한 그림입니다. <u>timeline을 기준</u>으로 위쪽이 싱글스레드, 아래쪽이 멀티스레드를 표현한 그림입니다.
   - <u>싱글스레드</u>의 경우 Task1과 Task2를 순차적으로 실행하기 때문에 <u>특정 시점(빨간색 선)</u>에서의 작업 완료도를 보았을때, <u>Task2는 전혀 진행되지 않은 것</u>을 볼 수 있습니다.
   - <u>멀티스레드</u>의 경우 두 개의 스레드로 Task1과 Task2를 동시적으로 처리하기 때문에 <u>특정 시점(빨간색 선)</u>에서의 작업 완료도를 보았을때, <u>Task1과 Task2가 적절하게 진행</u>된 것을 볼 수 있습니다.

2. 싱글스레드를 사용하는 것보다 **짧은 시간안에 복잡한 작업을 완료할 수 있음.**

   - 싱글 코어인 경우에는 이를 보장하지 않습니다. 오히려 오래 걸릴 수도 있습니다.

3. 같은 시간 안에 더 많은 작업을 수행할 수 있다는 뜻은 **경제적으로도 효과**적입니다.
   - 대규모 스케일의 서비스의 경우
     - 더 적은 서버로 더 많은 작업을 처리할 수 있습니다.
     - 하드웨어 비용 절감 효과까지 생각해볼 수 있습니다.

#### ❓ 싱글스레드 vs 멀티스레드

멀티스레드 프로그래밍은 근본적으로 순차적으로 사용하는 싱글 스레드 프로그래밍과 다릅니다.

멀티스레드의 경우 순차적으로 실행하는 싱글스레드의 방식보다 직관적으로 이해하기가 어려운 경우가 많습니다. 일반적으로 OS의 스케쥴링 정책에 따라 실행되기 때문에 스레드의 순서를 보장할 수 없습니다. 때문에 디버깅 또한 쉽지 않은것이 사실입니다.

그렇기 때문에 <u>무조건적으로 멀티스레드가 좋은 것은 아닙니다</u>.

**응답성과 성능**을 얻는 대신에 싱글 스레드를 사용한 프로그램 **개발의 직관성**과 교환하는 셈입니다.

---

## 2. 스레드 사용하려면 어떻게 해야하죠? (Java)

### Q. 스레드는 어떻게 생성하나요?

> A. 자바 Thread 객체와 Runnable 인터페이스의 run 메서드를 오버라이드하여 생성합니다.

Java에서는 JDK가 모든 스레드와 관련된 속성 및 메서드를 Thread class(`java.lang.Thread`)에 집약시켜두었습니다.

Thread class를 보게되면 Runnable 인터페이스를 구현한 객체라는 것을 알 수 있습니다. 그리고 스레드 객체의 생성자에 Runnable 인터페이스 구현체를 전달함으로써 Thread 객체를 생성하는 것을 알 수 있습니다.

```java
public class Thread implements Runnable {
  ...
  public Thread(Runnable target) {
      this(null, target, "Thread-" + nextThreadNum(), 0);
  }
  ...
}
```

Runnable 인터페이스가 무엇인지 확인해보겠습니다.

> The Runnable interface should be implemented by any class whose instances are intended to be executed by a thread. The class must define a method of no arguments called run.
>
> Runnable 인터페이스는 해당 인스턴스가 스레드에 의해 실행될 예정인 모든 클래스에서 구현되어야 합니다. 이 클래스는 run이라는 매개변수 없는 메서드를 정의해야 합니다. 이 메서드 안에는 스레드가 실행할 코드가 들어가게 됩니다.

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

Runnable 인터페이스는 추상메서드로 run() 메서드를 가지고 있습니다. 이는 스레드가 작업하게될 일종의 "**작업정의서 양식**"이 됩니다. 그리고 Runnable를 상속받은 구현 클래스의 run 메서드에서 실행되는 코드는 스레드가 실제로 작업하게 되는 "**작업정의서**"가 되게 됩니다.

![Desktop View](/assets/posts/threadRunnable.png){: width="972" height="589"}_thread와 runnable_

정말로 그런지 Thread 클래스의 run 메서드를 보겠습니다.

```java
public class Thread implements Runnable {
  ...
  @Override
      public void run() {
          if (target != null) {
              target.run();
          }
      }
```

Target 이 null인지 확인하고 아니라면 target의 run 메서드를 실행합니다. 여기서 target은 쓰레드 생성시 넘겨주는 Runnable 구현객체를 의미합니다. 이를 통해 초기의 Thread클래스의 run 메서드는 빈 작업정의서이며, Thread를 생성할때 넘겨주는 Runnable 구현객체의 run메서드로 오버라이드되며 우리가 작성한 작업정의서를 스레드가 실행하는 것을 알 수 있습니다.

따라서 다음과 같이 Runnable 구현객체를 넘겨 스레드를 생성할 수 있습니다. Thread 생성자에 익명클래스로 Runnable 객체를 넘겨줍니다. JAVA 8 이상인 경우람다를 사용해서 더 간결하게 표현이 가능합니다.

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                // TODO Code that will run in a new thread.
            }
        });

	      // JAVA 8 이상, 람다 사용
        Thread thread = new Thread(() -> {
              // TODO Code that will run in a new thread.
        });
    }
}
```

Thread 객체를 상속받는 일종의 wrapper 클래스를 사용하면 조금 더 깔끔하게 작성할 수 있습니다.

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {

      	Thread thread = new WrapperThread();
      	thread.start();
    }

  	public static class WrapperThread extends Thread {
      	@Override
        public void run() {
            // TODO Code that will run in a new thread.
        }
    }
}
```

스레드는 `start()` 메서드로 실행할 수 있습니다.

### Q. 스레드를 언제 종료해야할까?

> A. 작업이 완료된 스레드 중 더 이상 사용하지 않을 경우, 스레드가 오작동하는 경우 스레드를 종료해야 한다.

스레드도 자원을 소모합니다. 여기서 말하는 자원은 다음을 의미합니다.

- 메모리와 커널리소스
- Cpu 사이클과 캐시 메모리 등

만약에 스레드가 맡은 일이 모두 끝났음에도 스레드가 아무일도 하지 않고 남아있다면 이는 자원을 계속 불필요하게 점유하고 있는 것이기 때문에 종료해야 합니다.

스레드가 예상한 것보다 오래 연산을 함으로써 오작동하게 된다면 이를 종료해야할 필요가 있습니다. 또한 최소 하나의 스레드가 계속 실행되고 있으면 (메인 스레드가 종료되었음에도)애플리케이션이 종료하지 않게 됩니다.

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new NewThread());

        thread.start();
        thread.interrupt();
    }

    public static class NewThread implements Runnable {
        @Override
        public void run() {
            try {
                for (int i = 0; i < 1000; i++) {
                    Thread.sleep(100);
                    System.out.println(i);
                }
            } catch (InterruptedException e) {
                System.out.println("Interrupted NewThread.");
            }
        }
    }
}
```

자바에서는 `interrupt()` 메서드를 사용해 종료할 수 있습니다.

---

## 3. 멀티스레드는 위험한가요? 왜죠?

![Desktop View](/assets/meme/multithreadingPuppies.png){: width="972" height="589" .w-50}_멀티스레딩의 기대와 현실_

멀티스레드를 사용하면서 문제가되는 케이스는 어떨 때 일까요? 바로 다음과 같습니다.

- 스레드들이 동시에 공유가능한 자원에 접근하여 읽기/쓰기 등의 행위를 할때,
- 스레드의 작업이 원자적이지 않을때,

이런 경우가 어떻게 발생하는지, 어떤 문제를 일으키는지 살펴보겠습니다.

### Q. 스레드 간 데이터 공유가 가능한가요?

> A. 가능합니다. 그래서 문제가 발생합니다.

프로세스의 구조를 살펴보겠습니다. 프로세스에서의 메모리영역은 크게 2가지로 나뉘집니다. 하나는 스택, 하나는 힙입니다.

![Desktop View](/assets/posts/processStruct.png){: width="972" height="589" }

#### 스택(Stack)

반드시 하나의 프로세스당 하나의 메인 스레드를 보유합니다.

- 스레드에는 스택이라는 자료구조 영역이 있는데
  - 지역 변수가 저장되고, 함수로 전달되는 메모리 영역
- 스레드에는 또 명령어 포인터가 존재합니다.
  - 다음으로 실행할 명령의 주소가 담겨있는 포인터를 말합니다.

각각의 스레드마다 스택과, 명령어 포인터를 가지고 있는 이유는, 각각의 스레드는 특정 순간에 서로 다름 함수를 이용해 다른 명령을 수행하기 때문입니다. 이말은 서로 스레드 간의 스택영역에서는 간섭이 일어날 수 없다는 의미와도 같습니다.

서로 다른 스레드의 스택영역에서는 서로 접근할 수 없는 것이죠.

#### 힙(Heap)

반면에 힙은 공유 메모리 영역입니다. 모든 스레드가 힙에 있는 **데이터를 공유**하고 해당 영역에 객체를 할당하거나 접근할 수 있습니다. 여러 스레드가 동시에 접근할 수 있는 자원이나 변수를 이를 **공유자원(Shared Resource)**이라고 합니다.

이러한 공유자원에 두 스레드가 접근하게 되면 문제가 발생하게 됩니다.

### Q. 공유자원에 여러 스레드가 접근하게 되면 어떻게 되나요?

> 둘 이상의 스레드가 공유자원을 병행적으로 읽거나 쓰는 동작을 할때 접근 순서(스레드 스케쥴링)나 시점에 따라 실행 결과가 달라지게 됩니다. 이를 **경쟁상태(race condition)**라고 합니다.

![Desktop View](/assets/posts/raceCondition.png){: width="972" height="589" }

- 경쟁상태는 공유 자원에 비원자적 연산이 동시에 실행될경우 발생할 수 있습니다.
- 위의 그림에서 공유자원인 Money에 증가연산을 하는 스레드와 감소연산을 하는 경우가 있습니다.
- 각각의 연산들은 공유자원인 Money 에 각각 1000을 더하고 빼는 연산을 합니다.
- 하지만 스레드의 연산의 순서가 서로 달라 각각의 메서드가 원하는 실행결과를 얻지 못하는 것을 알 수 있습니다.

이를 통해 문제가 발생하는 2가지 상황을 알 수 있습니다.

1. 연산이 원자적이지 않습니다. 하나의 연산(더하기)가 여러 연산으로 분리될 수 있습니다.
2. 공유자원에 한 개 이상의 스레드가 접근할 수 있습니다.

때문에 의도하지 않은 결과를 방지하기 위해 위의 상황들을 막기 위한 방법들이 필요합니다.

### Q. 임계 영역(Critical Section)은 무엇인가요?

> A. 스레드 하나만 실행될 수 있도록 / 들어갈 수 있게 보장하는 코드 구역입니다.
> 일종의 공유자원에 대한 독점권을 보장해주는 구역을 말합니다.

- 임계 영역이란 다음과 같습니다.
  - 스레드를 하나만 들어갈 수 있게 지정하는 구역입니다.
  - 다른 스레드가 진입하려고하면 엑세스를 거부당하고, 첫번째스레드가 종료되어 나갈때까지 중단됩니다.
- 임계 영역을 설정함으로써 개별 작업의 원자성을 보장할 수 있습니다.

JVM의 경우 여러개의 스레드로 인한 동시 실행 엑세스로부터 임계영역을 지켜주는 다양한 도구들을 가지고 있습니다.
보통 이런 개념과 원리는 동일해서 다른 언어에서도 API와 언어적 특성만 변경될 뿐 거의 대부분 동일합니다.

## 4. 어떻게 해결하죠?

### Q. 임계영역을 보호하기 위한 방법은 무엇인가요? (Java)

> 자바에는 이러한 임계구역을 보호하기 위한 2가지 방법을 지원합니다.

- Synchronized 키워드 - Monitor
- Synchronized 키워드 - Lock

#### 1. Synchronized 키워드 - Monitor

- 여러 개의 스레드가 코드 블럭이나 전체 메서드에 엑세스할 수 없도록 설계된 락킹 메커니즘을 사용합니다.
- `synchronized` 키워드를 사용합니다.

```java
public class ClassWithCriticalSections {
		public synchronized method1() {
				...
		}

		public synchronized method2() {
				...
		}
}
```

- 하나의 스레드가 해당 메서드를 점유하면, 다른 스레드는 method1, 2에 모두 접근할 수 없습니다.

- `synchronized`가 객체마다 각각 적용되어 있기 때문입니다. -> 이러한 개념을 `monitor` 라고 합니다.

> 모니터는 스레드가 다음을 가질 수 있도록 하는 동기화 메커니즘입니다.
>
> - _상호 배제 –_ _잠금을_ 사용하여 특정 시점에 하나의 스레드만 메서드를 실행할 수 있습니다.
> - _협력 -_ _wait-set을_ 사용하여 스레드가 특정 조건이 충족될 때까지 기다리게 하는 기능
>
> 이 기능을 "모니터"라고 부르는 이유는 무엇입니까? 스레드가 일부 리소스에 액세스하는 방법을 모니터링하기 때문 **입니다.** https://www.baeldung.com/cs/monitor

#### 2. Synchronized 키워드 - Lock

- 전체 메서드가 아닌 필요한 코드 영역에 대해서만 엑세스를 제한하는 것을 말합니다.
- 장점
  - 각각 별도의 임계영역을 설정하여 개별 쓰레드별로 접근하도록 설정할 수 있습니다.
  - 원하는 일부부분만 임계영역을 설정할 수 있습니다.

```java
public class ClassWithCriticalSections {
  Object lockingObject = new Object();

  public void method1() {
    synchronized(lockingObject) {
      '''
      critical section
      '''
    }
  }
}
```

- 이 방법은 락 역할을 할 동기화 객체가 필요하게 됩니다.

```java
public class ClassWithCriticalSections {
    Object lock1 = new Object();
    Object lock2 = new Object();

  public void method1() {
      synchronized(lock1) {
        '''
        critical section
        '''
      }

    public void method2() {
      synchronized(lock2) {
        '''
        critical section (	)
        '''
      }
  }
}
```

- 클래스 한개 객체 내부에 서로 다른 객체에 동기화된 각각 별도의 임계 영역이 생겼는데 이방식은 아주 유용하게 쓰이는 경우가 있습니다.

  - 예를 들어 ThreadB가 method2의 임계영역을 실행하는 동안 Thread A가 첫번째 임계영역에 엑세스할 수 있음.
  - 하지만 ThreadB method1의 임계영역에 접근하기 위해서는 Thread A가 끝날때까지 기다려야 해당 영역에 엑세스할 수 있음.

- 또 메서드 내부 일부에만 임계영역이 포함된 경우 전체 메서드를 동기화할 필요가 없음.

  - 일반적으로 임계영역 내의 코드는 로직을 실행하는 데 필요한 최소한만 남겨두고 최소화 하는게 좋다.
  - 더 많은 코드가 여러 스레드로 동시 실행 가능하고, 스레드간 동기화가 필요한 코드는 더 적어지는 것이 바람직, 성능 향상

- 동기화 블록이나 메서드는 Reentrant하다는 점이 중요합니다.

  - 재진입이 가능한 요소
  - 스레드는 자기 스스로 임계구역에 진입하는 것을 막을 수 없습니다.

  ```java
  public class ClassWithCriticalSections {
     public synchronized void method1() {
  	   method2(); // ThreadA
     }
     public synchronized void method2() {
   	    ... // ThreadA
     }
    }
  }
  ```

### Q. 어떤 연산이 원자적(Atomic)한지 판단할 수 있을까요?

> A. Java에서는 다음의 경우 원자적 연산을 보장합니다.
>
> - **<u>참조타입(reference)</u>**의 할당 (Read/Write)
> - **<u>원시타입(Long, double 제외)</u>**의 할당 (Read/Write)

연산이 원자적이라는 것은 한 번에 완전히 실행되거나 전혀 실행되지 않는 연산을 의미합니다. 이것이 왜 중요할까요?

멀티스레드에서 연산이 원자적이라는 것의 의미는 다른 스레드에 의해서 연산 혹은 데이터가 인터럽트 받을 일이 없다는 것 입니다. 때문에 원자적 연산에서는 동기화를 할 필요가 없습니다. 어느 연산이 원자적인 연산을 보장하는지/그렇지 않은지 판단할 수 있으면, 올바른 결과를 예상할 수 있으며, 효과적으로 멀티스레드의 이점 이용하여 최적화 할 수 있습니다.

- 의도하지 않은 연산 결과를 방지할 수 있습니다.
- (원자성을 보장해야만 하는)로직과 (그렇지 않은)로직을 분리할 수 있다면, 서로 병행실행(상호 영향 X)을 통해 성능을 높일 수 있습니다.

불행히도 대부분의 연산은 원자적이지 않은 경우가 훨씬 많습니다. 자바에서는 다음과 같은 경우에 원자적 연산을 보장합니다.

#### Atomic Operation

##### 1. 모든 참조 할당(get, set)은 원자적입니다.

- Object에 대한 참조의 Get, set은 원자적 연산을 보장하기 때문에 동기화할 필요가 없습니다.

  ```java
  Object a = new Object();
  Object b = new Object();

  a = b; // atomic
  ```

  ```java
  public int[] getAges() { // atomic!
  		return this.ages;
  }

  public void setName(String name) { // atomic!
  		this.name = name;
  }

  public void setPerson(Person person) { // atomic!
  		this.person = person;
  }
  ```

##### 2. 모든 원시타입의 할당, 읽기, 쓰기는 원자적입니다.

- 원시타입의 읽기, 쓰기는 원자적 연산을 보장합니다.

  - int
  - short
  - byte
  - float
  - chart
  - boolean

- **<u>But,</u>** *long, double(64bit)*은 자바가 보장해주지 않습니다.

  - 실제로는 cpu가 32bit씩 두개의 연산으로 나누어 완료할 가능성이 높습니다. (64bit 운영체제의 경우에도)

  - > ex) x = y;
    >
    > - x.lower_32_bits = y.lower_32_bits
    > - x.upper_32_bits = y.upper_32_bits

#### Volatile

**Volatile 키워드**를 사용하면 Long, Double 에 대해 스레드 세이프한 <u>원자적 연산</u>으로 만들어줄 수 있습니다.

```java
volatile double x = 2.0;
volatile double y = 9.0;
```

- 자바에서 데이터 유형에 대한 원자적 연산을 보장(Volatile키워드 등)을 도와줍니다.

- `java.util.concurrent.atomic` 클레스에서 더욱 향상된 연산들이 존재합니다. 원래라면 비원자적 연산이어야 할 연산들을 원자적으로 수행할 수 있도록 도와주죠. 이러한 클래스들의 로직 대부분이 이러한 내용을 기반들로 하여 구현되어 있습니다.

> 모든 공유가능한 변수(적어도 하나 이상의 스레드에 의해 변경될 수 있는)들은 synchronized 블록이나 어떤 타임의 lock으로 막거나, volatile 선언을 해주는 것이 좋습니다.
> {: .prompt-danger }

## 유스케이스 - 예) 성능지표

- 개발 단계에서 앱을 실행할 때 중요한 특정 연산이나 코드 일부의 실행시간이 얼마나 걸리는 지 측정해야하는 경우가 많은데 이러한 시간은 사용자 입력, 하드웨어 운영체제에 따라 많은 영향을 받는 작업입니다.

- 해당 연산들의 지속시간을 잘 캡쳐해서 **성능 문제**를 찾아내어 고객 경험을 **최적화** 할 수 있도록 하는 것이 중요합니다.

- 예를 들어 이럴때 멀티스레드를 적용할 수 있습니다.

  - 비지니스 로직과 유틸 로직을 분리함으로써 중요한 비지니스로직에 영향이 가지 않도록하고, 부가적인 유틸로직은 따로 측정하여 최적화 할 수 있기 때문입니다.

    ```java
    public class BusinessLogicClass {
      public void businessLogic() {
        long start = System.currentTimeMillis();
        // important operation
        long end = System.currentTimeMillis();

        long duration = end - start;
        captureSample(duration); // 별도 성능지표 측정 스레드
      }
    }
    ```

- 이를 파악하지 못하고 변수/메서드에 모두 동기화를 걸어버리면 싱글 스레드로 돌리는 것보다 못한 결과가 나올 수도 있습니다.

  - 모든 코드가 동기화된 상태이기 때문에 한번에 스레드 한개만 실행될 수 있음.
  - 사실상 싱글스레드를 사용하는 것보다 못한 결과가 나옴

- 컨텐스트 스위칭 비용이 들기 때문

이러한 이유 때문에 불가피한 경우를 제외하고 가능한 포인트들에서 동기화를 최소화하기 위해 노력해야 합니다.

## 정리

지금까지 크게 4가지의 큰 주제와 그에 따른 질문에 답을 해보았습니다. 이를 정리하면 다음과 같습니다.

- 1. 스레드는 뭐고 왜 필요한거죠?
  - Q. 스레드는 무엇인가?
  - Q. 왜 멀티스레드가 필요할까?
- 2.스레드 사용하려면 어떻게 해야하죠? (Java)
  - Q. 스레드는 어떻게 생성하나요?
  - Q. 스레드를 언제 종료해야 할까?
- 3.멀티스레드는 위험한가요? 왜죠?

  - Q. 스레드 간 데이터 공유가 가능한가요?
  - Q. 공유자원에 여러 스레드가 접근하게 되면 어떻게 되나요?
  - Q. 임계 영역(Critical Section)은 무엇인가요?

- 4.어떻게 해결하죠?
  - Q. 임계영역을 보호하기 위한 방법은 무엇인가요?
  - Q. 어떤 연산이 원자적(Atomic)한지 판단할 수 있을까요?

본 파트에서는 멀티스레드의 전반적인 개요와 멀티스레드의 필요성, 그로인해 발생하는 문제점들에 대해 알아보는 기회가 되었습니다. 사실 여기까지만 해도 굉장히 방대한 양의 내용이며 이를 위해 더 깊은 주제들이 더 뒷받침되어야 하는 부분도 있습니다. 최대한 이해할수 있도록 내용들을 첨부했지만 부족한 부분들이 많습니다. 시간이 될때 추가적으로 계속 보충할 예정입니다.

다음 파트에서는 스레드간의 통신과 여러 스레드 구조, 가상스레드와 고성능 IO를 위한 모델 등을 알아볼 예정입니다.
