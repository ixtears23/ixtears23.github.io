---
layout: post
title: "npm package.json"
date: 2020-02-23
tags: [npm,package.json]
excerpt: ""
etc: true
comments: true
---


[docs](https://docs.npmjs.com/files/package.json)  

## npm 작성방법

JavaScript 객체 리터럴이 아니라 실제 JSON이어야합니다.



## name
패키지를 공개하려는 경우 package.json에서 가장 중요한 것은 `name`과 `version` 필드가 필요하다는 것입니다.  
`name`과 `version`은 완전히 고유 한 것으로 간주되는 식별자를 구성합니다.  
패키지 변경 사항은 `version` 변경 사항과 함께 제공되어야합니다.   
패키지를 게시하지 않으려는 경우 `name` 및 `version` 필드는 선택 사항입니다.  

몇 가지 규칙:  
 - `name`은 214 자 이하 여야합니다. 여기에는 범위가 지정된 패키지의 범위가 포함됩니다.  
 - `name`은 점이나 밑줄로 시작할 수 없습니다.  
 - 새 패키지는 `name`에 대문자가 없어야합니다.
 - `name`은 URL의 일부, 명령 행의 인수 및 폴더 이름이 됩니다. 따라서 `name`에는 URL이 안전하지 않은 문자를 사용할 수 없습니다.

몇 가지 팁 :  
 - 핵심 노드 모듈과 동일한 `name`을 사용하지 마십시오.
 - `name`에 "js"또는 "node"를 넣지 마십시오.  
 package.json 파일을 작성하고 있기 때문에 js라고 가정하고 "engines"필드를 사용하여 엔진을 지정할 수 있습니다. (아래 참조)
 - `name`은 아마도 require()에 인수로 전달 될 것이므로 짧지 만 합리적으로 설명해야합니다.
 - `name`을 붙이기 전에 npm 레지스트리에서 해당 `name`의 항목이 있는지 확인하십시오.  




## version

패키지를 공개하려는 경우 package.json에서 가장 중요한 것은 `name`과 `version` 필드가 필요하다는 것입니다.  
`name`과 `version`은 완전히 고유 한 것으로 간주되는 식별자를 구성합니다.  
패키지 변경 사항은 `version` 변경 사항과 함께 제공되어야합니다.  
패키지를 게시하지 않으려는 경우 `name` 및 `version` 필드는 선택 사항입니다.  

버전은 종속 노드로 npm과 함께 번들로 제공되는 node-semver로 구문 분석 할 수 있어야합니다.  
(`npm install semver`를 사용하여 직접 사용하십시오.)

More on version numbers and ranges at semver.

## scripts

"scripts"속성은 패키지 수명주기 동안 다양한 시간에 실행되는 스크립트 명령이 포함 된 사전입니다.  
핵심은 수명주기 이벤트이며 값은 해당 시점에서 실행하는 명령입니다.  
패키지 스크립트 작성에 대한 자세한 내용은 [npm-scripts](https://docs.npmjs.com/misc/scripts)를 참조하십시오.

## private
package.json에서 "private": true를 설정하면 npm에서 게시를 거부합니다.  
개인 저장소가 실수로 게시되는 것을 방지하는 방법입니다.  
지정된 패키지가 특정 레지스트리 (예 : 내부 레지스트리)에만 공개되도록하려면 아래 설명 된
publishConfig 사전을 사용하여 공개시 레지스트리 구성 매개 변수를 대체하십시오.

## publishConfig
게시시 사용될 구성 값 세트입니다.  
태그, 레지스트리 또는 액세스를 설정하여 지정된 패키지에 "최신"태그가 지정되지 않았거나
전역 공용 레지스트리에 게시되거나 범위가 지정된 모듈이 기본적으로 비공개인지 확인하는 것이 특히 편리합니다.  

모든 구성 값을 재정의 할 수 있지만 “tag”, “registry” 및 “access” 만 게시 목적에 중요합니다.

재정의 할 수있는 구성 옵션 목록을 보려면 [npm-config](https://docs.npmjs.com/misc/config)를 참조하십시오.

## dependencies
`Dependencies` 는 패키지 이름을 버전 범위에 매핑하는 간단한 개체에 지정됩니다.  
버전 범위는 하나 이상의 공백으로 구분 된 설명자가있는 문자열입니다.  
tarball 또는 git URL로 종속성을 식별 할 수도 있습니다.  
`dependencies` Object에 `test harnesses` 또는 `transpilers`를 넣지 마십시오.  
아래 devDependencies를 참조하십시오.  

버전 범위 지정에 대한 자세한 내용은 [semver](https://docs.npmjs.com/misc/semver)를 참조하십시오.  

- **version** `version`과 정확히 일치해야합니다.  
- **>version** `version`보다 커야 합니다.  
- **>=version** `version`보다 크거나 같아야 합니다.  
- **<version** `version`보다 작아야 합니다.  
- **<=version** `version`보다 작거나 같아야 합니다.  
- **~version** `version`과 거의 동일합니다. [semver](https://docs.npmjs.com/misc/semver) 참조  
- **^version** `version`과 호환 가능. [semver](https://docs.npmjs.com/misc/semver) 참조  
- **1.2.x** 1.2.0, 1.2.1 등. 1.3.0은 아닙니다.  
- **http://...** [URLs as Dependencies](https://docs.npmjs.com/files/package.json#urls-as-dependencies) 참조  
- __*__ 모든 `version`과 일치  
- **""** (빈 문자열) __*__ 와 동일  
- **version1 - version2** 왼쪽과 오른쪽은 동일 **>=version1 <=version2**  
- `range1 || range2` range1 또는 range2가 충족되면 전달합니다.  
- **git...** [Git URLs as Dependencies](https://docs.npmjs.com/files/package.json#git-urls-as-dependencies) 참조
- **user/repo** [GitHub URL](https://docs.npmjs.com/files/package.json#github-urls) 참조  
- **tag** 태그로 태그되고 게시 된 특정 버전. [npm-dist-tag](https://docs.npmjs.com/cli/dist-tag) 참조
- **path/path/path** [Local Paths](https://docs.npmjs.com/files/package.json#local-paths) 참조  

예를 들어 다음은 모두 유효합니다.  
~~~javascript
{ "dependencies" :
  { "foo" : "1.0.0 - 2.9999.9999"
  , "bar" : ">=1.0.2 <2.1.2"
  , "baz" : ">1.0.2 <=2.3.4"
  , "boo" : "2.0.1"
  , "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0"
  , "asd" : "http://asdf.com/asdf.tar.gz"
  , "til" : "~1.2"
  , "elf" : "~1.2.3"
  , "two" : "2.x"
  , "thr" : "3.3.x"
  , "lat" : "latest"
  , "dyl" : "file:../dyl"
  }
}
~~~




## devDependencies
프로그램에서 모듈을 다운로드하여 사용하려는 경우 사용중인 외부 테스트 또는 문서 프레임 워크를 다운로드하거나 빌드하지 않아도됩니다.  

이 경우 이러한 추가 항목을 devDependencies 객체에 매핑하는 것이 가장 좋습니다.  

이러한 것들은 패키지의 루트에서 `npm link` 또는 `npm install`을 수행 할 때 설치되며
다른 npm 구성 매개 변수처럼 관리 할 수 ​​있습니다.  
주제에 대한 자세한 내용은 [npm-config](https://docs.npmjs.com/misc/config)를 참조하십시오.  

CoffeeScript 또는 다른 언어를 JavaScript로 컴파일하는 것과 같이 플랫폼별로 고유하지 않은 빌드 단계의 경우,
**prepare** 스크립트를 사용하여 이를 수행하고 필요한 패키지를 devDependency로 만듭니다.  

예:  

~~~
{ "name": "ethopia-waza",
  "description": "a delightfully fruity coffee varietal",
  "version": "1.2.3",
  "devDependencies": {
    "coffee-script": "~1.6.3"
  },
  "scripts": {
    "prepare": "coffee -o lib/ -c src/waza.coffee"
  },
  "main": "lib/waza.js"
}
~~~

**prepare** 스크립트는 게시하기 전에 실행되므로 사용자가 직접 컴파일하지 않고도 기능을 사용할 수 있습니다.
 개발 모드 (예 : 로컬에서 `npm install` 실행)에서는 이 스크립트도 실행되므로 쉽게 테스트 할 수 있습니다.  



## URLs as Dependencies
버전 범위 대신 tarball URL을 지정할 수 있습니다.  
이 tarball은 설치시 패키지에 로컬로 다운로드되어 설치됩니다.  

## Git URLs as Dependencies

Git URL은 다음과 같은 형식입니다.  

~~~
<protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]
~~~

`<protocol>` 은 **git**, **git+ssh**, **git+http**, **git+https** 또는 **git+** 파일 중 하나입니다.  


`#<commit-ish>`가 제공되면 해당 커밋을 정확하게 복제하는 데 사용됩니다.  
commit-ish의 형식이 `#semver:<semver>`이면 `<semver>`는 유효한 semver 범위 또는 정확한 버전 일 수 있으며
npm은 원격 저장소에서 해당 범위와 일치하는 태그 또는 참조를 찾습니다.  
레지스트리 의존성. `#<commit-ish>` 또는 `#semver:<semver>`를 지정하지 않으면 `master`가 사용됩니다.  

예:  
~~~
git+ssh://git@github.com:npm/cli.git#v1.0.27
git+ssh://git@github.com:npm/cli#semver:^5.0
git+https://isaacs@github.com/npm/cli.git
git://github.com/npm/cli.git#v1.0.27
~~~
