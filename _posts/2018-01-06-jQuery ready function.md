---
layout: javascript-post
title: "jQuery ready function"
date: 2018-01-06
excerpt: ""
tags: [javascript]
javascript: true
comments: true
---

# $(documnet).ready()

 > 문서가 "준비"될 때까지 페이지를 안전하게 조작 할 수 없습니다.  
 > jQuery는 이러한 준비 상태를 감지합니다.  
 > $ (document) .ready ()에 포함 된 코드는 DOM (Document Object Model)이  
 > JavaScript 코드를 실행할 준비가 된 후에 만 실행됩니다.  
 > $ (window) .on ( "load", function () {...}) 안에 포함 된 코드는  
 > DOM뿐만 아니라 전체 페이지 (이미지 또는 iframe)가 준비되면 실행됩니다.  
~~~javascript
// A $( document ).ready() block.
$( document ).ready(function() {
    console.log( "ready!" );
});
~~~

 > 숙련 된 개발자는 때때로 $(document).ready()에 약 $()를 사용합니다.  
 > jQuery에 익숙하지 않은 사람들이 볼 수있는 코드를 작성한다면 긴 형식을 사용하는 것이 가장 좋습니다.  
~~~javascript
// Shorthand for $( document ).ready()
$(function() {
    console.log( "ready!" );
});
~~~

 > 익명 함수를 전달하는 대신 명명 된 함수를 $ (document) .ready ()에 전달할 수도 있습니다.  
~~~javascript
// 익명 함수 대신 명명 된 함수 전달
function readyFn( jQuery ) {
    // Code to run when the document is ready.
}

$( document ).ready( readyFn );
// or:
$( window ).on( "load", readyFn );
~~~


 > 아래 예제는 $ (document) .ready ()와 $ (window) .on ( "load")의 작동 모습입니다.  
 > 코드는 <iframe>에 웹 사이트 URL을로드하고 두 이벤트를 모두 확인합니다.  
~~~javascript
<html>
<head>
    <script src="https://code.jquery.com/jquery-1.9.1.min.js"></script>
    <script>
    $( document ).ready(function() {
        console.log( "document loaded" );
    });

    $( window ).on( "load", function() {
        console.log( "window loaded" );
    });
    </script>
</head>
<body>
    <iframe src="http://techcrunch.com"></iframe>
</body>
</html>
~~~
