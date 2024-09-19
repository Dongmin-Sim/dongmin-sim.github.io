---
title: Stream 구조와 특징
author: codongmin
date: 2024-06-10T23:42:00
categories: 
tags: 
preview:
---


## 컬렉션 
Java에는 데이터를 정의하고 처리하기 위한 구조로 컬렉션을 제공한다. 그리고 대부분의 프로그래밍 작업에서 컬렉션을 이용한 작업이 사용된다. 예를 들어 특정 커피 컬렉션에서 커피들을 반복하면서 커피의 총 카페인양을 구한다던지, 이중에서 카페인이 없는 디카페인 음료만 따로 필터링하는 로직이 요구될 수도 있다. 


## Stream API란?

스트림은 자바 8에 새로 추가된 기능이다. 

> Sequence of elements supporting sequential and parallel aggregate operations
> 순차 & 병렬 집합 연산을 지원하는 요소들의 나열

스트림은 **데이터 처리 연산**을 지원하는 **연속된 요소**들의 모음이라고 정의할 수 있다. 
다음 정의를 쪼개서 하나씩 살펴보자. 

**연속된 요소 (Sequence of elemets)** 
- 연속적인 특정 데이터의 형식으로 이루어진 데이터의 처리방법을 의미한다.

**데이터 처리 연산**(aggregate operations)
- 위에서 언급한 데이터의 처리방법을 의미한다.
- filter, map, reduce, find, match, sort와 같은 연산들이 이에 해당한다.


## 자바 8 이전 , Stream 비교

(코드, 작성, 코드가 읽히는 순서를 굵은 표시로 표현하기) 



### 컬렉션과 stream 차이




스트림은 데이터 소스를 단 한번만 탐색할 수 있다.
- 반복자와 마찬가지로 한번 탐색한 요소를 다시 탐색하려면, 초기 데이터 소스에서 새로운 스트림을 만들어야 한다. 
- 반복사용할 수 있어야하는 데이터 소스여야 한다. 데이터 소스가 I/O라면 반복 불가능하니, 새로운 스트림을 만드는 것이 불가능하다.



## Stream 특징 

### 1. 선언형 
- 스트림을 이용하면 선언형 (연산의 결과로 무엇을 하기를 바라는지 나타내는)프로그래밍 방식으로 컬렉션 데이터를 처리할 수 있다. 
### 2. 내부 반복
사용자가 반복자를 사용하여 직접 요소를 반속하는 것을 외부 반복이라고 한다. 
반복을 알아서 처리하고, 결과 스트림 값을 어딘가에 저장해주는 것을 내부 반복이라고 한다.
- 반복자(for, while)를 이용해서 명시적으로 요소를 반복하는 컬렉션과는 다르게, 내부 반복을 지원한다. 
- 이에 대한 연산 방법을 숨긴다.



### 3. 병렬 처리
- 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다. 

### 4. 파이프라이닝
대부분의 스트림 연산은 스트림 연산끼리 연결할 수 있도록 디자인되었다. 따라서 연산의 결과는 스트림 자신을 반환하도록 되어있다. 
파이프라인은 데이터 소스에 적용하는 질의 같은 존재이다.
덕분에 lazyness, short-circuiting과 같은 성능 최적화를 얻을 수 있다. 

lazuness
- 데이터를 언제 계산하느냐가 컬렉션과 스트림의 가장 큰 차이다. 
- 컬력센은 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조이다. 
- 즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야 한다. 

short-circuiting
- and 연산에서 하나라도 거짓이면, 나머지 표현식에 대한 평가를 중지하고 결과를 반환
- 전체 스트림을 처리하지 않아도 결과를 반환할 수 있는 연산 -> limit, matching 

반면 스트림은 이론적으로 요청할 때만 요소를 계산하는 자료구조다. -> 스트림에 요소를 추가하거나, 스트림에서 요소를 제거할 수 없다. 게으르게 만들어지는 컬력센과 같다.

