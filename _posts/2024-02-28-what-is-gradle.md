---
title: "Gradle, 든든한 코끼리 "
author: codongmin
date: 2024-02-28 22:16:00 +0800
categories: [Gradle, Tool]
tags: [gradle, build, ci/cd]

image:
  path: /assets/img/thumbnail/gradle.png
  lqip:
  alt: Gradle이란?
---

## 들어가며

오늘은 자바나, 스프링을 다룰때 항상 등장하는 Gradle(코끼리)에 대한 이야기를 해보려고 한다. 처음 스프링을 접하고 공부했을 때 잘 모르겠는 프로젝트 파일 구조와 빌드에 관련된 설정들, Gradle이 한꺼번에 다가오면서 대체 이것들은 뭘까 하는 압박감과 당황함이 들었었다. 적어도 나는 그랬다. 일단 실행되고 잘 돌아가니 실행하는데 도움을 주는 빌드 툴정도 로 이해하고 있었지만. 이 Gradle(코끼리)가 대체 왜 있는지, 그리고 어떤걸 도와주는지, 그리고 어떻게 동작되는지에 대한 이해는 높지 않았다.

그래서 이번 글에서는 Gradle이 어떤 도구인지? Gradle은 어떤 것을 도와주는지, Gradle의 핵심 컨셉과 동작 배경, 그리고 이들을 통해 간단한 Spring 멀티모듈 빌드 예시를 통해 설명해보려고 한다.

## 1. 빌드란 ?

결론부터 말하자면 Gradle은 Build Tool이다. 빌드를 도와주는 도구인데, 그렇다면 빌드라는 것은 무엇일까?

**빌드**는 **소스 코드 파일**을 **실행 가능한 소프트웨어**로 **변환**시키는 과정이다.

> "<u>소스코드 파일</u>", "<u>실행가능한 소프트웨어</u>", "<u>변환</u>"이란 대체 무슨말일까?

컴퓨터는 우리가 흔히 작성하는 프로그래밍 언어를 곧 바로 이해하고 실행할 수 없다. 우리가 미지의 종족을 만나서 그들의 말을 들어도 무슨 뜻인지 이해할 수 없듯이, 컴퓨터도 우리의 자연어를 곧바로 이해할 수 있는 구조가 아니다. 외계인과 소통하기 위해서 그들의 언어를 분석하고, 특정 의미를 담은 단어를 우리가 가진 언어와 매핑하여 그들의 문장을 이해하듯이 컴퓨터도 일종의 "**번역**"이 필요하다.

다음의 비유가 비약적일 수 있겠지만, 우리가 외계인들과 소통하고자 할때, 그들의 단어를 분석하여 우리가 이해할 수 있는 온전한 문장으로 번역하여 소통하는 과정에 비유해볼 수 있다.

> **nga za‘u ftu peseng?**

![Desktop View](/assets/meme/same-picture.jpg){: width="972" height="589" .w-50}_???_

어느날 외계인이 지구에 도착해 다음과 같은 말을 뱉었을 때, 우리는 이를 이해할 수 없다. 기껏해야 비슷한 발음의 단어를 찾아보겠지만, 그것은 그들이 의도하는 의미와는 매우 다를 확률이 높다.

따라서 우리는 다음과 같은 것들을 시도해볼 수 있다. 이 **개별 단어**(라고 보이는)들로 분리하고 각 단어들이 의미하는 바를 우리가 이해하는 단어로 "**번역**"하고,

- **\*nga** : [Na]PFpn.<u>you</u>\*

