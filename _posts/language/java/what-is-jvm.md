---
title: JVM 구성과 동작 원리
author: codongmin
date: 2024-10-23T21:14:00
categories:
  - Java
tags:
  - JVM
preview:
---

## 들어가며

JVM 구성 요소 설명 + 현업에서 고려해볼만한 점 추가


## JVM의 구성요소 

JVM 구성요소 이해

### Java compiler
    
### gc
    

### execute engine

- Runtime DA 에 배치된 바이트 코드를 실행하는 역할
- 바이트 코드를 machine language로 바꾸는 역할, 여기서 방식이 2가지로 나뉨
    - **Interpreter 방식**
    - **JIT 방식** : 인터프리터처럼 실행되지만, 자주 실행되는 섹션(바이트 코드)를 → 네이티브 코드(머신코드) 로 변환, 이를 캐시에 저장해 두고 사용.
        - 자주 실행되는 부분을 ‘hotspot’이라고 부름
        - 이 JIT 컴파일러 덕분에 full-compile과 비슷한 성능을 낼 수 있게 도와줌.
            - Tiered Compilation
                - C1 - Client Compiler
                    - JIT 컴파일러를 빠른 최초 시작을 위해 최적화하는 방법
                    - 주로 Start-Up Time(앱 초기 실행시간)이 중요한 애플리케이션에서 사용되어 왔음.ㄴ
                    - Java 8 이전에는 C1 Compiler를 사용하기 위해 `-client` 옵션을 사용했어야만 했음. (이후 버전부터 X)
                - C2 - Server Compiler
                    - 전반적인 성능 향상이 있는 JIT 컴파일러 최적화 방법
                    - C1 보다 코드를 분석하는데 더 많은 시간을 소요, 대신 컴파일 코드 최적화가 더 잘된다.
                    - Java 8 이전에는 C2 Compiler를 사용하기 위해 `-server` 옵션을 사용했어야만 했음. (이후 버전부터 X)
                    - Graal 컴파일러로 C2 대체 가능 (Java10부터 )
                        - Graal can run in both just-in-time and [ahead-of-time](https://www.baeldung.com/ahead-of-time-compilation) compilation modes to produce native code.
                        - AoT 방식 JIT 방식?
        - AOT , JIT
            - 실행 전에 미리컴파일 해둘거냐, 런타임에 실시간으로 할거냐의 차이
            - 둘 다 장단점이 존재

### class loader

- 자바 클래스들을 메모리로 로드하는 역할,
- Loading, Linking, initialization 기능을 담당한다.

### **runtime data area (위의 구성 요소들과 상호작용)**

- 스레드 공유
    - method area
        - **클래스의 메타 데이터**를 가지고 있는 영역, **상수 const** 변수들, **static** 기본적으로 메모리에 로드되어있는 친구들 위치
    - heap
        - 참조 값들,
- 스레드 각자
    - stack
    - pc
    - native method area?

Hotspot JVM

- Longview Technologies LLC라는 회사에서 1999년에 처음 발표한 JVM → SUN → Oracle
- 중요 기능으로 최신 버전의 JVM 들에 포함됨



## JVM 동작과정 

hello world 쳤을때ㅡ 

.java -> .class -> 클래스 로더가 로드, 링킹 -> 실행 
되는 과정일 듯



## 메모리 관리

GC 어떤전략 가지고 실행되는지 

엔진 어떤식으루

JIT(Just-In-Time) 컴파일러 설명

### Header3

## 마무리
