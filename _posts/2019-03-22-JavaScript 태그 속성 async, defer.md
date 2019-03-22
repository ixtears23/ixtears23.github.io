---
layout: javascript-post
title: "JavaScript 태그 속성 async, defer"
date: 2019-03-22
excerpt: ""
tags: [javascript,async,defer]
javascript: true
comments: true
---


# JavaScript 태그 속성 async, defer


JavaScript는 "파서 차단 자원(parser blocking resource)"으로 간주됩니다.
즉, HTML 문서 자체의 구문 분석은 JavaScript에 의해 차단됩니다.
파서가 내부 태그인지 아니면 외부 태그인지에 관계없이 <script> 태그에 도달하면 (외부 태그 인 경우) 반입을 중단하고 실행합니다.  


이 문제는 페이지에 여러 개의 JavaScript 파일을로드하는 경우 문제가 될 수 있습니다.
이는 문서가 실제로 해당 파일에 종속되지 않은 경우에도 처음 paint(html을 그리는)하는 시간을 방해하기 때문입니다.
다행스럽게도 `<script>` 요소에는 **async** 와 **defer** 의 두 가지 특성이 있습니다.
외부 파일을 가져 와서 실행하는 방법과시기를 더 많이 제어 할 수 있습니다.  

### 정상적인 실행

두 속성의 효과를 조사하기 전에 먼저 부재시에 발생하는 것을 살펴 봐야합니다.
기본적으로 위에서 언급했듯이 JavaScript 파일은 인라인되지 않은 경우 가져오고 실행하기 위해 HTML 문서의 구문 분석을 중단합니다.  

예를 들어이 스크립트 요소를 페이지 중간에 위치 시키십시오.  

~~~html

<html>
<head> ... </head>
<body>
    ...
    <script src="script.js">
    ....
</body>
</html>
~~~

문서 파서가 페이지를 통과 할 때 발생하는 현상입니다.  

<figure>
	<a href="https://github.com/ixtears23/img/blob/master/Normal-Execution.png?raw=true?raw=true">
  <img src="https://github.com/ixtears23/img/blob/master/Normal-Execution.png?raw=true?raw=true"></a>
</figure>


HTML 파싱은 스크립트를 가져 와서 실행하기 위해 일시 ​​중지되므로 첫 번째 페인트까지 가져 오는 데 걸리는 시간이 길어집니다.  


### async 속성

async 속성은 브라우저에 스크립트 파일이 비동기 적으로 실행될 수 있음을 나타 내기 위해 사용됩니다.
HTML 구문 분석기는 가져 오기 및 실행하기 위해 스크립트 태그에 도달 한 지점에서 일시 중지 할 필요가 없으며
문서 구문 분석과 병행하여 스크립트를 가져온 후 스크립트가 준비 될 때마다 실행이 가능합니다.  

~~~html
<script async src="script.js">
~~~


이 속성은 외부에 위치한 스크립트 파일에서만 사용할 수 있습니다.
외부 스크립트에이 속성이 있으면 HTML 문서가 여전히 구문 분석되는 동안 파일을 다운로드 할 수 있습니다.
다운로드가 완료되면 스크립트가 실행될 수 있도록 파싱이 일시 중지됩니다.  

<figure>
	<a href="https://github.com/ixtears23/img/blob/master/Async-Execution.png?raw=true?raw=true">
  <img src="https://github.com/ixtears23/img/blob/master/Async-Execution.png?raw=true?raw=true"></a>
</figure>

### defer 속성

defer 속성은 HTML 문서가 완전히 구문 분석되면 스크립트 파일 만 실행하도록 브라우저에 지시합니다.  

~~~html
<script defer src="script.js">
~~~

비동기식으로로드 된 스크립트와 마찬가지로, HTML 문서가 여전히 구문 분석되는 동안 파일을 다운로드 할 수 있습니다.
그러나 문서가 구문 분석을 완료하기 오래 전에 파일이 완전히 다운로드 되더라도 구문 분석이 완료 될 때까지 스크립트는 실행되지 않습니다.  

<figure>
	<a href="https://github.com/ixtears23/img/blob/master/Defer-Execution.png?raw=true?raw=true">
  <img src="https://github.com/ixtears23/img/blob/master/Defer-Execution.png?raw=true?raw=true"></a>
</figure>


### Asynchronous, Deferred or Normal Execution?

그렇다면 비동기, 지연 또는 일반적인 JavaScript 실행은 언제 사용해야합니까? 늘 그렇듯이 상황에 따라 다르며 고려해야 할 몇 가지 질문이 있습니다.  


### <script> 요소는 어디에 있습니까?

`<script>` 요소가 문서 맨 끝에 있지 않으면 스크립트의 비동기 및 지연 실행이 더 중요합니다.
HTML 문서는 첫 번째 여는 `<html>` 요소부터 닫히는 순서로 파싱됩니다.
외부 소스 JavaScript 파일이 닫는 `</body>` 요소 바로 앞에 있으면, 비동기 또는 지연 특성을 사용하는 것이 훨씬 덜 관련됩니다.
파서가 그 시점까지 문서의 대다수를 완성 할 것이기 때문에, 자바 스크립트 파일은 파싱을 많이하지 않아도 됩니다.  


### 스크립트 자체가 포함되어 있습니까?

다른 파일에 종속되지 않거나 종속성 자체가없는 스크립트 파일의 경우 **async** 속성이 특히 유용합니다.
파일이 어느 지점에서 실행되는지 정확히 알지 못하기 때문에 비동기로드가 가장 적합한 옵션입니다.  


### 스크립트가 완전히 구문 분석 된 DOM에 의존합니까?

대부분의 경우 스크립트 파일에는 DOM과의 상호 작용이 필요한 기능이 포함되어 있습니다.
또는 페이지에 포함 된 다른 파일에 대한 종속성이있을 수 있습니다.
이러한 경우 스크립트가 실행되기 전에 DOM을 완전히 구문 분석해야합니다.
일반적으로 이러한 파일은 페이지의 맨 아래에 배치되어 파싱되기 전에 모든 내용을 확인합니다.
그러나 어떤 이유로 든 문제의 파일을 다른 위치에 배치해야하는 상황에서는 지연 **defer** 을 사용할 수 있습니다.  


### 스크립트가 (작은) 종속성입니까?

마지막으로, 스크립트가 비교적 작거나 다른 파일에 의존하는 경우 인라인으로 정의하는 것이 더 유용 할 수 있습니다.
인라인을 사용하면 HTML 문서의 구문 분석이 차단되지만 크기가 작으면 큰 혼란이 없어야합니다.
또한 다른 파일에 의존하는 경우 사소한 차단이 필요할 수 있습니다.  

## 지원 및 최신 브라우저 엔진

**async** 및 **defer** 속성에 대한 지원은 매우 광범위합니다.  



이러한 속성의 동작은 서로 다른 JavaScript 엔진마다 약간 다를 수 있습니다.
예를 들어 V8 (Chromium에서 사용됨)에서는 스크립트 실행을위한 별도의 전용 스레드에서 속성에 관계없이 모든 스크립트를 구문 분석하려고합니다.
이렇게하면 JavaScript 파일의 "파서 차단(parser blocking)"특성을 기본값으로 최소화해야합니다.  

[출처](https://bitsofco.de/async-vs-defer/)
