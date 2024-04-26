---
title: 랭체인(LangChain Framework)과 핵심 개념(Core Concept)
author: codongmin
date: 2024-03-17T22:16:00
categories:
  - ai
  - LangChain
tags:
  - ai
  - LangChain
  - 글또
image:
  path: /assets/logo/geulttoUdemy.png
  lqip:
  alt: Geultto X Udemy
---

## 들어가며

글또 덕분에 두번째 유데미 강의를 들을 기회를 얻었다. 여러 강의들 중 눈에 바로 밟히는 강의제목이 있었는데 그것은 바로 **"랭체인 - LangChain으로 LLM 기반 애플리케이션 개발하기"** 였다. 이전에 AI 관련 공부를 했었을때는 이미지 처리 관련 분야에 관심이 많아 NLP쪽 분야는 많이 접하지 않았는데, 불과 2, 3년 사이에 ChatGpt라던가 LLaMA 등 대형언어모델들이 등장하면서 현재는 개발직군에 속하지 않는 사람들도 많이 익숙해질정도로 NLP 분야에 **커다란 변화**가 찾아왔고, 앞으로도 물론 **NLP분야의 많은 변화**가 예상된다. 이런 이유로 깊숙하게 모델까지 리서치하지 않더라고 이런 거대 언어 모델들에 대한 접근이 앞으로도 점점 더 많아지고, **진입장벽이 점점 낮아질것 같아**(일반인들이 접근하기에) 이를 활용하는 Framwork를 한번쯤 공부해 보는 것도 **좋은 기회**라 생각되어 신청하게 되었다.

이 글을 보고 강의에서 다루는 **내용**이나 **예제 프로젝트에 관심**이 생기거나, 해당 내용에 **더 깊은 궁금증을 해소**하고 싶다면, 다음 링크를 참조하면 된다.

