---
layout: spring-framework-post
title: "@Component 및 추가 스테레오 타입 주석"
date: 2018-03-11
excerpt: "stereotype 주석"
tags: [springframework,streotype,annotation]
spring-framework: true
comments: true
---


# `@Component` 및 추가 스테레오 타입 주석

`@Repository` annotation은 **저장소 (데이터 액세스 개체 또는 DAO라고도 함)의 역할** 또는
**stereotype을 충족하는 모든 클래스의 표식**입니다.  
이 표식의 사용 중에는 예외 변환에 설명 된 예외 자동번역(automatic translation)이 있습니다.  

Spring은 `@Component`, `@Service` 및 `@Controller`와 같은 스테레오 타입 주석을 추가로 제공합니다.  
`@Component`는 **모든 Spring 관리 구성 요소의 일반 스테레오 타입**입니다.  
`@Repository`, `@Service` 및 `@Controller`는 *예를 들어* **지속성 레이어**, **서비스 레이어 및 프리젠 테이션 레이어**와 같이
**보다 구체적인 사용 사례를 위해** `@Component`를 **전문화 한 것**입니다.  

따라서 **`@Component`를 사용하여 구성 요소 클래스에 주석을 달 수 있지만**
`@Repository`, `@Service` 또는 `@Controller`로 주석을 달면
클래스를 도구로 처리하거나 애스펙트와 연결하는 데 더 적합합니다.  

예를 들어, 이러한 스테레오 타입 주석은 **pointcuts에 이상적인 타겟을 만듭니다.**  
`@Repository`, `@Service`, `@Controller`가 스프링 프레임 워크의 향후 릴리스에서 추가적인 의미를 전달할 수도 있습니다.  

따라서 **서비스 계층**에 `@Component` 또는 `@Service`를 사용하도록 선택하는 경우
`@Service`가 **분명히 더 나은 선택**입니다.  

마찬가지로 위에서 설명한 것처럼 `@Repository`는
**이미 지속성계층(persistence layer)에서 자동 예외 변환의 표식으로 지원됩니다.**
