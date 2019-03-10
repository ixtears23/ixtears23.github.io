---
layout: network-post
title: "HAL, HATEOAS"
date: 2019-02-19
excerpt: ""
tags: [network, hal, hateoas]
network: true
comments: true
---

# HAL

*Hypertext Application Language*  

HAL (Hypertext Application Language)은 JSON 또는 XML 코드 내의 외부 리소스에 대한 링크와 같은 하이퍼 미디어를 정의하기위한
인터넷 초안 ( "진행중인 작업") 표준 규칙입니다.  

이 표준은 처음에 JSON과 함께 사용하기 위해 2012년 6월에 처음 제안되었으며 이후 JSON과 XML의 두 가지 변형으로 제공됩니다.  
두 개의 연관된 MIME 유형은 media type : `application/hal+xml` 및 media type : `application/hal+json` 입니다.  

HAL은 사용하기 쉽도록 만들어졌으며 프로젝트 구성 방법에 대한 요구 사항을 부과 할 필요가 없으므로 여러 도메인에서 쉽게 적용 할 수 있습니다.  
HAL을 사용하면 HAL을 사용하는 모든 API에 쉽게 통합 될 수 있는 범용 라이브러리를 만들 수 있습니다.  

HAL을 채택한 API는 오픈 소스 라이브러리의 사용을 단순화하고 JSON 또는 XML을 사용하여 API와 상호 작용할 수있게합니다.  
대안은 독점 형식을 개발해야하는데 이는 개발자에게 또 다른 외국 형식을 사용하는 방법을 배우도록합니다.  

### 협약
HAL은 리소스와 링크의 두 가지 개념을 기반으로 요소를 표현하는 방식으로 구성됩니다.  
리소스는 URI 링크, 내장 리소스, 표준 데이터 (JSON 또는 XML) 및 비 URI 링크로 구성됩니다.  
링크에는 대상 URI뿐 아니라 링크 이름 ('rel'이라고도 함)뿐만 아니라 비추천 및 내용 협상에주의하도록 설계된 선택적 속성이 있습니다.  

### 예

- General Resource  
~~~javascript
{
  "_links": {
    "self": {
      "href": "http://example.com/api/book/hal-cookbook"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
~~~

- Embedded resource    
~~~javascript
{
  "_links": {
    "self": {
      "href": "http://example.com/api/book/hal-cookbook"
    }
  },
  "_embedded": {
    "author": {
      "_links": {
        "self": {
          "href": "http://author-example.com"
        }
      },
      "id": "shahadat",
      "name": "Shahadat Hossain Khan"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
~~~

- Collections  
~~~javascript
{
  "_links": {
    "self": {
      "href": "http://example.com/api/book/hal-cookbook"
    },
    "next": {
      "href": "http://example.com/api/book/hal-case-study"
    },
    "prev": {
      "href": "http://example.com/api/book/json-and-beyond"
    },
    "first": {
      "href": "http://example.com/api/book/catalog"
    },
    "last": {
      "href": "http://example.com/api/book/upcoming-books"
    }
  },
  "_embedded": {
    "author": {
      "_links": {
        "self": {
          "href": "http://author-example.com"
        }
      },
      "id": "shahadat",
      "name": "Shahadat Hossain Khan"
    }
  },
  "id": "hal-cookbook",
  "name": "HAL Cookbook"
}
~~~

[출처: 위키피디아](https://en.wikipedia.org/wiki/Hypertext_Application_Language)  

# HATEOAS  

*Hypermedia As The Engine Of Application State*  

HATEOAS 는 다른 network application architecture와 구별되는 rest application architecture 의 구성요소.  



HATEOAS를 사용하면 **클라이언트**는  
하이퍼 미디어를 통해 정보를 동적으로 제공하는 응용 프로그램 서버가있는 **네트워크 응용 프로그램과 상호 작용**합니다.  

REST 클라이언트는 하이퍼 미디어에 대한 일반적인 이해를 넘어서  
애플리케이션 또는 서버와 상호 작용하는 방법에 대한 **사전 지식이 거의 또는 전혀 필요하지 않습니다.**  

반대로 CORBA의 클라이언트와 서버는 문서 또는 인터페이스 설명 언어 (IDL)를 통해 공유되는 고정 인터페이스를 통해 상호 작용합니다.  

HATEOAS 제약이 클라이언트와 서버를 분리하는 방식은  
서버 기능을 독립적으로 발전시킬 수 있습니다.  

## Deatails

**REST 클라이언트**는 **간단한 고정 URL**을 통해 REST 애플리케이션에 들어간다.  
클라이언트가 취할 모든 향후 조치는 **서버에서 리턴 된 자원 표현** 내에서 발견됩니다.  
이러한 표현에 사용 된 미디어 유형 및 포함될 수있는 링크 관계가 표준화됩니다.  
클라이언트는 표현 내의 링크에서 선택하거나 미디어 유형에 따라 다른 방식으로 표현을 조작하여 응용 프로그램 상태를 전환합니다.  
이러한 방식으로, **RESTful 상호 작용**은 대역 외 정보가 아닌 **하이퍼 미디어에 의해 주도**됩니다.   

예를 들어, 이 **GET 요청**은 XML 표현으로 세부 사항을 요청하는 계정 자원을 가져옵니다.  

~~~http
GET /accounts/12345 HTTP/1.1
Host: bank.example.com
Accept: application/xml
...
~~~

응답은 다음과 같습니다.  
~~~xml
HTTP/1.1 200 OK
Content-Type: application/xml
Content-Length: ...

<?xml version="1.0"?>
<account>
    <account_number>12345</account_number>
    <balance currency="usd">100.00</balance>
    <link rel="deposit" href="https://bank.example.com/accounts/12345/deposit" />
    <link rel="withdraw" href="https://bank.example.com/accounts/12345/withdraw" />
    <link rel="transfer" href="https://bank.example.com/accounts/12345/transfer" />
    <link rel="close" href="https://bank.example.com/accounts/12345/status" />
</account>
~~~

응답에는 가능한 예금, 인출 또는 이체 또는 계정 폐쇄와 같은 가능한 후속 링크가 포함됩니다.  
계정 정보가 나중에 검색되면 계정이 초과 저장됩니다.  

~~~xml
HTTP/1.1 200 OK
Content-Type: application/xml
Content-Length: ...

<?xml version="1.0"?>
<account>
    <account_number>12345</account_number>
    <balance currency="usd">-25.00</balance>
    <link rel="deposit" href="https://bank.example.com/accounts/12345/deposit" />
</account>
~~~

이제는 하나의 링크 만 사용할 수 있습니다. 더 많은 돈을 입금하십시오. 현재 상태에서는 다른 링크를 사용할 수 없습니다.  
따라서 응용 프로그램 상태의 엔진이라는 용어. 가능한 조치는 자원 상태가 다양 할 때마다 다릅니다.  

클라이언트는 서버가 제공하는 모든 미디어 유형 및 통신 메커니즘을 이해할 필요가 없습니다.  
새로운 미디어 유형을 이해하는 기능은 서버가 클라이언트에게 제공하는 "코드 온 디맨드 (code-on-demand)"를 통해 런타임에 얻을 수 있습니다.  



[출처: 위키피디아](https://en.wikipedia.org/wiki/HATEOAS)  


HATEOAS 란 무엇인가?

- 클라이언트는 서버가 동적으로 제공 한 응답을 통해 REST API와 완전히 상호 작용합니다.  
- REST API를 사용하기 위해 문서 또는 대역 외 정보가 필요 하지 않습니다.  
