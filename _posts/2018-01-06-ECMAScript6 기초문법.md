---
layout: javascript-post
title: "ECMAScript6 기초문법"
date: 2018-01-06
excerpt: ""
tags: [javascript]
javascript: true
comments: true
---


##### HTML5 에서는 script 태그에 type 속성을 적지 않는 것이 원칙입니다.

~~~html
<!DOCTYPE html>
<html>
<head>
    <script>
        alert('start')
    </script>
</head>
<body>

</body>
</html>
~~~

##### html은 head태그를 먼저 실행하고 body태그를 실행한다.

##### 주석
~~~javascript
//주석문
/*
주석문
주석문
*/
~~~
~~~html
<!DOCTYPE html>
<html>
<head>
    <!-- 주석입니다. -->
    <script>
    </script>
</head>
<body>
    <!-- <h1>주석입니다.</h1> -->
</body>
</html>
~~~

##### 일치 연산자
* === 양쪽 변의 자료형과 값이 일치합니다.  
* !== 양쪽 변의 자료형과 값이 다릅니다.  

### ECMAScript 6
- 템플릿 문자열
- let 키워드
- const 키워드

##### 템플릿 문자열과 표현식
~~~javascript
<sciprt>
    alert(`표현식 273 + 52의 값은 ${52 + 273}입니다...!`);
</sciprt>
~~~
> 변수를 넣어 사용할 수도 있습니다.  
~~~javascript
<sciprt>
    var varialbe = 273;
    alert(`표현식 273 + 52의 값은 ${varialbe}입니다...!`);
</sciprt>
~~~
> 모든 버전의 익스플로러에서는 사용이 불가.  
> IE에서는 안되는 듯....


##### let 키워드와 const 키워드
> ECMAScript5까지는 '식별자에 값을 넣어 활용하기 위한 기능'이 'var키워드를 사용한 변수' 밖에 없었습니다.  
> ECMAScript6부터는 'let 키워드를 사용한 변수', 'const 키워드를 사용한 상수'라는 개념이 추가되었습니다.  


| 키워드 | 구분     | 선언 위치   | 재선언 |
| :---- | :------: | ---------: | -----: |
| var   | 2.3      | 전역 스코프 | 가능   |
| let   | 2.3      | 해당 스코프 | 가능   |
| const | 2.3      | 해당 스코프 | 불가능 |


##### 변수와 상수의 차이
~~~javascript
let variable = 273;
variable = 53; //가능
const variable = 273;
variable = 53; //불가능
~~~
##### 상수 선언
~~~javascript
const constant = 273; //가능
const constant; //불가능
~~~

* example
~~~javascript
<!DOCTYPE html>
<html>
<head>
    <script src="https://code.jquery.com/jquery-1.9.1.min.js"></script>
    <script>
        var startVar = 'start';

        $(document).ready(function () {
            console.log("document loaded"); //window on load 보다 먼저 출력됨.
            var readyVar = 'ready';

            function fn1() {
                console.log(startVar); //'start'
                console.log(readyVar); //'ready'
                console.log(str);   //오류 str변수는 fn2 함수에 var로 선언했지만
                                    //다른 함수에 선언된 var 변수를 찾을 순 없는듯..
            }            

            function fn2() {

                var str = 0;
                console.log(str);

                for (var str = 0; str < 2; str++) {
                    var desc = 'abc';                    
                    let letval = 'desc';
                }

                console.log(str); // str 값은 2

                console.log(desc); //'abc'
                console.log(letval); //오류
            }
            fn1();
            fn2();


        });

        {
            var a = 0;
            let b = 0;
        }

        {
            console.log(a); //0
            console.log(b); //오류
        }

        $(window).on("load", function () {
            console.log("window loaded");   //이미지, iframe이 load 된 뒤에 출력됨.

        });
    </script>
</head>
<body>
</body>
</html>
~~~

#### 짧은 조건문
#### (<불 표현식>) && (<불 표현식이 참일 때 실행할 문장>)
~~~javascript
        function Abc(a, b, c) {

            console.log(a || 'a');
            console.log(b || 'b');
            console.log(c || 'c');
        }

        Abc(5, 6);
~~~
> 결과  
> 5  
> 6  
> c  


##### do While
> do 문장을 무조건 한번은 꼭 실행한다는 것만 기억하자.  
~~~javascript
var value = 0;
do {
    alert(value + '번째 반복문');
    value++;
} while (value < 5)
~~~

### ECMAScript6
* for of 반복문
~~~javascript
        for (var num of [1, 2, 3, 4]) {
            console.log('배열의 해당요소는 ' + num);
        }
~~~
> 역시나 IE에서는 오류..  
