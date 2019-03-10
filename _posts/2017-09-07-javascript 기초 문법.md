---
layout: javascript-post
title: "javascript 기초 문법"
date: 2017-09-07
excerpt: ""
tags: [javascript]
javascript: true
comments: true
---


- select 자기 자신만 체크(readonly  기능 비슷)  

~~~javascript
$("[name='APC_DIV_CODE']").attr("onFocus", "this.initialSelect = this.selectedIndex");
$("[name='APC_DIV_CODE']").attr("onChange", "this.selectedIndex = this.initialSelect");
~~~

- radio 자기 자신 외에는 체크 못함(readonly 기능 비슷)  

~~~javascript
$("[name='PRUS_SHPE_CODE']:not(:checked)").prop("disabled", true);
~~~

- focusout 포커스 out 될 때  

~~~javascript
$('.hrm_number').focusout(function(){
  $(this).val(addComma($(this).val()));
});
~~~

- focusin 포커스 in 될 때  

~~~javascript
$('.hrm_number').focusin(function(){
  $(this).val(delComma($(this).val()));
});
~~~

- each	selector에 속한 것 모두 실행  

~~~javascript
$('.hrm_number').each(function(){
  $(this).val(addComma($(this).val()));
});
~~~


- 문자열 - 오브젝트(object) 객체 타입
**참고 : serializeObject() 와 유사하지만 serializeObject()는 로직이 더 복잡함**  

~~~javascript
var reqObj = null;
var paramArr = $("#frm1").serializeArray();

if(paramArr){
  reqObj = {};

  $.each(paramArr, function(){
    reqObj[this.name] = this.value;
  });
}
~~~


- 바이트 계산 (개선된 for문 비트 연산) 글자 수 계산
~~~javascript
var stringByteLength = 0;

function getByte(str){

  stringByteLength = (function(s, b, i, c){

    for(b=i=0; c=s.charCodeAt(i++); b+=c>>11?3:c>>7?2:1);
    return b
  })(str);

  console.log(stringByteLength + " Bytes");
}
~~~

**※ 여기서 c>>11 c>>7 비트 연산을 하는 이유는**  

- 다음 표는 코드 포인트 범위에 따른 UTF-8 인코딩 방식을 보여준다.

|코드|포인트|범위|비트 수|인코딩|
|:----|:---:|:----|
|U+0000~U+007F|7|그대로 인코딩|
|U+0080~U+07FF|11|110xxxxx 10xxxxxx|
|U+0800~U+FFFF|16|1110xxxx 10xxxxxx 10xxxxxx|
|U+10000~U+1FFFFF|21|11110xxx 10xxxxxx 10xxxxxx 10xxxxxx |


> 한글 완성형의 코드 포인트 범위는 U+AC00~U+D7AF이므로,
UTF-8 인코딩에서 한글은 무조건 3바이트 인코딩이다


- 오브젝트(object) 객체 -> 문자열

~~~javascript
$.param(reqObj)
~~~

- 승수 제곱 구하기
~~~javascript
Math.pow(2, 5);		//2의 5승
Math.pow(3, 7);		//3의 7승
~~~

- 10진수 -> 2진수  
~~~javascript
var c = "120"
(Number(c)).toString(2);
~~~

- 2진수 -> 10진수  
~~~javascript
parseInt(110001010001, 2);
~~~

- 비트연산
~~~javascript
29323>>2
324234<<6
~~~

- [자바스크립트 테스트](https://jsfiddle.net/jmnote/afzs4u1p/?utm_source=website&utm_medium=embed&utm_campaign=afzs4u1p)  

- 문자열 복사
~~~javascript
"34".repeat(5)		-- 3434343434
~~~

- 문자열 자르기 앞뒤
~~~javascript
"123456789".slice(-1)	-- 9
"123456789".slice(1, 1)	-- 2345678
~~~

- 문자열 배열
~~~javascript
Array(5)  -- 출력하면 ,,,,
Array(5).join("*") -- 출력하면 ****
~~~
