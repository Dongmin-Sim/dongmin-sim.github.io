---
title: 
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


## Context

Spring에서 사용되는 "context"의 개념은 애플리케이션의 실행 환경 또는 설정에 대한 정보를 담고 있는 객체이다.

 이러한 context는 스프링 애플리케이션에서 빈(bean)의 생성, 관리, 및 의존성 주입 등을 처리합니다. 주로 스프링 컨테이너(Spring Container)라고도 불립니다. 스프링에서는 다양한 종류의 context가 있으며, 각각의 역할과 특징이 다릅니다.

## Spring Context

### ApplicationContext(애플리케이션 컨텍스트)

가장 일반적으로 사용되는 스프링 컨텍스트입니다. 애플리케이션에 필요한 모든 빈을 로드하고 관리합니다. 클래스패스(classpath)에 있는 XML 파일, Java 파일, 어노테이션 등을 사용하여 설정될 수 있습니다.

### AnnotationConfigApplicationContext (어노테이션 기반 애플리케이션 컨텍스트)

자바 클래스에 기반하여 설정 정보를 제공하는 애플리케이션 컨텍스트입니다. 주로 자바 기반의 설정 클래스를 사용하여 빈을 정의합니다.

### 
3. **XmlWebApplicationContext(XML 웹 애플리케이션 컨텍스트)**: 웹 애플리케이션에서 사용되며, 웹 어플리케이션의 컨텍스트 설정을 XML 파일로 정의합니다.
    
4. **AnnotationConfigWebApplicationContext(어노테이션 기반 웹 애플리케이션 컨텍스트)**: 웹 애플리케이션에서 사용되며, 어노테이션 기반의 설정 정보를 제공하는 웹 애플리케이션 컨텍스트입니다.
    
5. **GenericWebApplicationContext(일반 웹 애플리케이션 컨텍스트)**: 웹 애플리케이션에서 사용되며, 일반적인 웹 애플리케이션 컨텍스트입니다.
    
6. **FileSystemXmlApplicationContext(파일 시스템 기반 XML 애플리케이션 컨텍스트)**: 파일 시스템에서 XML 파일을 사용하여 애플리케이션 컨텍스트를 설정합니다.
    
7. **ClassPathXmlApplicationContext(클래스 패스 기반 XML 애플리케이션 컨텍스트)**: 클래스 패스에서 XML 파일을 사용하여 애플리케이션 컨텍스트를 설정합니다.
    
8. **WebApplicationContext(웹 애플리케이션 컨텍스트)**: 웹 애플리케이션에서 사용되며, 웹 애플리케이션에 필요한 빈을 로드하고 관리합니다.
## Servlet Context 

 - Defines a set of methods that a servlet uses to communicate with its servlet container, for example, to get the MIME * type of a file, dispatch requests, or write to a log file.  
 * There is one context per "web application" per Java Virtual Machine. (A "web application" is a collection of servlets  
 * and content installed under a specific subset of the server's URL namespace such as <code>/catalog</code> and  
 * possibly installed via a <code>.war</code> file.)  
 * In the case of a web application marked "distributed" in its deployment descriptor, there will be one context  
 * instance for each virtual machine. In this situation, the context cannot be used as a location to share global  information (because the information won't be truly global). Use an external resource like a database instead. 
 * The <code>ServletContext</code> object is contained within the {@link ServletConfig} object, which the Web server  
 * provides the servlet when the servlet is initialized. * * @see Servlet#getServletConfig  
 * @see ServletConfig#getServletContext  
 
 ```java
 public interface ServletContext {
 ```
