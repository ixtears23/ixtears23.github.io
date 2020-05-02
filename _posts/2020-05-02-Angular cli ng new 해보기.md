---
layout: post
title: "Angular cli ng new 해보기"
date: 2020-05-02
tags: [angular]
excerpt: ""
etc: true
comments: true
---

# angular 프로젝트 생성하기

대부분의 프레임워크는 cli를 제공한다.

cli는 command line interface로  

윈도우에서는 cmd 에서 입력하는 명령  

리눅스(기타 unix 계열 등 centos 등) shell 에서 입력하는 명령  

으로 생각하고 넘어가도 될 것 같다.  

spring에 spring cli가 있듯이  

npm 에도 npm cli가 있듯이 (npm ls, npm install)  

angular에도 angular cli를 제공한다.  

윈도우 os 기준으로 설명을 한번 해보면  

cmd 창을 연다.

참고:  
angular cli 가 설치되어 있어야 한다.  

그러려면 node js 를 설치하고 npm 을 설치하고 angular cli를 설치하면 된다.  
아주 간단함... 

보통은 users 의 내가 윈도우 로그인 한 id가 폴더명인 path가 default path로 cmd 창이 열리게 된다.  

여기서 ng version 을 하게 되면 angular version을 확인할 수 있다.  

정확히 말하면 angular cli 버전과 angular 버전을 확인할 수 있다.  

정확히 angular version을 표시하게 하려면 어떤 설정파일에 어떤 값이 들어가 있어야  

angualr 버전이 표시되는지 까지는 확인하지 못했다.  

하지만 angular 프로젝트를 한번 만들어 보면..  

프로젝트 이름이 demo인 angular 프로젝트를 만들어 보자.  

cmd 창에서 angular cli를 이용해서 만들어 보자.  

`ng cli demo` 이렇게 입력하면 angular 프로젝트가 생성된다.  


여기서 ng version을 입력하게 되면 angular 설치하기 전과는 다르게..  
angular 버전을 확인할 수 있다.  

내 추측이 맞을지는 모르겠지만. 생성된 angular 프로젝트의  

package.json 파일을 열어 보면.  

cmd에서 열어보고 싶으면 type package.json 을 입력해라.  

그러면 여기서 ng version 에서 확인된 angular의 버전은  

package.json 의 devDependencies 의 @angular/cli : ~7.2.1 과  
일치하는것을 볼 수 있다.  

참고:  
package.json 앞에 붙는
~ 틸드 라고 읽고 ^ 캐럿 이라고 읽고..  
npm cli docs 에 보면 확인할 수 있다.  

1.2.3 버전이라 한다면
1 은 Major 메이저 버전  
2 는 Minor 마이너 버전  
3 은 Patch 패치 버전  이라 한다.  

~틸드 ^캐럿 등 앞에 뭐가 붙냐에 따라  
메이저 버전을 따라가는지 마이너 버전을 따라가는지 이상인지 이하인지 대략 그런 내용  

자세한 건 npm cli docs 를 찾아보고 거기에 있는 semver 라는 키워드 링크를 따라 들어가보면 자세히 알 수 있다.  


---

혹시 아직 cli가 뭔지 모르겠다 라고 한다면..  

이렇게 생각하면 될 것 같다.  

윈도우에는 cmd 명령어를 한번에 실행할 수 있는 batch 파일을 만들 수 있고  

리눅스에서는 shell 명령어를 한번에 실행할 수 있는 .sh 쉘 스크립트 파일을 만들 수 있다.  

위와 같이 ng new demo 를 하게 되면..

ne new 라는 명령이..  

어딘가에 만들어져 있는 배치또는 쉘 파일이겠지? 추측..  

을 실행시켜서  

angular 프로젝트를 생성해 주는 것..  

angular 프로젝트에 필요한 package.json 을 만들고  
angular.json 을 만들고 
tsconfig.json 을 만들고  

기본 적으로 main.ts src 폴더에 app 폴더 안에 컴포넌트 등  

아주 기본적인 구조로 생성하도록 만들어 놨을 것이다.  

new 라는 명령어 하나로..  


간다히 이야기 한다면 처음으로 angular 프로젝트에 접하는 개발자들이  

쉽게 angular 프로젝트를 접할 수 있도록 편리한 기능을 angular 프레임워크를 만든 개발자들이 제공했다고 볼 수 있겠다.  


정리 없이 생각나는데로 풀어서 이야기 하니 부수적인 이야기를 많이 하게 된다...  



angualr 프로젝트를 생성하게 되면  

- e2e 폴더
- node_modules 폴더
- src 폴더
- angular.json
- package.json
- package-lock.json
- README.md
- tsconfig.json
- tslint.json

생성된다.  

여기에 대한 자세한 내용은 다음에 시간이 된다면...  






