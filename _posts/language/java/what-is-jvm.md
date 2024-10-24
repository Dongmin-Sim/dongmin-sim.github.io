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
    - interpreter
    - JIT : 인터프리터처럼 실행되지만, 자주 실행되는 섹션(바이트 코드)를 → 네이티브 코드(머신코드) 로 변환, 이를 캐시에 저장해 두고 사용.
    - 자주 실행되는 부분을 ‘hotspot’이라고 부름
    - 이 JIT 컴파일러 덕분에 full-compile과 비슷한 성능을 낼 수 있게 도와줌.
        - Tiered Compilation
            - C1 - Client Compiler
            - C2 - Server Compiler

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