[Udemy 랭체인 - LangChain으로 LLM 기반 애플리케이션 개발하기](https://www.udemy.com/course/LangChain-korean/?couponCode=KEEPLEARNING)

강의 소개에도 나오지만 이 코스는 완전 **초급자**를 기본으로 하지 않는다. 파이썬 문법을 다루지 않는다.

- 그래서 어느정도 **파이썬 문법에 익숙**하고
- **NLP 분야에 관심 있어 간단한 주변 지식**이 있으면 이해하는 데 훨씬 더 도움될 것이라고 생각한다.
- 또한 LangChain은 애플리케이션 개발 Framwork다. 따라서 **LLM에 대한 지식**은 다루지 않으니, **LLM을 기반으로 어플리케이션**을 개발해보고 싶은 분들에게 **첫 단추를 끼우기**에 적합한 강의로 추천한다.

본 글에서는 강의에서 나오는 실습등을 그대로 옮기지는 않을 것이고, 대신 강의 수강의 배경 지식으로 함께 도움이 될 만한 
1. [**LangChain Framework란 무엇인지**](#what-is-langchain-framework-), 
2. LangChain의 근간이 되는 [**LLM에 대한 설명**](#llm-) 
3. LangChain [**생태계**](#langchain-echosystem)와 [**핵심 모듈**](#langchain-module)
4. 주요 개념인 [**Agent**](#agent-)에 대한 내용을 설명할 예정이다.

그리고 강의에서 나오는 실습을 응용한 예제를 만들어보면서 여러 재미난 시도나 실험등을 후속 글로 작성해 볼 예정이다.
<br>
<br>
## What is LangChain Framework 🦜

앞서서 간략하게 설명했지만, LangChain은 강력한 **LLM(언어모델)기반 어플리케이션 개발** Framwork이다.

LangChain 공식문서에 따르면 다음과 같은 Task를 수행할 수 있는 어플리케이션을 개발을 가능하게 해준다.

- **맥락 파악**
- **추론**

언어모델을 이용하여 몇가지 예시에 대한 맥락을 파악하거나, 제공된 맥락에 따라 답변하는 방법 등 추론을 하는 Task를 수행하는 어플리케이션을 개발할때 LangChain Framwork를 고려해볼 수 있는 것이다.

![Desktop View](/assets/posts/ai/langchain/langchain-do.png){: width="972" height="589"}_LangChain_

LangChain은 기본적으로 **여러 외부 소스**(Google, Wikipedia, 검색 Api 등)을 **LLM**과 연결해줄 수 있다.
이때 여러 기능을 하는 **명령어**들 끼리 엮어서 사용할 수가 있는데, 이것이 LangChain에서 말하는 **Chain**이라는 개념이다. 뒤에서 다룰 LLM, Tool이나, 데이터 전처리와 같은 단계들의 **시퀀스**를 의미한다.

Chain을 사용하면 여러 구성 요소를 결합하여 하나의 **종합적인 애플리케이션**을 만들 수 있다. 여러 개의 체인을 결합하거나, 체인을 **다른 구성요소와 결합**하여 더 **복잡한 체인**을 만들 수도 있다.작업들을 서로 연결하고(chain), 출력 내용을 LLM에게 전달한 후에 출력 내용을 도로 가져오고 다른 작업도 연결해서 **복잡한 애플리케이션**을 구축할 수 있다는 것이 LangChain Framwork의 가장 큰 장점이다.

👉 [LangChain 공식 Docs 확인해보기](https://python.langchain.com/docs/get_started/introduction/)

### LangChain Logo

![Desktop View](/assets/posts/ai/langchain/LangChainLogo.png){: width="972" height="589" .w-50}_LangChain Logo_

LangChain의 로고에 뜬금없이 왠 **앵무새**가 있을까? 궁금증을 떠올려본 독자들을 위해서 준비했다.

이에 대한 유래를 한 [유저의 답변-링크](https://github.com/LangChain-ai/LangChain/issues/8673)에서 찾아볼 수 있었는데, **LLM** 모델은 종종 "**확률론적 앵무새**"라는 단어로 불려오곤 했다고 한다. 왜냐하면 인간의 출력을 따라하는 텍스트를 **생성**할 수는 있지만, 실제로 그 생성된 출력물을 "**이해**"하지 못한다는 것을 **비유**하는 의미로 앵무새를 사용했던 것이었다. 따라서 LangChain Framwork는 보다 **의미있고, 유용한 출력물**을 얻기 위해 앵무새를 함께 체인한다는 의미로 사용되었다고 한다.

로고에 담긴 역사 배경도 알 수 있어 흥미롭지 않은가?


## LLM 🧠

LLM은 "**대형 언어 모델(Large Language Model)**"의 약자로 광범위한 신경망 언어 모델을 의미하는 단어로 사용된다.

- 대량의 텍스트 데이터를 사용하여 학습되며, 자연어 이해(Natural Language Understanding) 및 생성(Natural Language Generation) 작업을 수행하는 데 주로 사용된다.
- 이제는 잘알려진 OpenAI의 GPT, Facebook(meta)의 LLAM 등이 대표적
- 사람으로 따지면 뇌의 언어적인 부분을 담당하는 역할에 해당된다.

LangChain은 기본적으로 이러한 LLM을 사용해서 주어진 Task들을 수행하게 된다. LangChain은 LLM을 직접적으로 개발하거나, 튜닝하는 작업의 목적과는 약간 거리감 있다.

앞으로 사용되는 "**언어모델**"이라는 단어는 **LLM**을 의미하는 단어로 사용하고자 한다.


## LangChain EchoSystem

![Desktop View](https://python.langchain.com/svg/langchain_stack.svg){: width="972" height="589"}_LangChain Framework (출처 : LangChain Offical Homepage) [Link](https://python.langchain.com/svg/langchain_stack.svg)_

LangChain Framwork는 여러 에코시스템으로 이루어져 있는데, 최근에는 개발과 운영, 그리고 배포까지 지원하는 Framwork로 계속 진화중인 것으로 보인다.

- **LangChain 라이브러리** : Python 및 JavaScript 라이브러리. 여러 구성 요소를 체인 및 Agent로 결합하기 위한 기본 런타임, 체인 및 Agent의 기성 구현이 포함
- **LangChain 템플릿** : 다양한 작업을 위해 쉽게 배포할 수 있는 참조 아키텍처 모음
- **LangServe** : LangChain 체인을 REST API 배포를 지원하는 라이브러리
- **LangSmith** : LLM Framwork에 구축된 체인을 디버그, 테스트, 평가 및 모니터링하고 LangChain과 원활하게 통합할 수 있는 플랫폼

## LangChain Module

LangChain 은 다음과 같은 주요 모듈로 이루어져 있다. 각 모듈은 다음과 같다.

**Model I/O** 📃

- **언어모델(LLM)과의 인터페이스**를 담당
- 프롬프트 관리, 프롬프트 최적화, LLM을 사용하기 위한 공통 유틸기능들을 포함한다.

**Retrieval** 📚

- 어플리케이션 별 **데이터 소스의 인터페이스** 담당
- 외부 데이터 소스와 상호작용하여 생성 단계에서 사용할 데이터를 가져옴. 예시로 긴 텍스트의 요약이나, 특정 데이터에대한 질문/답변을 의미

**Agents** 🤖

- 주어진 높은 수준의 지시문에 사용할 **도구를 결정하는 로직 담당**.
- LLM이 어떤 행동을 취해야하는지 결정을 내리고, 행동을 수행하고, 완료될때 까지 과정을 반복하는 것들을 포함한다. LangChain은 Agent을 위한 표준 인터페이스 등을 제공한다.

각 모듈들은 연관된 개념이 나오게 될때 추후 함께 다뤄보고자 한다. 이번 글에서는 주요한 역할을 담당하는 Agent에 대한 내용을 뒤에서 주로 다룬다.


## Agent 🤖

### Problem Context

![Desktop View](/assets/posts/ai/langchain/chatgpt-langchain.png){: width="972" height="589"}_예를 들면 이런 경우..?_

만약 **최신 날짜를 기준으로 정보**를 알고 싶다면 어떻게 해야할까(최신 기사, 최신 동향 등)
언어 모델(LLM)은 특정한 날짜를 기준으로 훈련을 받음, 이러한 종류의 task에는 최신 데이터가 언어 모델(LLM)의 훈련 셋에 포함되어 있지 않았었기 때문에, 다시 말해 **배운적이 없기 때문에 답변이 불가능**하다.

그렇다면 언어 모델(LLM)이 최신 데이터를 필요로 하는 작업을 사용자는 요청할 수 없는걸까?  
답은 그렇지 않다! **Agent를 통해 이러한 작업을 요청 가능**하게 해줄 수 있다.

### Yes I Can! 🫡

Agent는 사용자를 대신해 작업을 수행하고 **언어 모델(LLM)과 소통하는 역할**을 해주는 일종의 Bot 역할을 해줌

그 과정에서 최종작업을 수행하기 위한 필요한 작은 하위 단위의 작업으로 나누고, 이를 정렬하여 작업들의 순서를 정하고 작업을 수행해 나가는데 이는 마치 사용자가 키워드를 얻기 위해 사이트를 검색하거나, 키워드를 바탕으로 데이터베이스에 필요한 데이터를 직접 꺼내서 파악하는 것처럼 **사람의 문제 해결방식**과 비슷하다.

사람처럼 사고할 수 있다는 걸까?

### Core Idea 💡

Agent의 핵심 아이디어는 언어 모델(LLM)을 사용하여 수행할 일련의 작업을 선택하는 것이다.  
Agent는 언어 모델(LLM)을 통해 **수행할 작업과 순서를 결정**하는 **추론 엔진**으로 사용된다.

예를 들어 Agent는 언어 모델(LLM)을 사용하여 다음과 같은 작업들을 수행할 수 있음.

![Desktop View](/assets/posts/ai/langchain/langchain-core-idea.png){: width="972" height="589"}_Agent 동작 예시_

**상황 : 수행하려고 하는 작업이 있고, 이를 수행할 Agent를 정의하는 상황**

- 사용자가 Agent에게 작업 요청
- Agent는 사용자가 원하는 작업을 수행하기 위해 뭘 해야할지 스스로 질문함.
- 이후 산출된 수행 단계의 목록를 직접 정렬. 리스팅함.
  - API 요청을 한다거나, DB를 엑세스 한다거나, 기타 작업을 수행하기 위한 단계들을 처리함.
- 위 단계를 만족할 때까지 반복함.
- 그리고 산출된 작업의 결과를 사용자에게 리턴함.

예를 들어 위의 예시처럼 LangChain에 대한 정보가 없는 LLM은 LangChain에 대해 대답할 수 없지만, Agent는 "LangChain이 뭐지?" 라는 Task에 답하기 위한 LangChain에 대해 검색하고, LangChain에 대한 소개 글을 읽고, 여러 정보를 종합하는 것과 같은 **일련의 하위 작업들을 나열**하고, 이를 **실행**하여 결국에 LangChain이 "LLM(언어모델)기반 어플리케이션 개발 Framwork" 라는 결과를 사용자에게 리턴할 수 있다는 말이다.

실질적으로 "**사람"이 사고 & 행동**하는 것과 같은 방식으로 보인다. 내부에는 어떤 비밀이 숨겨져 있을까?

### How to?

Agent의 내부 동작원리는 다음과 같다. 기본적으로 Agent은 **언어모델**과 **Agent 타입**에서 파생된 **추론엔진**을 사용한다.

Agent에게 작업을 맡기면, Agent는 가장 먼저 과제를 달성하고, 완료하기 위해 **큰 작업**을 어떤 **하위 작업을 세분화하고 수행해야할지 계산**한다.

> 위와 관련해 Agent의 타입이 여러개 존재한다. Agent 타입은 Agent에게 하위 작업의 계산 방식을 의미하는데, Agent 타입에 따라 Agent가 맡은 작업을 달성하기 위한 전략은 하위 작업 계산 방법과, 수행해야하는 과제의 진행 방법에 영향을 미친다. 이러한 타입들은 프롬프트 엔지니어링 기술에서 많이 파생되었다.
> {: .prompt-info }

**스스로 질문에 답하고 하위 작업을 세분화하는 작업**을 언어모델을 통해 충분히 소화할 수 있다.

이는 **Chain-of-Thought(CoT)** 생각의 사슬, 기본적으로 언어모델의 추론을 돕고 추론과정을 나타내는 **프롬프트 엔지니어링 테크닉**을 통해 가능하다. 'Chain-of-Thought Prompting Elicits Reasoning in Large Language Models'[(논문 링크)](https://arxiv.org/pdf/2201.11903.pdf) 라는 논문에서 나온 개념으로 **언어모델의 큰 성능 향상**을 불러왔다.


![Desktop View](/assets/posts/ai/langchain/chain-of-thought.png){: width="972" height="589"}_프롬프트 구성 예시_

간단하게 이 테크닉에 대해 요약하자면, 문제의 원인과 결과에 대해 과정을 풀어서 나아가다보면, 실제 정답에 더 잘 도달할 수 있다는 개념으로 "문제-정답"(도표 좌측 "Standard Prompting") 보다 **"문제-과정-정답"**(도표 우측 <u>"Chain-of-Thought Prompting"</u>) 형태로 프롬프트를 구성하는 것이다.

이를 통해 Agent가 **큰 과제**와 **수행단계의 목록을 직접 정렬**하고 최종 **결과를 리턴**하는 것이 가능하다.

다시 돌아와서 Agent는 수행해야하는 하위 작업들을 나열하는 것 뿐만 아니라
하위 작업을 수행하기 위한 필요한 실질적인 행동(action)을 할 수 있음.

- 예시, 최신데이터 검색 : 검색 API를 호출하거나 DB를 엑세스하는 것

그렇다면 작업을 수행하기 위한 **실질적인 행동**이라는 것은 무엇을 의미할까?

### Tool

이 과정에서 Agent 는 **Tool**이라는 것을 사용함.
Agent에게 팔을 달아준다고 이해하면 직관적이다.

여기서 Tool이라함은 LangChain에서 사용되는 용어로 **Agent가 호출할 수 있는 기능**을 의미함.

- LangChain 프레임 워크와 **third-party 외부서비스와 연결**하는 것을 의미함.
- 예를 들어 특정 DB나 외부 소스에 접근 권한을 얻거나 간단한 API 호출을 하고 싶다고 가정할때, Agent가 툴을 사용해서 이 과정을 사용자 대신 도와줌.

Tool은 Agent가 세상과 **상호작용**하는 데 사용할 수 있는 **인터페이스**로 다음과 같은 인터페이스를 가지고 있다.

1. 도구의 이름
2. 도구가 무엇인지에 대한 설명
3. 도구에 대한 입력에 대한 JSON 스키마
4. 호출하는 함수
5. 도구의 결과를 사용자에게 직접 반환해야 하는지 여부

다음은 Wikipedia 와 연결해주는 툴에 대한 간략한 예시임.

```python
from LangChain_community.tools import WikipediaQueryRun
from LangChain_community.utilities import WikipediaAPIWrapper

api_wrapper = WikipediaAPIWrapper(
		top_k_results=1,
		doc_content_chars_max=100
)
tool = WikipediaQueryRun(
		api_wrapper=api_wrapper
)
```

```python
tool.name
---
>>> 'Wikipedia'
```

```python
tool.description
---
>>> 'A wrapper around Wikipedia. Useful for when you need to answer general questions about people, places, companies, facts, historical events, or other subjects. Input should be a search query.'
```

```python
tool.args
---
>>> {'query': {'title': 'Query', 'type': 'string'}}
```

```python
tool.name
---
>>> 'Wikipedia'
```

```python
tool.return_direct
---
>>> False
```

이러한 기능은 **React (Reasoning + Action)**, 즉 추론과 행동을 결합한 개념의 도입으로 이루어졌고 이것 또한 프롬프트 엔지니어링 테크닉 중 하나이다. (ReAct Agent)

이러한 기능들을 통해 사용자가 요청한 작업이 언어모델의 훈련 셋에 없는 외부 데이터에 접근이 필요한 문제일 경우 Tool을 이용하여 이러한 문제를 해결해 줄 수 있다.

## 마무리

금번에는 LangChain이 어떤 Framwork인지, 그리고 LangChain을 이루는 핵심 요소는 무엇인지, 그리고 LangChain에서 주요 핵심 기능중 하나인 Agent는 어떻게 동작을 하는지, 그리고 LangChain Framwork를 통해 어떤 것을 개발해볼 수 있는지 전반적으로 LangChain Framwork에 대한 이해와, 핵심 개념 & 이론을 살펴보았다.  

1. [**LangChain Framework란 무엇인지**](#what-is-langchain-framework-), 
	- LLM(언어모델)기반 어플리케이션 개발 Framwork
2. LangChain의 근간이 되는 [**LLM**](#llm-) 
	- 대형 언어 모델(Large Language Model)의 약자로 광범위한 신경망 언어 모델
3. LangChain [**생태계**](#langchain-echosystem)와 [**핵심 모듈**](#langchain-module)
	- Model I/O : 언어모델(LLM)과의 인터페이스를 담당
	- Retrieval : 어플리케이션 별 데이터 소스의 인터페이스 담당
	- Agent : 주어진 높은 수준의 지시문에 사용할 도구를 결정하는 로직 담당.
1. 주요 개념 [**Agent**](#agent-) 와 동작원리
	- Agent는 사용자를 대신해 작업을 수행하고 언어 모델(LLM)과 소통하는 역할
	- 과제를 완료하기 위해 큰 작업을 어떤 하위 작업을 세분화하고 수행해야할지 계산
	- Chain-of-Thought(CoT) 생각의 사슬, 프롬프트 엔지니어링 테크닉

이러한 이론들과 개념을 기반으로 여러 실습을 통해 나만의 LLM 애플리케이션을 개발해보고 싶다면 다음 강의를 첫 단추로 수강해보는 것을 추천한다.

[Udemy 랭체인 - LangChain으로 LLM 기반 애플리케이션 개발하기](https://www.udemy.com/course/LangChain-korean/?couponCode=KEEPLEARNING)

무엇보다 한글자막이 잘 제공되어 있고, 내용도 꾸준히 업데이트 되므로, 좋은 컨텐츠를 담고 있다는 느낌을 받았다.

다음 Step으로 다뤄볼 내용들은, **프롬프트**란 무엇인지? 그리고 LangChain에서 어떤 역할을 맡게 되는지, 그리고 **ReactAgent**나 LangChain에서 사용되는 더 깊은 이론들과 예제들을 살펴보고자 한다.