- **\*za’u** : ["z·a.P·u]PFvin.<u>come</u>\*

- **\*ftu–** : [ftu]PFadp.<u>from</u>(direction)\*

- **\*peseng** : [pE."sEN] PF inter. <u>where</u>\*

각 단어들 간의 **관계와 의존성**을 분석하여 그들의 **의도를 나타내는 문장**으로 최종 변환해볼 수 있다.

> You, come, from, where -> **(where are you come from?) / (where do you from?)**

이러한 일련의 단계들은 지구를 방문한 외계인들의 "(외계언어)가 (의도하는 바를 표현)하는 (변환) 과정"이라고 부를 수 있다.

컴퓨터도 마찬가지이다. 컴퓨터가 우리가 작성하는 자연어를 이해하기 위해 소스코드(단어와 구문)을 컴퓨터가 이해할 수 있는 방식(010110..)으로 바꾸고, 이를 컴파일이라고 부른다. 소스코드들을 연결하고 패키징하여 프로그래머가 의도한 것을 수행하는 소프트웨어로 변환시키는 일련의 연속된 행위를 빌드라고 한다.

> 예시로 들은 외계어는 영화 아바타에 나오는 나비족의 언어를 가져왔다.  
> 실제로 영화에서 사용하기 위한 인공어로 [나비족 언어 사전](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://files.learnnavi.org/dicts/NaviDictionary.pdf)과 [커뮤니티](https://learnnavi.org/)가 있으므로 관심 있으신 분들은 둘러봐도 좋을 듯하다.
> {: .prompt-tip }

## 2. 빌드툴이란 ?

"빌드"라는 과정이 이해가 되었다면 빌드 툴은 더 이해가 직관적이다. 빌드툴은 말 그대로 빌드를 도와주는 도구이다.

앞서 설명한 실행 가능한 소프트웨어가 되려면 소스코드가 단순히 번역 뿐 아니라 코드들 간의 서로 의존하고 있는 순서가 올바른지, 해당 소스코드가 에러를 발생시키지는 않는지 이를 확인하고 테스트하는 과정도 필요하다. 그렇지 않으면 이는 실행 가능한 소프트웨어가 될 수 없기 때문이다.

![Desktop View](/assets/meme/right.jpg){: width="972" height="589" .w-50}_좋은 대화였어_

만일 외계인이 "당신들은 위험한 존재입니까?" 라는 질문을 뱉었는데, 단순한 단어(위험)의 번역과 나열로 잘못 오해하고 그들에게 위험하고 무례한 대답을 해버린다면 어쩌면 친구가 되었을지도 모르는 외계인은 거대한 우주선으로 올라가 정수리에 무자비한 빔을 때려버리는 극단적인 상황이 벌어질 수 있다.

이러한 의사소통의 실패를 막기 위해 해당 단어들간에 가지는 의존 순서, 맥락, 그리고 그들의 언어가 제대로 번역이 되었는지 테스트해볼 필요가 있다.

빌드툴은 앞선 빌드의 과정을 손수 수동적으로 하는 것이 아니라, 일련의 과정들을 **자동화**하여 수고로움을 덜어준다. 또한 필요한 프로젝트의 **구성을 관리**해주거나, 프로젝트가 구동되기 위해 필요로 하는 라이브러리의 **의존성**을 관리해주기도 하며, **테스트 및 패키징 & 배포** 기능을 지원해주는 도구다.

## 3. Gradle란?

Gradle은 자바, C++, 안드로이드 등 여러 프로그래밍 언어로 작성된 소프트웨어를 빌드하고 관리하기 위한 오픈 소스 빌드 자동화 도구이다. 안드로이드 애플리케이션 및 여러 개발분야에서 널리 사용되며, 프로젝트의 의존성 관리, 빌드 및 테스트 자동화, 배포 등을 지원한다.

현재(8.6버전 기준) Gradle은 다음과 같은 언어와 프레임워크에서 지원을 하고 있다.

![](https://docs.gradle.org/current/userguide/img/userguide-languages.png)

또한 다음과 같은 IDE(소스코드 편집기)에서도 호환되도록 지원되고 있다.

![](https://docs.gradle.org/current/userguide/img/userguide-ides.png)

특히, Gradle는 빌드 과정을 명시하기 위해 DSL(Domain Specific Language)을 사용하여 프로젝트의 빌드 스크립트를 작성하며, 이를 통해 빌드 프로세스를 자유롭게 정의할 수 있다.

DSL이란 ?

> 특정 도메인 환경에서 사용하기 위해 만들어진 프로그래밍 언어입니다. JAVA나 C++ 과 같은 범용 프로그래밍 언어와는 반대되는 개념입니다.

| DSL 종류                  | 특징                                                                      |
| ------------------------- | ------------------------------------------------------------------------- |
| Groovy (-.gradle)         | - JVM에서 실행되는 스크립트 언어이다.<br />- 문법이 Java에 매우 비슷하다. |
| Kotlin DSL (-.gradle.kts) | - kotlin 문법으로 작성할 수 있어 직관적이고 사용이 쉽다.                  |

### 3.1 Gradle 장점

그렇다면 Gradle이 어떠한 특징, 그중에 어떤 장점들이 있길래 사용을 하는 것일까? 다음은 Gradle 공식 홈페이지에서 말하는 Gradled의 장점들을 정리하여 나열한 것들이다.

#### 유연성

- Gradle은 JVM용으로 가장 널리 사용되는 빌드 시스템이며 Android 및 Kotlin 다중 플랫폼 프로젝트의 기본 시스템. 풍부한 커뮤니티 플러그인 생태계가 존재.

- Gradle은 내장 기능, 타사 플러그인 또는 사용자 정의 빌드 로직을 사용하여 광범위한 소프트웨어 빌드 시나리오를 자동화 가능

  - 다양한 플러그인을 통해 확장 가능한 구조

- Gradle은 빌드 로직을 쉽게 읽고 쓸 수 있게 해주는 선언적이며 표현력이 풍부한 높은 수준의 빌드 언어를 제공

  - groovy DSL 지원

  - Kotlin DSL 지원

#### 성능

![Desktop View](/assets/posts/gradle-performance.png){: width="972" height="589" .w-50}_gradle, maven 성능 비교 (이미지 출처 : [maven vs gradle](https://gradle.org/maven-vs-gradle/))_

- Gradle은 증분 빌드, 빌드 캐싱, 병렬 실행과 같은 최적화의 이점을 활용하면서 신뢰할 수 있는 결과를 생성.
  - 속도 이점

#### 사용자 경험

- 빌드 디버깅 및 최적화를 위한 Build Scan을 제공하여, 빌드 시간을 최적화 할 수 있는 로깅 기능들을 제공

- 많은 IDE 제조업체들과 협력하고 편집 지원을 돕는 기능들을 개발 중
- 뿐만 아니라 CLI환경에서 명령줄 자동완성과 같은 최신 CLI환경을 구축 중

#### 종속성 관리

- Gradle은 빠르고 확장 가능하며 규모와 복잡성에 관계없이 프로젝트를 구축할 수 있음.
- 멀티 모듈 프로젝트를 지원. 여러 모듈의 동시 개발되는 경우 각각 따로 빌드하고 합치는 과정중에서 에러가 발생하기도 하고, 번거로운 반면 Gradle은 이러한 멀티 모듈 빌드를 지원해주어 실수 및 시간에서 효율성을 가질 수 있음.

### 3.2 Gradle의 핵심 컨셉과 개념

Gradle은 크게 5가지의 개념을 가지고 있다. 핵심 컨셉과 개념은 다음과 같다.

![](https://docs.gradle.org/current/userguide/img/gradle-basic-1.png)_Gradle 전체 구조(출처 : Gradle)_

#### (1) Project

> Project는 응용 프로그램 또는 라이브러리와 같이 빌드할 수 있는 소프트웨어,

- 일종의 <u>"빌드 대상"</u>이라고 볼 수 있다.
  - 단일 프로젝트 빌드에는 <u>루트 프로젝트</u>라고 불리는 단일 프로젝트가 포함
  - 다중 프로젝트 빌드에는 하나의 <u>루트 프로젝트</u>와 여러 <u>하위 프로젝트</u>가 포함.

#### (2) Build Scripts

> 빌드 스크립트는 Gradle에게 Project를 빌드하는 데 필요한 단계를 설명

- 빌드에 필요한 ''<u>빌드 명세서</u>'' 같은 개념이다.
- 각 프로젝트에는 하나 이상의 빌드 스크립트가 포함될 수 있다.

#### (3) Dependency Management

> Dependency Management는 프로젝트에서 필요로 하는 외부 리소스를 선언하고 해결하는 자동화된 기술

- 각 프로젝트에는 일반적으로 Gradle이 빌드 중에 해결할 외부 의존성이 포함

#### (4) Tasks

> Task는 코드를 컴파일하거나 테스트를 실행하는 것과 같은 작업의 기본 단위

- 각 프로젝트에는 <u>빌드 스크립트</u>나 <u>플러그인</u> 내에서 정의된 하나 이상의 Task가 포함

- Task은 클래스 컴파일, JAR 생성, Javadoc 생성, 저장소에 아카이브 게시 등 빌드가 수행하는 **<u>독립적인 작업 단위를 나타낸다.</u>**

다음과 같이 빌드 스크립트에 <u>직접 자신만의 task</u>를 정의할 수 있다. 다음 task는 `print say hello`라는 이름의 task로 콘솔창에 프린트하는 간단한 기능을 가진 task이다. 이때 스크립트에 task를 정의하게 되는데 이때 사용하는 언어가 위에서 언급한 groovy 문법이다.

![Desktop View](/assets/posts/gradle-custom-task.png){: width="972" height="589"}_custom task_

또 다른 예시는 <u>자바 플러그인</u> 내에 속해 있는 Task들중 `jar`이라는 task가 존재하는데, `jar` task는 'main' 소스 디렉토리에 연결된 클래스와 리소스를 기반으로 JAR 파일(Java 배포용 패키지 파일)로 어셈블하는 역할을 한다.

> `jar` — [Jar](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html)  
> _Depends on_: `classes`  
> Assembles the production JAR file, based on the classes and resources attached to the `main` source set.

#### (5) Plugins

> 플러그인은 Gradle의 기능을 확장하고 선택적으로 프로젝트에 작업을 기여하는 데 사용  
> 미리 정의된 Task들의 패키지

- 미리 정의된 Tasks의 묶음 단위이다, 일종의 <u>Task 세트 패키지</u>

<br>

예시로 핵심 플러그인인 **<u>자바 플러그인</u>**을 분석해보면 다음과 같다.

Gradle을 통해 자바 프로젝트를 빌드하거나, spring initial를 통해 스프링 프로젝트를 다운받고 빌드를 수행하면 볼 수 있는 기본 구조인 `scr/main/java`, `src/ test/java`는 어디서 나온걸까? 어떤 규칙으로 만들어진걸까?

정답은 자바 **플러그인이 [링크 : 프로젝트 레이아웃](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_project_layout)**을 잡아주기 때문이다.

```
src
├── main
│   ├── java // 개발 Java 소스
│   └── resources // XML 및 속성 파일과 같은 개발 리소스
└── test
    ├── java // 테스트 Java 소스
    └── resources // 테스트 리소스
```

그리고 Java plugin을 사용하면 프로젝트에 다음과 같은 **task**들을 추가시킨다. (더 많은 Task는 링크 [Java Task](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_tasks) 참조)

| Task            | Description                                                |
| :-------------- | ---------------------------------------------------------- |
| compileJava     | JDK 컴파일러를 사용하여 Java 소스파일을 컴파일.            |
| compileTestJava | JDK 컴파일러를 사용하여 테스트 Java 소스파일을 컴파일.     |
| Jar             | SourceSet에 연결된 클래스를 기반으로 Jar file 생성         |
| javadoc         | Javadoc을 사용하여 Java 소스에 대한 API 문서를 생성합니다. |

Java 플러그인은 아래와 같이 종속성 구성 속성을 사용해 프로젝트에 여러 가지 **종속성** 구성을 추가할 수 있다.

- `implementation`

  - 구현 전용 종속성
  - java 라이브러리 플러그인을 활용하여 Java 플러그인( `java`) 의 기능을 확장할 수 있음.

- `compileOnly`
  - 컴파일 시간에만 종속성이 있으며 런타임에는 사용되지 않음.
- `compileClasspath`연장하다`compileOnly, implementation`
  - 소스를 컴파일할 때 사용되는 컴파일 클래스 경로. Task에서 사용 `compileJava`.
- `annotationProcessor`
  - 컴파일 중에 사용되는 주석 프로세서입니다.
  - Ex) Lombok 사용시

이렇게 플러그인에는 여러 개발, 테스트, 빌드에 편의성을 더해주는 task 들이 미리 정의되어있어, 빌드시에 필요한 편리한 기능들을 사용할 수 있게끔 도와준다. 다음은 Gradle 에서 제공하는 핵심 플러그인 들과 여러 기타 플로그인 레포지토리에 대한 링크들로 한번 참고 해볼만하다.

👉 링크 : [Gradle 핵심 플러그인 소개](https://docs.gradle.org/current/userguide/plugin_reference.html#jvm_languages_and_frameworks)  
👉 링크 : [Gradle 플러그인 레포지토리](https://plugins.gradle.org/)

<br>

![Desktop View](https://docs.gradle.org/current/userguide/img/javaPluginTasks.png){: width="972" height="589"}_Java plugin task 연관관계_

기타로 SourceSet(빌드 유형이라던가, 버전별로 이용한다던가 특정 부분의 코드를 여러 버전으로 관리해야할 필요가 생길 경우, 특정 의존성 패키지의 경로를 지정할때 유용)이나 자바 플러그인의 task들끼리의 호출관계 등 플러그인에 대한 더 자세한 설정이나 내용들이 있으나 이 이상의 내용은 본 글의 범위를 벗어나는 내용들이니 추후에 예시나, 사례등을 통해 소개하도록 하려한다. 중요한 것은 빌드에 필요한 Task들을 미리 작성해 두어 패키지화 해둔 것이 플러그인이라는 사실이다.

<br>

### 3.3 Gradle의 디렉토리 구조

Gradle의 디렉토리 구조는 다음과 같다. 위의 Gradle의 컨셉과 구조와 같이 보며 대응되는 개념을 연결하면서 봐도 좋을 듯하다.

```
project
├── (1) gradle
│   ├── (2) libs.versions.toml
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── (3) gradlew
├── (3) gradlew.bat
├── (4) settings.gradle(.kts)
├── subproject-a
│   ├── (5) build.gradle(.kts)
│   └── (6) src
└── subproject-b
    ├── (5) build.gradle(.kts)
    └── (6) src
```

#### (1) Gradle

JAR file과 [Gradle Wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html#gradle_wrapper) 구성이 포함되어 있는 디렉토리

#### (2) libs.version.toml

`libs.version.toml`파일은 종속성 관리를 위한 Gradle 버전 명세서

#### (3) gradlew

래퍼는 선언된 Gradle 버전을 호출하는 일종의 쉘 스크립트이며 **Gradle 빌드를 실행하는 데 권장되는 방법**. 이는 프로젝트 루트 디렉터리에 `gradlew`또는 `gradle.bat`파일로 있다.

```shell
$ gradlew build     // Linux or OSX
$ gradle.bat build  // Windows
```

#### (4) settings.gradle

다음 빌드 사이클에서도 언급하지만, 설정 파일은 모든 빌드 단계의 진입점이다.

설정 파일의 주요 목적 중 하나는 빌드에 하위 프로젝트를 추가하는 것으로 이를 통해 Gradle은 단일 및 다중 프로젝트 빌드를 지원합니다.

- 단일 프로젝트 빌드의 경우 설정 파일은 선택 사항.
- 다중 프로젝트 빌드의 경우 설정 파일은 필수이며 모든 하위 프로젝트를 선언.

#### (5) build.gradle(.kts)

일반적으로 빌드 스크립트에는 **빌드 구성, 작업 및 플러그인이** 자세히 설명되어 있다.

모든 Gradle 빌드는 최소한 하나의 _빌드 스크립트(`build.gradle(.kts)`_ 로 구성된다. 빌드 파일에는 두 가지 유형의 종속성을 추가할 수 있는데.

1. Gradle과 빌드 스크립트가 의존하는 라이브러리 및/또는 플러그인
2. 프로젝트 소스(예: 소스 코드)가 의존하는 라이브러리

다음은 빌드 스크립트(`build.gradle(.kts)`) 파일의 예시이다.

```groovy
plugins { ----------------------------------------------------------------(1)
    id 'application'
//    id 'java'
//    id 'org.jetbrains.kotlin.jvm' version '1.9.0'
//    id "org.springframework.boot" version "3.2.2"
}

repositories { -----------------------------------------------------------(2)
    mavenCentral()
}


dependencies {------------------------------------------------------------(3)
    testImplementation 'org.jetbrains.kotlin:kotlin-test-junit5'
    testImplementation 'org.junit.jupiter:junit-jupiter-engine:5.9.3'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    implementation 'com.google.guava:guava:32.1.1-jre'
}

application {-------------------------------------------------------------(4)
    mainClass = 'com.example.Main'
}

tasks.register("print say hello") { --------------------------------------(5)
    doLast {
        println("hi everybody this task is working!")
        println("something build build..")
    }
}
```

1. 빌드에 플러그인 적용

   1. Gradle을 확장하는 데 사용됨. 또한 프로젝트 구성을 모듈화하고 재사용하는 데에도 사용됩니다.
   2. Ex) spring boot 플러그인 `id "org.springframework.boot" version "3.2.2"`

2. 종속성을 찾을 수 있는 위치 정의.

   1. 종속성에는 Gradle이 빌드 성공을 위해 다운로드해야 하는 플러그인, 라이브러리 또는 구성 요소의 위치가 포함
   2. Ex) [Maven Central Repository](https://repo.maven.apache.org/maven2/)

3. 종속성 추가

   1. 프로젝트에 작업을 수행하는 데 필요한 여러 가지 종속성을 정의.
   2. 위의 저장소에서 검색.

4. 속성 설정

   1. 내부적으로 프로젝트(`Project()-build.gradle`)에 설정된 플러그인의 설정과 속성을 설정하는 구간인듯 함..

5. Task을 등록하고 구성
   1. 직접 Task를 등록하고 구성 해야하는 경우 정의.

#### (6) src

프로젝트의 소스코드 및 파일이 있는 디렉토리

<br>

### 3.4 빌드 라이프 사이클

Gradle은 빌드전에 Task 그래프를 그리는데, 빌드에 속하는 모든 프로젝트의 Task는 DAG(비순환 방향 그래프)를 이룬다.

![Desktop View](https://docs.gradle.org/current/userguide/img/task-dag-examples.png){: width="972" height="589"}_Task DAG 그래프 (이미지 출처:[Gradle 공식홈페이지](https://docs.gradle.org/current/userguide/build_lifecycle.html))_

그리고 Gradle을 앞서 그려놓은 Task 그래프를 빌드과정에서 이용하는데 빌드 과정은 다음과 같다.

![Desktop View](https://docs.gradle.org/current/userguide/img/author-gradle-1.png){: width="972" height="589"}_Gradle 빌드 단계 (이미지 출처:[Gradle 공식홈페이지](https://docs.gradle.org/current/userguide/build_lifecycle.html))_

첫번째로 **<u>초기화</u>** 단계

- `settings.gradle` 파일 변화를 감지한다.
- `Settings`라는 인스턴스를 생성하고.(`settings.gradle` 파일과 1:1 대응 관계)
- 설정 파일을 평가해서 빌드를 구성하는 프로젝트들이 어떤 것들인지 결정 한 후
- 모든 프로젝트에 대해서 `Project` 라는 인스턴스를 생성한다. (`build.gradle(.kts)` 파일과 1:1 대응관계)

두번째로 **<u>구성</u>** 단계

- 빌드에 포함되는 모든 프로젝트들의 빌드 스크립트(`build.gradle(.kts)`)를 평가한다.
- 요청된 Task에 대한 작업 그래프를 생성

세번째로 **<u>실행</u>** 단계

- Task들 간의 종속성에 따라 실행 순서를 결정
- 선택된 작업들을 실행(병렬 실행 가능)

정리하자면 설정파일을 감지하고 빌드 대상인 **프로젝트들이 무엇**이고, **어떤 설정들이 있는지 체크** 후 각 프로젝트별 **Task들을 작성하고**, Task들 간의 **종속성에 따라 순서를 정해 Task를 실행하는** 방식으로 빌드가 진행된다.

## 4. Gradle를 활용한 Spring 멀티 모듈 빌드 예시

그렇다면 앞서 살펴본 Gradle의 빌드 사이클, 구조, 핵심 개념으로 간단한 멀티 모듈 빌드를 시도해볼 수 있다.

목표하는 타겟 구조는 다음과 같다.

![Desktop View](/assets/posts/gradle-multi-module.png){: width="972" height="589"}

예시 상황은 다음과 같다. 가상의 스프링 프로젝트를 생성하려고 하는데 별도의 2가지 모듈로 분리하여 관리하고자 한다.

1. 첫번째 모듈은 웹 전용 API 모듈로 서비스에 필요한 컨트롤러, 도메인 로직등을 담는 모듈이고
2. 두번째 모듈은 DB 모듈로 서비스에 필요한 데이터베이스와 관련된 내용을 담는 모듈이다.

그리고 API 모듈은 DB 모듈에 의존성을 갖는 구조라고 한다.

**[3.4 빌드 라이프 사이클]**에서 확인한 것처럼 기본적으로 Gradle은 **<u>초기화 단계</u>**에서 루트 프로젝트의 `settings.gradle` 파일 변화를 감지한다. 그리고 해당 `settings.gradle` 파일을 통해 빌드에 포함되는 프로젝트들이 어떤 것이지 확인하고 내부적으로 프로젝트에 대응되는 `Project()` 인스턴스를 생성한다.

이를 위해 루트 프로젝트에서 포함해야할 하위 프로젝트들을 `1.settings.gradle` 에서 다음과 같이 정의할 수 있다.

```groovy
rootProject.name = 'newService'
include('api')
include('db')
```

하위프로젝트는 `include(name)` 으로 지정할 수 있다.

그 다음으로 **<u>구성 단계</u>**에서 빌드에 포한되는 모든 프로젝트들의 빌드 스크립트를 평가한다. 먼저 루트프로젝트에 있는 `2. build.gradle(.kts)` 파일을 통해 하위 프로젝트의 빌드 스크립트를 설정해줄 수 있다. 기본적으로 없어도 내용이 없이(하위 프로젝트에 잘 기재되어 있다면)도 동작하나, 하위 프로젝트들의 공통적인 설정값을 지정해 줄 수도 있다.

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.2.2'
    id 'io.spring.dependency-management' version '1.1.4'
}

allprojects {
    group = 'org.newservice'
    version = '1.0-SNAPSHOT'

    apply plugin: 'java'
    apply plugin: 'org.springframework.boot'
    apply plugin: 'io.spring.dependency-management'

    java {
        sourceCompatibility = '17'
    }

    repositories {
        mavenCentral()
    }

    configurations {
        compileOnly {
            extendsFrom annotationProcessor
        }
    }

    dependencies {
        compileOnly 'org.projectlombok:lombok'
        annotationProcessor 'org.projectlombok:lombok'

        implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    }
}

bootJar {enabled = false}
jar {enabled = false}

project(':api') {
    dependencies {
        implementation(project(':db'))
    }
    bootJar {enabled = true}
    jar {enabled = false}
}

project(':db') {
    bootJar {enabled = false}
    jar {enabled = true}
}

```

- `allProjects` 속성을 통해 루트 하위의 모든 프로젝트들에 대한 속성설정을 추가할 수 있다. `subProjects` 는 지정된 하위 프로젝트들만, `project` 속성은 한개의 프로젝트에 대해서 설정할 수 있다.
- 위의 `2. build.gradle(.kts)` 에서 작성된 내용을 분석해보면 `java`와 `spring-boot` 플러그인을 사용하고, 하위의 모든 패키지들은 java 17, 그리고, `lombok`, `jpa` 라이브러리를 사용한다는 것을 의미한다.
- 하위 프로젝트인 api 모듈에서는 별도의 내부 모듈(db)대한 의존성을 추가하여 api 모듈에서 db모듈에서 작성된 엔티티라던가 작성된 클래스를 가져올 수 있게 설정된 것을 볼 수 있다.

루트 프로젝트의 빌드 스크립트에서 공통 속성을 잘 정리해서 작성하면 하위의 api 모듈과 db 모듈에서는 각자 필요한 의존패키지만 가지고 동시에 빌드될 수 있다.

`3. project:api / build.gradle(.kts)`

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    implementation group: 'org.springdoc', name: 'springdoc-openapi-starter-webmvc-ui', version: '2.2.0'
    implementation group: 'io.jsonwebtoken', name: 'jjwt-api', version: '0.11.5'
    runtimeOnly group: 'io.jsonwebtoken', name: 'jjwt-impl', version: '0.11.5'
    runtimeOnly group: 'io.jsonwebtoken', name: 'jjwt-jackson', version: '0.11.5'
    ...
}

test {
    useJUnitPlatform()
}
```

`4. project:db / build.gradle(.kts)`

```groovy
dependencies {
    runtimeOnly 'com.mysql:mysql-connector-j'
    ...
}

test {
    useJUnitPlatform()
}
```

마지막으로 **<u>실행 단계</u>**를 통해 각각의 프로젝트별 Task들의 종속성을 파악해 실행 순서를 결정하고 빌드가 실행된다.

## 정리

지금까지 쭉 알아본 내용들을 정리하면 다음과 같다.

1. **빌드**는 **소스 코드 파일**을 **실행 가능한 소프트웨어**로 **변환**시키는 과정이다.
2. 빌드툴은 말 그대로 빌드를 도와주는 도구
3. Gradle은 자바, C++, 안드로이드 등 여러 프로그래밍 언어로 작성된 소프트웨어를 빌드하고 관리하기 위한 **오픈 소스 빌드 자동화 도구**
4. Gradle 유연하고 확장 가능하며(플러그인), 전용 빌드언어를 제공해(groovy, kotlin) 이해가 쉽고, 빠르고, 많은 언어와 IDE를 제공, 종속성 관리가 편하다는 장점
5. Gradle은 크게 5가지로 Project, Build Scripts, Dependency Management, Tasks, Plugins 로 이루어져 있다.
6. Task은 클래스 컴파일, JAR 생성, Javadoc 생성, 저장소에 아카이브 게시 등 빌드가 수행하는 **독립적인 작업 단위**를 나타낸다.
7. Plugin은 미리 정의된 Task들의 패키지
8. Gradle의 빌드 과정은 1.초기화 단계 - 2.구성 단계 - 3.실행단계로 나뉘어 진행된다.

이렇게 Gradle이 무엇인지, 또 어떤 개념들로 이루어져 있고, 어떤 기본 동작방식을 가지는지 간단하게 알아봤다. 이전에 Gradle이 뭔지, 빌드가 어떻게 이루어지는지 감이 안잡혔다면, 이번 글을 통해 Gradle의 구조, 빌드 라이프 사이클 등 기타 세부사항도 알아갈 수 있었다.

글을 쓰면서 공식홈페이지도 조사해보고 여러가지 자료를 찾아가면서 이외의 자료들도 찾았으나 이 글에 담기엔 분량와 주제가 걷잘을 수 없이 커질 것 같아 이쯤에서 마무리하려고 한다. 추가적으로 글을 작성하면서 떠올린 주제나, 알아보고 싶은 주제들이 생겨 더 공부해보고 아래 목록 리스트와 같이 해당되는 내용들을 작성해보려고 한다.

- Gradlew란?
- Gradle SourceSet 을 활용한 프로젝트 레이아웃 변경
- Gradle Core API 분석
- Java 컴파일 과정과 Gradle 종속성 관리 설정
- Gradle 설정을 통한 빌드 최적화
