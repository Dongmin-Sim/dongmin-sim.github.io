---
title: 테스트 더블이란?
author: codongmin
date: 2024-06-09T13:02:00
categories:
  - test
  - tdd
tags:
image:
  path: /assets/img/thumbnail/test.png
  lqip:
  alt: 테스트 관련 포스트
preview: 테스트 더블에 대한 개념을 설명하고, Stub, Mock에 대해서 자세히 알아봅니다.
---

## 0. 들어가며

테스트 관련 개념을 공부하다 보면 나오는 개념이 있다. 바로 Test Double 이라는 개념인데, 이 글에서는 테스트 더블에 대한 개념과 예시들을 살펴보면서 Test Double이 무엇인지 알아보려 한다.

그리고 Test Double에 해당하는 개념인 Stub과 Mock에 대해서 간단히 알아보고 이 둘의 차이를 설명해보고자 한다.

### Dummy란

**더미(Dummy)** 라는 개념을 들어보았는가?

주로 8시 뉴스나, 유튜브의 여러 자동차를 생산하는 회사에서 **차량의 안정성 테스트**를 진행할때 다음과 같은 익숙한 장면을 볼 수 있다. 특히 차량의 탑승자 **역할을 대신**하여 사람이 받는 피해를 직관적으로 보여주고 측정할 수 있는 사람 모양의 **더미(Dummy)** 는 우리에게 익숙한 대상이다.