이러한 특성으로 인해 결과적으로 생산자 - 소비자 관계를 형성한다. 
인터넷 스트리밍을 예시로 들기 적합하다.



- 선언형 : 간결하고 가독성이 좋음
- 조립 가능 : 유연하게 대처 가능 
- 병렬화 : 성능상의 이점

## Stream 구조

Stream은 기본적으로 데이터 소스를 필요로 한다. 

주어진 데이터 소스에 대해 연산처리를 지원해주는데 연산처리를 담당하는 스트림 오퍼레이션은 두 부분으로 나뉜다. 

-  _intermediate_ (중간 작업)
- _terminal_ (종단 작업)

그리고 이 2가지를 결합하여서 **스트림 파이프 라인**을 형성한다.

그림으로 나타내면 다음과 같다. 

![[Pasted image 20240610230343.png]]
### 데이터 소스 (Data Source)
데이터 소스는 스트림을 만들 수 있는 대상(target)을 의미한다. 다음과 같은 형태들이 데이터 소스가 될 수 있다. 
- From a [`Collection`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Collection.html "interface in java.util") via the `stream()` and `parallelStream()` methods;
- From an array via [`Arrays.stream(Object[])`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Arrays.html#stream-T:A-);
- From static factory methods on the stream classes, such as [`Stream.of(Object[])`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/Stream.html#of-T...-), [`IntStream.range(int, int)`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/IntStream.html#range-int-int-) or [`Stream.iterate(Object, UnaryOperator)`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/Stream.html#iterate-T-java.util.function.UnaryOperator-);
- The lines of a file can be obtained from [`BufferedReader.lines()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/io/BufferedReader.html#lines--);
- Streams of file paths can be obtained from methods in [`Files`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/nio/file/Files.html "class in java.nio.file");
- Streams of random numbers can be obtained from [`Random.ints()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Random.html#ints--);
- Numerous other stream-bearing methods in the JDK, including [`BitSet.stream()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/BitSet.html#stream--), [`Pattern.splitAsStream(java.lang.CharSequence)`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/regex/Pattern.html#splitAsStream-java.lang.CharSequence-), and [`JarFile.stream()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/jar/JarFile.html#stream--).
### 중간 연산 (Intermediate Operations)


중간 연산의 특징은 연산의 반환 타입이 **스트림**이라는 것이다. 
그리고 이 연산들은 실제 스트림을 적용하는 원본 데이터 소스를 건들이지 않는다. 단지 원본 데이터에서 연산을 수행한 결과들을 리턴하는 것 뿐이다. 따라서 중간 연산들을 연결하여 질의를 만들 수 있다. 

그리고 중간 연산들은 lazy 한 특성을 가지고 있다. 연산을 **종단 연산 terminal**이 호출되기전 시점까지 미루는 특징을 가지고 있다. 따라서 뒤에 종단 연산이 존재하지 않는다면, 중간 연산은 아무런 연산도 수행하지 않는다.

`Stream<T> filter(Predicate<? super T> predicate`
중간연산은 없어도 되고, 다수의 여러 중간연산이 존재해도 무방하다. 


### 종단 연산(Terminal Operations)

종단 연산은 스트림 파이프라인에서 결과를 도출하는 역할을 맡는다. 
때문에 종단 연산은 새로운 **Stream을 리턴**하지 않는다. 
스트림이 종료되는 시점으로 **연산의 결과 타입**이 리턴된다. 

한개의 종단 연산만 가능하다.

종단 연산을 호출하기 전까지는 데이터소스에서 무엇도 선택되지 않는다. 그러므로 출력결과도 없다. 
즉, 종단 연산이 호출되지 전까지는 메서드의 호출이 저장되는 효과가 있다. 

filter나, sorted, map, collect와 같은 연산은 고수준 빌딩 블록으로 이루어져, 특정 스레딩 모델에 제한되지 않고, 자유롭게 어떤 상황에서든 사용할 수 있다. 결과적으로 데이터 처리 과정을 병렬화하면서 스레드와 락을 걱정할 필요가 없다. 



## Stream 연산 활용 

스트림 연산 : 상태 없음과 상태 있음.

Filtering
- predicate 
- distinct
Slicing
- predicate
- 처음 몇개 무시 : skip
- 특정 크기로 줄이기
Mapping (특정 데이터를 선택하는 작업)
	인수로 제공된 함수가 각각의 요소에 적용되며, 적용된 결과가 새로운 요소로 반환된다.(변환보다는 새로운 값을 만든다)
- map, flatMap
검색과 매칭 
- , , , findFirst, findAny 등 다양한 유틸리티 메서드 제공
- anyMatch(predicate) 적어도 한 요소와 일치하는지 확인 
- allMatch (predicate) 모든 요소와 일치하는지 검사
- noneMatch, allMatch와 반대 연산을 수행 
요소 검색 
- findAny : 현재 스트림에서 임의의 요소를 반환 
	- `Optional<T>`를 반환

리듀싱
- 요소의 합 등, 값을 단일된 결과로 리턴할때까지 반복하는 연산 
- map-reduce 기법 

```java
Integer dishCount = menu.stream()  
        .map(dish -> 1)  
        .reduce(0, Integer::sum);  
System.out.println("dishCount = " + dishCount);
```
리듀스 메서드의 장점


## 숫자형 스트림 
Integer를 기본형으로 언방싱해야함.

기본형 특화 스트림 

스트림 API 박싱 비용을 피할 수 있도록  기본형 특화 스트림을 제공함. 

boxed 메서드로 숫자형 스트림 <-> 객체 스트림으로 변환이 가능함.
```java
IntStream mapToInt(ToIntFunction<? super T> mapper);

public interface IntStream extends BaseStream<Integer, IntStream> {
	...
	IntStream map(IntUnaryOperator mapper);
	...
	Stream<Integer> boxed();
}
```

boxed 찾아가보니 
```java

abstract class IntPipeline<E_IN>  
        extends AbstractPipeline<E_IN, Integer, IntStream>  
        implements IntStream {
	...
	
	@Override  
	public final Stream<Integer> boxed() {  
		return mapToObj(Integer::valueOf, 0);  
	}
	...
	
	private <U> Stream<U> mapToObj(IntFunction<? extends U> mapper, int opFlags) {  
	    return new ReferencePipeline.StatelessOp<Integer, U>(this, StreamShape.INT_VALUE, opFlags) {  
	        @Override  
	        Sink<Integer> opWrapSink(int flags, Sink<U> sink) {  
	            return new Sink.ChainedInt<U>(sink) {  
	                @Override  
	                public void accept(int t) {  
	                    downstream.accept(mapper.apply(t));  
	                }  
	            };  
	        }  
	    };  
	}
}
```

기본형 OptionalInt
```java
OptionalInt maxCalories = menu.stream()  
        .mapToInt(Dish::getCalories)  
        .max();
---
@jdk.internal.ValueBased  
public final class OptionalInt {
	...
	private static final OptionalInt EMPTY = new OptionalInt();

	private final boolean isPresent;  
	private final int value;

	private OptionalInt() {  
	    this.isPresent = false;  
	    this.value = 0;  
	}
}
```
- 값이 없는 경우는 OptionalInt.empty가 반환됨.
- OptionalInt는 `boolean` isPresent 과 `int` value를 가지고 있음. 



피타고라스 정리 
```java
Stream<int[]> pythagoreanTriples = IntStream.rangeClosed(1, 100).boxed()  
        .flatMap(a ->  
                IntStream.rangeClosed(a, 100)  
                        .filter(b -> Math.sqrt(a * a + b * b) % 1 == 0)  
                        .mapToObj(b -> new int[]{a, b, (int) Math.sqrt(a * a + b * b)})  
        );  
  
pythagoreanTriples.limit(5)  
        .forEach(t-> System.out.println(t[0] + " " + t[1] + " " + t[2]));
```
- **내부 스트림**: 각 `a`에 대해 여러 `b` 값을 처리하여 `Stream<int[]>`을 반환하는 작은 스트림.
- **평탄화**: 이러한 여러 내부 스트림을 모두 하나의 스트림으로 합치는 과정. 이로 인해 `Stream<Stream<int[]>>`이 아니라, **하나의 연속된 `Stream<int[]>`**이 됩니다.

---
## Stream 특징

다시 스트림의 특징을 정리하자면 다음과 같다. 


- 스트림은 반복문과 같이 유한한 사이즈를 같지 않는다. 무제한의 사이즈를 가질 수 있다. 
	- 길이가 정해져 있지 않은 데이터소스의 연산을 수행할 수 있다. 
	- `limit(n)` or `findFirst()` 과 같은 Short-circuiting 연산들로 제한할 수 있다. 

- No storage. A stream is not a data structure that stores elements; instead, it conveys elements from a source such as a data structure, an array, a generator function, or an I/O channel, through a pipeline of computational operations.
- Functional in nature. An operation on a stream produces a result, but does not modify its source. For example, filtering a `Stream` obtained from a collection produces a new `Stream` without the filtered elements, rather than removing elements from the source collection.
- Laziness-seeking. Many stream operations, such as filtering, mapping, or duplicate removal, can be implemented lazily, exposing opportunities for optimization. For example, "find the first `String` with three consecutive vowels" need not examine all the input strings. Stream operations are divided into intermediate (`Stream`-producing) operations and terminal (value- or side-effect-producing) operations. Intermediate operations are always lazy.

- Consumable. The elements of a stream are only visited once during the life of a stream. Like an [`Iterator`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Iterator.html "interface in java.util"), a new stream must be generated to revisit the same elements of the source.

병렬처리 
### Parallelism

Processing elements with an explicit `for-`loop is inherently serial. Streams facilitate parallel execution by reframing the computation as a pipeline of aggregate operations, rather than as imperative operations on each individual element. All streams operations can execute either in serial or in parallel. The stream implementations in the JDK create serial streams unless parallelism is explicitly requested. For example, `Collection` has methods [`Collection.stream()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Collection.html#stream--) and [`Collection.parallelStream()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/Collection.html#parallelStream--), which produce sequential and parallel streams respectively; other stream-bearing methods such as [`IntStream.range(int, int)`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/IntStream.html#range-int-int-) produce sequential streams but these streams can be efficiently parallelized by invoking their [`BaseStream.parallel()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/BaseStream.html#parallel--) method. To execute the prior "sum of weights of widgets" query in parallel, we would do:

```

     int sumOfWeights = widgets.
```

The only difference between the serial and parallel versions of this example is the creation of the initial stream, using "`parallelStream()`" instead of "`stream()`". When the terminal operation is initiated, the stream pipeline is executed sequentially or in parallel depending on the orientation of the stream on which it is invoked. Whether a stream will execute in serial or parallel can be determined with the `isParallel()` method, and the orientation of a stream can be modified with the [`BaseStream.sequential()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/BaseStream.html#sequential--) and [`BaseStream.parallel()`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/stream/BaseStream.html#parallel--) operations. When the terminal operation is initiated, the stream pipeline is executed sequentially or in parallel depending on the mode of the stream on which it is invoked.

Except for operations identified as explicitly nondeterministic, such as `findAny()`, whether a stream executes sequentially or in parallel should not change the result of the computation.

Most stream operations accept parameters that describe user-specified behavior, which are often lambda expressions. To preserve correct behavior, these _behavioral parameters_ must be _non-interfering_, and in most cases must be _stateless_. Such parameters are always instances of a [functional interface](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/function/package-summary.html) such as [`Function`](https://docs.oracle.com/javase%2F8%2Fdocs%2Fapi%2F%2F/java/util/function/Function.html "interface in java.util.function"), and are often lambda expressions or method references.
