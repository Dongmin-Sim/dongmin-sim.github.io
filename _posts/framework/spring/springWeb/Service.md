---
title: "@Service"
author: codongmin
date: 2024-03-17T22:16:00
categories: 
tags:
  - build
  - ci/cd
image:
  path: /assets/img/thumbnail/.png
  lqip: 
  alt: 이미지 설명
---

## 들어가며


@Service 
비지니스 로직을 담당하는 컴포넌트 
 - 도메인 모델(데이터)와 컨트롤러 사이에 위치.
 - 클래스 네이밍을 반드시 "-Service"로 할 필요는 없음
	 - 관례적으로 즐겨 사용됨.
 - 구현 방식 
	 - 인터페이스 + 클래스 : 정석 
		 - 인터페이스를 요구사항에 따른 기능문서(javadoc)로 작성
		 - 같은 기능을 하는 다양한 구현체를 작성할 니즈가 예상될 때 적합 
	 - 클래스 
		 - 실무에서는 즐겨 사용되기도 하는 방식
		 - 인터페이스를 추가로 작성하는 수고를 덜게됨
		 - 아무래도 컨트롤러 레이어와 결합도는 조금 더 증가됨.