![Desktop View](https://i.gifer.com/BwMq.gif){: width="972" height="589" .w-50}_차량 충돌 테스트의 테스트 더미_

이러한 더미를 쓰는 이유는 굳이 설명을 하지 않아도 우리가 직관적으로 이해할 수 있는데, 실제 사람이 충돌용 차량을 타고 시속 100km로 콘크리트 벽에 박는 테스트는 **위험부담이 크기** 때문이다. 본능적으로 가속 페달을 밟을 수 없을 뿐더러, 설사 기계로 페달을 밟는다고 해도, 충돌 직전에 어떠한 움직임을 할지도 모르기 때문에 독립적인 테스트 환경을 만드는 것이 어려울 뿐 아니라 매우 위험하다. (반복 테스트도 어렵다. 너무 비윤리적이기 때문에..)

때문에 **충돌 테스트(검증 환경)** 에서 **차량의 안정성(테스트 대상)** 을 위해 **사람의 역할(테스트 대상을 보조)** 을 대신해주는 더미를 사용한다.

하지만 더미(Dummy)는 특성 상황에서, 사람의 역할을 대신할 수 있지만, 그것이 사람을 의미하는 것은 아니다.  
사람과 정확히 동일하다고 볼수는 없다.

조금 더 일반화된 정의에서 더미(Dummy)란 다음과 같이 정의할 수도 있을 것 같다.

> **테스트 환경**에서 **테스트 대상**을 검증하기 위해 테스트 대상에 **필요한 기능/환경을 대신**하는 것

## 1. Test Double

개발과 테스트 환경에서도 앞선 더미(Dummy)와 비슷한 개념이 존재한다.

![Desktop View](https://i.gifer.com/2A1.gif){: width="972" height="589" .w-50}_그게 나야 둠빠둠빠_

단위테스트를 진행하는데 있어 테스트 환경을 만들기 까다로운 클래스를 직접 생성해서 테스트 하는 것이 아닌,
이런 객체들을 대신해 테스트를 진행할 수 있는 객체가 있는데..

이러한 객체들을 **Test Double**라고 부른다.

앞에서 충돌테스트에 사용되는 더미(Dummy)의 예시를 든것과 같이,

> **차량 충돌 테스트**에서 **차량의 안정성**을 테스트하기 위한, 사람의 **신체 구조** 역할만 대신
>
> - 이때 더미는 말하고, 듣고, 움직이는 기능들은 필요하지 않다. (테스트 목적에 부합하지 않기 때문)

**Test Double**은 테스트에서 **관심사 검증**을 위해 외부 요인의 **필요한 기능**을 대신 제공해주는 대상(객체)이라고 할 수 있다.

### 1.1 Test의 구별

Martin Fowler의 블로그 글인 [Unit Test](https://martinfowler.com/bliki/UnitTest.html) 에서 테스트 단위와 관련된 설명 중 **Test Double** 개념이 같이 나온다. 앞으로 나올 내용들의 이해를 위해 간략하게 언급하고 넘어가려고 한다.

예를 들어, "_회원들의 평균 구매 금액 계산_" 이 테스트에서 검증하려고 하는 **주요 관심사**라고 하자.

우리가 궁금한 것은 "_회원들의 평균 구매 금액 계산_"기능이 **의도한 대**로 구현이 되었는지, 실제로 회원들의 구매 금액의 총액이 회원들의 수만큼 잘 나누어지는지, 계산 방법이 틀리지 않았는지가 테스트하고 싶은 포인트이다.
그런데 이를 테스트 하려면 다음과 같은 과정들이 필요하다.

1. 회원 구매 DB 에 연결한다.
2. DB에 데이터를 삽입한다.
3. DB에서 데이터를 조회한다.
4. **조회된 데이터를 바탕으로 *평균 구매 금액을 계산*한다.**
5. DB 연결을 종료한다.

이 테스트에서 검증하려고 하는 주요 관심사는 4번이다. 하지만 4번을 수행하기 위해 앞서 1,2,3 그리고 5번의 과정이 동반된다. 이는 테스트 하고자 하는 기능이 1,2,3,5번 과정에 의존적이라는 말과도 동일하다.

![Desktop View](/assets/posts/test/what-is-test-double/sociable-tests.png){: width="972" height="589" .w-50}_Sociable Tests 예시_
이러한 테스트를 **Sociable Tests** 라고 저자는 구분한다.

하지만, 만일 1번이나 2번 과정에서 어떠한 연유 때문에 테스트가 실패하게 된다면, **조회된 데이터를 바탕으로 *평균 구매 금액을 계산*한다.** 라는 테스트의 주된 관심사를 확인하기란 여간 쉬운일이 아닐 것이다.

때문에 이는 독립적 테스트로 볼 수 없다. 변수들간의 **의존관계**가 생기기 때문에 이는 의존적 테스트로 봐야한다.

만약에 테스트 대상을 둘러싼 모든 변수들을 통제할 수 있는 독립적 테스트를 구성할 수 있다면, 어떨까?
![Desktop View](/assets/posts/test/what-is-test-double/solitary-tests.png){: width="972" height="589" .w-50}_Solitary tests 예시_

이러한 테스트를 **Solitary Test**라고 구분한다.
대신에 앞선 1, 2, 3, 5번 과정을 대신 해줄 **어떤 무언가**가 필요하다.

바로 이때, **Solitary Test**를 진행하기 위해 필요한 **어떤 무언가**로 **Test Double**이 사용될 수 있다.

## 2. Stub과 Mock

이러한 역할을 하는 Test Double 들의 모임을 **Test Doubles** 라고 부른다. 단순히 복수형이 붙은 것 뿐이다.

그 종류들은 다양한데, 그 중 대표적인 **Stub**과 **Mock**에 대해서 알아보려고 한다.

각 더블들이 가지는 특징이 서로 다르기 때문에 테스트의 상황과 맥락에 따라 적절한 더블을 선택해서 사용하거나, 이들을 중복해서 사용할 일도 있을 수 있다.

지금부터는 이들의 특성을 하나씩 알아가보자

### 예시상황

가령 **현재 위치**가 **입력**으로 주어지면, 해당 위치의 **날씨 정보**를 받아와 날씨 정보에 적합한 **권고 활동**을 내려주는 API가 있다고 가정해보자.

```java
public class WeatherServiceImpl implements WeatherService {

    private final LocationAPIService locationAPIService;
    private final WeatherAPIClient weatherAPIClient;

    public WeatherServiceImpl(LocationAPIService locationAPIService, WeatherAPIClient weatherAPIClient) {
	    this.locationAPIService = locationAPIService;
	    this.weatherAPIClient = weatherAPIClient;
    }

    @Override
    public ActivityAdvice getActivityAdvice(String location) {
    // 현재 위치 받아오기
    final LocationInfo locationInfo = locationAPIService.getLocationInfo(location);
    // 실제 API 호출
    final String weatherData = weatherAPIClient.fetchWeatherData(locationInfo);

    if (weatherData.equals("우박")) {
	    return new ActivityAdvice(locationInfo, weatherData, "실내 활동");
    }
    // ...
    return new ActivityAdvice(locationInfo, weatherData, "자유로운 활동");
    }
}
```

- `getActivityAdvice` 메소드는 `locationAPIService.getLocationInfo()` 메소드를 호출하여 위치 정보를 받아오고,
- `weatherAPIClient.fetchWeatherData()` 는 `LocationInfo`를 인자로 받고 받은 인자를 기반으로 외부 API를 호출하여 해당 위치의 날씨 정보를 받아온다.
- 받아온 날씨정보를 바탕으로 권고활동을 의미하는 `ActivityAdvice` 를 내려준다.
  - ActivityAdvice 는 위치의 도시와 정보가 담겨 있는 `locationInfo` 와
  - 날씨 정보를 나타내는 `weather`
  - 권고 활동을 나타내는 `recommandedActivity` 필드로 구성되어있다.



이 API를 테스트하는 상황을 예시로 아래 test double 들을 설명해보고자 한다.
(테스트 더블에 대한 이해를 주 목표로 상황을 가정하는 것으로 디테일한 부분에 대한 현실성이나 실현가능성은 후순위로 미룬다)

### 2.1 Test Stub

위의 테스트를 진행하려면 먼저 외부의 **의존성 문제**를 먼저 해결해야한다. 테스트 대상인 `getActivityAdvice` 메소드는 현재 위치를 받아오는 `LocationAPIService`와 위치 정보를 기반으로 날씨정보를 받아오는 `WeatherAPIClient`를 의존하고 있다.

만일 이런 상태에서 **"지역이 서울이고, 날씨정보가 우박일 때, 실내 활동을 권고하는 `ActivityAdvice`를 반환"**한다는 테스트 케이스를 검증하고 싶을때, 어떻게 해야할까?

노트북을 서울지역으로 들고 나가서, 우박이 내릴때 테스트 실행 버튼을 눌러야할까? 설사 가능하다해도 테스트 비용이 만만치 않을것으로 예상된다. 만약 우박이 아니라 천둥번개, 돌풍과 같은 다른 상황은 또 어떻게 테스트할까? 또 하필 그때 네트워크가 불안정하다면?

이때 테스트 더블을 사용해서 실제 위치나 날씨 정보에 대한 외부 의존성을 **대신**해줄 수 있다.
![Desktop View](/assets/posts/test/what-is-test-double/stub-example.png){: width="972" height="589"}_Stub 예시_

실제 외부 서비스와 통신하는 부분을 대신해서 서울 지역에 대한 위치 정보를 받아오는 부분을 별도의 내부 해시맵을 가진 **별도의 클래스**를 구현함으로써 대체가 가능하다.
이 클래스에서는 지정된 `input`에 대해 예상되는 정적인 `output`을 내보내주기만 하면 된다.

이때 입력에 대해 **지정된 출력**을 내보내는 역할을 하는 클래스를 **Stub**이라고 부른다.

```java
WeatherService sut;

@Nested
class Stub {

    @BeforeEach
    void setUp() {
        LocationAPIServiceStub locationAPIServiceStub = new LocationAPIServiceStub();
        WeatherAPIClient weatherAPIClientStub = new WeatherAPIClientStub();
        sut = new WeatherServiceImpl(locationAPIServiceStub, weatherAPIClientStub);
    }

    @Test
    @DisplayName("지역이 서울이고, 날씨정보가 우박일 때, 실내 활동 권고하는 Advice를 반환")
    public void getActivityAdviceTest() {
        // given
        String location = "seoul";
        // when
        var result = sut.getActivityAdvice(location);
        // then
        Assertions.assertThat(result).isInstanceOf(ActivityAdvice.class);
        Assertions.assertThat(result).hasFieldOrPropertyWithValue("recommandedActivity", "실내 활동");
    }
}
```

```java
public class LocationAPIServiceStub implements LocationAPIService {

    private final Map<String, LocationInfo> locationInfoMap = new HashMap<>();

    public LocationAPIServiceStub() {
        locationInfoMap.put("jamsil", new LocationInfo("seoul", "111", "111"));
        locationInfoMap.put("guro", new LocationInfo("seoul", "333", "333"));
        locationInfoMap.put("seongnam", new LocationInfo("gyeong-gi", "222", "222"));
    }

    @Override
    public LocationInfo getLocationInfo(String location) {
        return locationInfoMap.get(location);
    }
}
```

실제 `LocationAPIService` 인터페이스를 구현한 `LocationAPIServiceImpl`은 `getLocationInfo` 메소드에 실제 외부 시스템과 통신하여 위치정보를 받아오는 로직들이 구현되어 있겠지만, 우리는 `Stub`을 사용해서 훨씬 간편하게, 외부 의존성을 대체하는 방식으로 구현했다.

위의 예시로 살펴본 `Stub` 의 특징은 다음과 같다.

#### 특징

- **외부 의존성**을 제거할 수 있다.
- Stub은 테스트 중에 만들어진 호출에 대해 미리 **준비된 답변**을 제공한다.
- 실제 객체처럼 동작하는 클래스를 직접 구현하는데, 테스트에 **필요한 구현에만 집중**하고,
  - 부가적인 기능은 구현하지 않는다.
- 주로 **상태 검증**에 사용한다. 어떤 입력에 대해 어떤 출력이 발생하는지 검증.
- 테스트 코드가 **간결**하다.
- **별도 구현** 클래스가 필요하다.

### 2.2 Test Mock

`Mock`은 실제 객체의 동작을 모방하는 객체(Mock Object)를 말한다.

위에서 Stub은 실제 객체를 구현하여 동작방식이 미리 구현된 메소드로 우회하는 방식이라면, Mock은 실제 객체를 본떠 마치 메소드의 입력과 출력에 내가 원하는 모듈을 끼워넣는 것 같은 느낌이다.
![Desktop View](/assets/posts/test/what-is-test-double/mock-example.png){: width="972" height="589"}_Mock 예시_

위의 그림으로 표시한 것처럼 Mock 객체의 메소드의 입력과 출력을 내가 원하는대로 구성할 수 있다. 그리고 실제로 이 메소드가 호출되었는지 안되었는지 확인할 수 도 있다. 이를 코드로 확인해보자.

```java
@Nested
class Mock {
    LocationAPIService locationApiServiceMock;
    WeatherAPIClient weatherAPIClientMock;

    @BeforeEach
    void setUp() {
        locationApiServiceMock = Mockito.mock(LocationAPIService.class);
        weatherAPIClientMock = Mockito.mock(WeatherAPIClient.class);

        sut = new WeatherServiceImpl(locationApiServiceMock, weatherAPIClientMock);
    }

    @Test
    @DisplayName("지역이 서울이고, 날씨정보가 우박일 때, 실내 활동 권고하는 Advice를 반환")
    public void getActivityAdviceTest() {
        // given
        String location = "seoul";
        BDDMockito.given(locationApiServiceMock.getLocationInfo(any()))
                .willReturn(new LocationInfo(location, "111", "111"));
        BDDMockito.given(weatherAPIClientMock.fetchWeatherData(any()))
                .willReturn("우박");
        // when
        var result = sut.getActivityAdvice(location);

        // then
        Assertions.assertThat(result).isInstanceOf(ActivityAdvice.class);
        Assertions.assertThat(result).hasFieldOrPropertyWithValue("recommandedActivity", "실내 활동");

        BDDMockito.then(locationApiServiceMock).should().getLocationInfo(any());
        BDDMockito.then(weatherAPIClientMock).should().fetchWeatherData(any());
    }
}
```

Mock은 Stub과 달리 별도의 클래스 구현이 필요하지 않다. 대신 Mockito 프레임워크를 이용해 Mocking 하고자 하는 객체를 Mock으로 초기화 시켜주어야 하고, Mock으로 초기화된 객체들의 호출과, 호출 결과를 미리 작성해주어야 한다.

```java
BDDMockito.given(weatherAPIClientMock.fetchWeatherData(any()))
                .willReturn("우박");
```

다음 코드는 `weatherAPIClientMock.fetchWeatherData()` 를 호출할때,

- 입력값은 **어떠한 입력값(any)** 이 들어가도 무방하고,
- 대신 반환되는 값은 **"우박"** 이어야 한다
  라는 `fetchWeatherData()`메소드의 **행동 명세**를 기술한 것과 동일하다.

```java
BDDMockito.then(weatherAPIClientMock).should().fetchWeatherData(any());
```

실제로 `weatherAPIClientMock.fetchWeatherData()` 메소드가 호출되었는지 확인하는 코드이다.
`should()`안에는 `times(1)` 검증모드를 넣어 해당 메소드가 몇번 호출되었는지 확인할 수 있는데, 기본값은 1번이다.

#### 특징

- **외부 의존성**을 제거할 수 있다.
- 테스트 대상의 내부 로직을 원하는 대로 **stubbing** 할 수 있다.
- 메서드의 **호출에 대한 기대를 명세**하고, **미리 정의해놓은 내용에 따라 동작(or) 호출** 했는지 **검증**하기 위해 사용한다.
- 더 **복잡한 행위 검증**이 가능하다.
- 메서드의 반환값을 **동적으로 처리**할 수 있다.
- 의존 객체에 대한 **행위 명세**가 필요하다.
- 별도 구현 클래스가 필요없다.

---

## 3. 비교

#### Stub vs Mock 차이

처음에 이 개념을 접했을 때는 혼동이 많이왔다. 미리 준비된 답변을 제공하는 Stub이나, 호출에 대한 기대 명세를 리턴하는 Mock은 같은거 아닌가라는 생각이 들었다. 오히려 Mock이 Stub의 개념을 더 확장한 듯한 느낌도 든다.

결론부터 말하자면 검증하고자 하는 맥락에 따라 둘의 개념차이가 존재한다.
Stub은 주로 **상태** 검증이 필요할때, Mock은 **행동** 검증이 필요할때 주로 사용한다.

Stub : 테스트에 필요한 호출에 대해 **미리 준비된 답**을 제공하는 객체,

- 실제 객체처럼 동작하는 클래스를 직접 구현하는데, 테스트에 **필요한 구현에만 집중**하고, 부가적인 기능은 구현하지 않는다.
- input에 대해서 어떤 **output**이 반환되었는지 검증한다.
- 즉, **상태**(value)에 대한 검증

Mock : 예상된 동작을 가진 객체

- 다양한 Mock Framework를 통해서 **Mock 객체를 생성**하고 **호출에 대한 기대 명세**와 기대되는 **행동에 명세**를 정의한다.
- Mock 객체를 새롭게 작성하지는 않는다. 기존의 프로덕션 코드로 쓰여진 객체를 데려와서 가상의 형태처럼 만드는 방식
- 마치 AOP의 방식 처럼, 해당 mock 객체가 호출되는 시점에서 cut를 해서 기대 호출(call)명세대로, 종료되는 시점에서의 기대되는 **행동(return)명세**대로 수행하도록 **흐름**을 변경
- Input과 Output에 관심이 없다. 정해져 있는 비지니스 로직대로 행동이 되었는지를 흐름을 검증한다.
- 즉, **행동**(Behavior)을 검증한다.

#### 둘은 언제 사용해야하나

A의 경우에는 Stub을 사용해!, B의 경우에는 Mock을 사용해야해! 라고 명확하게 나누긴 어려웠으나, 나는 다음과 같은 기준으로 나눌 수 있다면, 적절한 분류기준이 되지 않을까 싶다.

Stubbing이나 Mocking 할 대상 **객체의 입력**이 중요하지 않은 경우(any)에는 **Mock을 사용하는 것**이 더 자연스러워보인다.
**단순한 입력값과 출력값**을 필요한 경우라면 Stub을 사용하는 것이 더 간편할 수도 있어보인다.

이것은 명확히 정해져 있는 가이드라인이 있기보다, 상황과 **테스트하려는 맥락**에 따라 다르게 사용해야 하는 중요해보인다.

위의 예시에서 단순히 위치를 받아 위치정보를 반환하는 `LocationAPIService` 은 Stub으로,
날씨정보를 리턴하는 `WeatherAPIClient`의 경우에는 입력값인 `LocationInfo` 보다 이후에 output 값으로 나오는 `weatherData`가 이후 로직의 분기를 결정하므로 이를 테스트하기 위해 행동 명세를 구체적으로 지정할 수 있는 Mock으로 구성하였다.

이렇게 구성하면 `@ParameterizedTest` 어노테이션을 활용하여 여러 테스트 케이스에 대해 쉽게 검증할 수 있는 코드를 작성할 수 있다.

```java
@Nested
class Together {
    LocationAPIService locationApiServiceStub;
    WeatherAPIClient weatherAPIClientMock;

    @BeforeEach
    void setUp() {
        locationApiServiceStub = new LocationAPIServiceStub();
        weatherAPIClientMock = Mockito.mock(WeatherAPIClient.class);

        sut = new WeatherServiceImpl(locationApiServiceStub, weatherAPIClientMock);
    }

    @ParameterizedTest(name = "지역이 {0}이고, 날씨정보가 {2}일 때, {3} 권고하는 Advice를 반환")
    @CsvSource(value = {
            "jamsil,seoul,우박,실내 활동",
            "guro,seoul,태풍,대피",
            "seongnam,gyeong-gi,폭염,그늘",
    })
    @DisplayName("권고 활동 테스트")
    public void getActivityAdviceTest(String location, String expectCity, String weather, String expectActivity) {
        // given
        BDDMockito.given(weatherAPIClientMock.fetchWeatherData(any()))
                .willReturn(weather);
        // when
        var result = sut.getActivityAdvice(location);

        // then
        assertThat(result).isInstanceOf(ActivityAdvice.class);
        assertThat(result).hasFieldOrPropertyWithValue("locationInfo.city", expectCity);
        assertThat(result).hasFieldOrPropertyWithValue("recommandedActivity", expectActivity);

        BDDMockito.then(weatherAPIClientMock).should().fetchWeatherData(any());
    }
}
```

#### 이들은 왜 사용해야 하는가?

이유는 간단하다 **주요 관심사**에 집중하기 위해.

주요 관심사는 내가 검증하고자 하는 것/대상과 그에 맞는 테스트 결과가 필요할 뿐이지.
테스팅에 필요한 작업을 하는 것에 큰 에너지가 쓰이기 때문.

비유에 집중하지 말고 맥락에 집중해보자 (비유가 실제와 다르거나, 너무 간략화 되었을 수 있다.)

만약 연출자가 다음과 같은 스크립트를 영화에 담고 싶어할 때를 상상해보자,

> 두 남녀가 헤어지기전 중요한 대화를 나누고 있다. 두 남녀 뒷편으로 기차가 지나가고 있다.

![Desktop View](/assets/posts/test/what-is-test-double/movie-example.jpeg){: width="972" height="589" .w-50}_예시_

여기서 감독에게 중요한 것은 해당 장면의 대사와 배우들의 연기가 장면에 적절한지, 어울리는지를 검증하는 것일 것이다.  
연출자가 원하는 그림이 나오는지 확인하고 싶을 뿐, 배경은 비교적 덜 중요하다.

하지만 배경으로 담기는 실제 기차의 소리가 너무 커서 배우들의 소리가 담기지 않거나, 기차가 고장이 나서 해당 장면을 찍을 수 없다면, 배우들이 완벽한 연기를 해도, 이 상황을 맞추기 위해 계속 에너지를 소비해야 할 것이다.

이런 경우 감독은 해당 배경을 cg나 대체 기술로 처리할 수 있다면 그렇게 하는 것이 더 효율적일 것이고, 해당 장면에 담겨야 하는 배우들의 대사와 연기에 더 집중할 수 있을 것이다.

만일 검증하고자 하는 코드가 아닌 검증 대상(System Under Test, sut)이 아닌 부가적인 부분에서 에러가 발생하거나, 의존성이 걸리거나, 혹은 실제로 테스트 하고 싶은 코드가 실행되지 않고, 실행될때마다, 결과값이 달라지는 상황이 발생하게 된다면. 이런 일련의 과정, 문제와 요소들로 인해 테스트 하고자 하는 주요 테스트 관심사에 집중하지 못 할 가능성이 높다.

주요 테스트 관심사에 집중하기 위해, 필요한 주변 의존성들을 대신 맡아준다. 이것들이 테스트 더블을 사용하는 이유이자 목적일 것이다.
