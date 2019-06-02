---
layout: java-basic-post
title: "why user interface in java"
date: 2019-06-01
excerpt: ""
tags: [java,interface]
java-basic: true
comments: true
---


[geeksforgeeks](https://www.geeksforgeeks.org/interfaces-in-java/)


#### 인터페이스 정의  
- 클래스는 둘 이상의 인터페이스를 구현할 수 있습니다.  
- 인터페이스를 구현하는 클래스는 인터페이스의 모든 메소드를 구현해야합니다.  
- 모든 메소드는 public 및 abstract입니다.  
- 그리고 모든 필드는 public, static, final입니다.  



- 모든것을 추상화 하기 위해서
- 인터페이스를 통해 다중 상속을 얻기 위해서
- 느슨한 커플링을 달성하기 위해서  


#### JAVA에서 interface의 필요성은 무엇인가?  

[Quora](https://www.quora.com/What-is-the-need-of-interface-in-Java)  

인터페이스의 주요 용도 중 하나는 두 객체간에 통신 계약을 제공하는 것입니다.  
클래스가 인터페이스를 구현한다는 것을 알고 있다면
해당 클래스는 해당 인터페이스에 선언 된 메소드의 구체적인 구현을 포함한다는 것을 알고 있으므로
이러한 메소드를 안전하게 호출 할 수 있습니다.  
즉, 두 개의 객체는 특정 구현 대신 인터페이스에 정의 된 계약을 기반으로 통신 할 수 있습니다.


#### JAVA interface가 필요한 이유 ?

- java8 에서는 interface에 default 구현부를 가질 수 있다.  

[java67](http://www.java67.com/2014/02/what-is-actual-use-of-interface-in-java.html)  

몇 가지 이유가 있습니다.  
애플리케이션 개발자는 인터페이스가 필요합니다.  
그 중 하나는 인터페이스 레벨에서 다중 상속을 제공하는 Java의 기능입니다.  
미래의 요구 사항을 처리 할 수 있는 유연한 코드를 작성할 수 있습니다.  

하위 클래스에 메소드 만 구현하는 경우 호출자는 인터페이스를 통해 메소드를 호출 할 수 없습니다.  
인터페이스는 클래스 구현을위한 계약을 성취하는 방법입니다.  
그것은 소비자와 생산자 사이의 추상화와 분리 된 디자인을 만드는 기본 도구입니다.  

다중 상속 때문에 인터페이스를 사용하면 한 가지를 다르게 처리 할 수 ​​있습니다.  
예를 들어 클래스는 드로잉 중에 Canvas로 처리되고 이벤트 처리 중에 EventListener로 처리 될 수 있습니다.  
인터페이스가 없으면 클래스가 두 가지 상황에서 서로 다른 두 엔티티처럼 작동하는 것이 불가능합니다.  
다음은 Java에서 인터페이스가 다중 상속을 지원하는 방법을 보여주는 예입니다.  

 "구현보다는 인터페이스 프로그래밍"은 인기있는 객체 지향 설계 원칙 중 하나이며 인터페이스를 사용하여이를 촉진합니다.  
 인터페이스에 작성된 코드는 구현시 작성된 코드보다 훨씬 융통성이 있습니다.


추상화를 달성하는 데 사용됩니다.  
인터페이스를 통해 다중 상속 기능을 지원할 수 있습니다.  
느슨한 커플 링을 달성하는 데 사용할 수 있습니다.  


자바에서 인터페이스를 사용하는 특별한 이점은 다중 상속을 허용한다는 것입니다. 의존성 주입 기술을 사용하여 런타임에 필요한 구현을 주입 할 때 인터페이스의 모든 기능이 활용됩니다. 구체적인 구현 클래스 대신 인터페이스에 대한 참조를 사용하면 인터페이스 참조 사용자가 기본 구현의 변경 사항을 염려 할 필요가 없으므로 파급 효과를 최소화하는 데 도움이됩니다.
예를 들자면, 우리의 애플리케이션은 컨트롤러 레이어가 서비스 레이어, 리포지토리 레이어 및 도메인 레이어의 순서로 계층화 된 아키텍처를 가짐을 고려하십시오. 여기서 제어기 계층은 서비스 계층과 상호 작용하고 서비스 계층은 저장소 계층과 상호 작용합니다. 각 계층은 하위 계층에서 제공하는 서비스를 인식합니다. 이 정보를 Interface를 사용하여 노출하고 구체적인 구현을 숨 깁니다. 시스템에 새 사용자를 등록하는 `UserRegistrationService`가 있다고 가정 해 보겠습니다. 우리의 요구 사항은 관계형 데이터베이스와 xml 파일에 사용자 정보를 저장하는 것입니다. 우리가 인터페이스를 사용하지 않는다면, `UserRegistrationService`는 `saveToXML()`과 `saveToDatabase()` 의 두 함수로 구현 될 수 있습니다.  

~~~java
public class UserRegistrationService {
    public void saveToXML(UserInfo userInfo) {
        // 저장소 계층에 의해 노출 된 서비스를 사용하여 xml에 저장
    }
    public void saveToDatabase(UserInfo userInfo) {
        // 저장소 계층에 의해 노출 된 서비스를 사용하여 db에 저장
    }
}
~~~
이 경우, `UserRegistrationController`는 `UserRegistrationService`에서 이 두 함수의 구체적인 구현을 알고 있어야합니다.  

~~~java
// 추가 기능이 필요할 때 컨트롤러가 복잡 ​​해짐
public class UserRegistrationController {
    // 컨트롤러는 인터페이스가 사용되지 않을 때 구현을 인식해야합니다.  
    UserRegistrationService userRegistrationService = new UserRegistrationService();
    public void processRequest(UserInfo userInfo) {
        this.saveToXml(userInfo);
    }
    private void saveToXml(UserInfo userInfo) {
        userRegistrationService.saveToXml(userInfo);
    }
    private void saveToDatabase(UserInfo userInfo) {
        userRegistrationService.saveToDatabase();
    }
}
~~~

`JSON`으로 정보를 저장하는 추가 기능이 있으면 `Controller` 클래스에서 새 함수 `saveToJson()` 을 추가하고 컨트롤러에서 변경해야합니다.  
이로 인해 수백 개의 컨트롤러와 서비스가 포함 된 대규모 애플리케이션의 **유지 관리에 많은 어려움** 이 따릅니다.  

인터페이스를 사용하면 훨씬 간단 해집니다. `UserRegistrationService`는 다음과 같이 정의됩니다.  

~~~java
public interface UserRegistrationService {
    public void save();
}
~~~  

컨트롤러 레이어는 save 메소드가있는이 인터페이스 만 인식합니다.  
다음과 같은 두 가지 구현이 있다고 가정 해 보겠습니다.  

~~~java
public class UserRegistraionServiceXmlImpl implements UserRegistrationService {
    @Override
    public void save(UserInfo userInfo) {
        // 저장소 계층에 의해 노출 된 서비스를 사용하여 xml에 저장
    }
}
public class UserRegistraionServiceRelDbImpl implements UserRegistrationService {
    @Override
    public void save(UserInfo userInfo) {
        // 저장소 계층에 의해 노출 된 서비스를 사용하여 관계형 데이터베이스에 저장
    }
}
~~~

여기서 우리는 의존성 주입을 사용하여 **런타임** 에서 이 두 구현 중 하나를 선택할 수 있습니다.  
`@Inject` 또는 `@Resource` 주석을 사용하여 구현이 삽입 될 것임을 표시 할 수 있습니다.  
Spring을 사용한다면, XML bean 정의를 사용할 수도 있다.  

~~~java
//서비스 레이어에서 인터페이스를 사용할 때 컨트롤러가 훨씬 간단 해짐
public class UserRegistrationController {
    @Resource(name = "userRegistrationServiceXmlImpl")
    UserRegistrationService userRegistrationService;
    public void processRequest(UserInfo userInfo) {
        userRegistrationService.save(userInfo);
    }
    public void setUserRegistrationService(UserRegistrationService userRegistrationService) {
        this.userRegistrationService = userRegistrationService;
    }
}
~~~

이제 `Json`에 저장 기능을 추가해야 할 때 다른 구현을 추가하고 삽입 할 구현을 선택하기 만하면됩니다.  
(`Spring`과 같은 `IOC{Inversion Of Control}` 컨테이너를 사용하면 이 작업을 원활하게 수행 할 수 있습니다.)  

~~~java
public class UserRegistraionServiceJsonImpl implements UserRegistrationService {
    @Override
    public void save() {
        //save to json using service exposed by Repository layer
    }
}
~~~

이것은 소프트웨어 수정 및 확장 비용을 크게 줄입니다.  
한 레이어의 변경 사항이 다른 레이어에 영향을 미치지 않으므로 다른 레이어에서 즉시 새 기능을 사용할 수 있습니다.  

따라서 인터페이스를 사용하면 응용 프로그램을 확장 및 유지 관리하고 추상화를 활용하며 우수한 소프트웨어 개발 방법을 구현할 수 있습니다.  


----------------------------

## 여기 아래부분이 처음으로 공감이 가는 부분.

[dzone](https://dzone.com/articles/to-interface-or-not-to-interface)  


---
구현이 하나 뿐인 경우 인터페이스가 제공하는 유일한 목적은 코드를 읽는 것을 더 어렵게 만드는 것입니다.

타사 용 라이브러리를 개발하지 않는 한 클래스를 생성하십시오. 나중에 동일한 인터페이스를 가진 두 개의 클래스가 있다는 것을 알게되면 클래스를 인터페이스로 변환하고 구현을 작성하십시오.  

---

인터페이스는 클라이언트에게 행동을 설명하거나 약속하는 데 사용됩니다.  
따라서 의존성 삽입뿐만 아니라 코드 관점에서 "클라이언트"의 코드를 보는 것이 중요합니다.  
해당 클라이언트가 구현 (느슨한 결합)에 대해 알지 못하거나 신경 쓰지 않아야한다면 인터페이스가 도움이됩니다.  

---

내 개인적인 경험으로 모든 비 유틸리티 (정적) 클래스를 인터페이스하는 것이 좋습니다. 이 관행에는 많은 이점이 있습니다.

---

나는 POJO에 대해 잊어 버렸고, 그들은 인터페이스를 가질 필요가 없다.  

나는 많은 시간이 걸리고 이것은 나의 개인적인 경험 일 뿐이 기 때문에 논쟁하고 싶지 않았지만, 당신은 나를 강요했다.  

1. 당신은 항상 당신의 클래스의 공용 API가 얼마나 많은 "숨겨진"기능을 포함 하는지를 알지 못합니다.  
모든 코드를 아래로 스크롤 할 필요는 없습니다.  

주석 : 자바 코드 표준을 따르는 경우, 모든 public 메소드를 맨 위에 놓을 것입니다. 아마도 이것은 인수가 아닙니다.  

2. 클래스를 아주 쉽게 교체 할 수 있으며 한 곳만, 한 명만 커밋 할 수 있습니다. 핫 픽스 (hot-fixes)에 매우 유용합니다.  
논평 : 당신은 분명히 이런 일이 일어나지 않을 것이라고 주장 할 수는 있지만 그러한 마이그레이션의 만료가 있었고 준비된 인터페이스가 많은 도움이되었습니다.  

그런 다음 Shift + Control + R을 사용하여 프로젝트 전체를 이름으로 바꿀 것이라고 주장 할 수 있습니다.  
그래, 이건 선택의 여지가 있지만 내 관점에서는 매우 위험한거야.  

3. 어떤 종류의 구현이 사용되었는지 상관하지 않습니다.

코멘트 : 예, 공용 메소드는 인터페이스와 같은 것이라고 주장 할 수는 있지만, 언젠가는 다른 클래스에서 사용되지 않는 public 메소드를 추가하거나, 개인 / 보호 된 메소드 아래에서 공용 메소드를 이동시킬 것입니다.  
이것은 문제가 될 수 있습니다.  

4. 두 가지 구현이 필요한 경우이 방법을 사용하여 인터페이스를 두 번 구현하십시오.  
v1과 v2가 동시에있는 것처럼 AB 테스트를 수행하십시오.  
또는 Jersey에서 Spring MVC로 이동.

댓글 : 복사하여 붙여 넣을 수 있다고 주장 할 수 있습니다.  
또는 구현간에 차이가있을 수 있습니다.  
그러나 이것은 사실이 아닙니다. 인터페이스를 사용하여 작성하면 좋은 향미 코드를 작성해야합니다.  

5. 두 명의 개발자 / 팀이 결합 클래스를 동시에 코딩 할 수 있습니다.
Comment : 빈 레포 클래스는 나쁜 습관입니다. public 메소드가있는 경우, 구현하거나 NotImplementedException을 던져 야합니다.  
두 번째 부분은 준비가되지 않았지만 인터페이스에 가짜 구현을 사용하는 것이 더 쉽습니다.  

6. 프로젝트를 서브 모듈 / 마이크로 서비스로 쉽게 분리 할 수 ​​있습니다 (예 : RMI 간 또는 RESTful 통신 설정).  
코멘트 : 일어나지 않을까요? 내 친구의 프로젝트에서 한 번 일어났다.  
추신 : 나는 그러한 일을 꼭해야 할 일로 강요하지 않을 것이지만, 할 수있을 때마다 그렇게 할 것입니다.  
PSS : 내 영어로 미안해.

---

하하, 미안, 아나톨리, 나는 당신을 어떤 식 으로든 화나게 할 의도가 없었어요.  
회신 할 시간을 내 주셔서 감사합니다.  
그것은 또한 당신이 또한 당신의 의견과 함께 사물의 다른면을 알고 있다는 것을 보는 것이 즐겁습니다 :)

귀하의 요점을 말하게하겠습니다 :

1. 이것은 의미가 있지만, IDE를 활용할 수도 있습니다.  
IDEA에서 파일 구조 (ctrl + f12 또는 cmd + f12)라고 불렀고 이클립스에서 빠른 개요라고 불렀습니다 :)  

2. 이것에 대해 잘 모르겠습니다 ...  
분명히 인터페이스를 사용할 때 소스를 통해 더 많은 인터페이스가 사용되지만 특정 구현을 어딘가에서 인스턴스화해야합니다.  
이러한 리팩터링을 위한 IDE를 활용하면 단일 구현을위한 인터페이스를 수행 할 것인지 아닌지를 결정할 수 있습니다.  

3. 이 클래스 를 올바르게 이해할 수 있는지 확실하지 않지만 클래스 가 두 개 이상이거나 하나 이상의 클래스에서 단일 메서드 (예 : 같은 이름, 동일한 매개 변수, 동일한 반환 값, 가능하게 다른 구현)가있는 경우에는 추출 인터페이스가 가야 할 분명한 방법! 인터페이스없이 하나의 클래스로 시작하고 나중에 다른 클래스가 튀어 나올 때 - 물론 인터페이스도 추출 할 것입니다!

나는 단 한 번의 구현 만이 예측 가능한 미래에 존재할 것이라는 것이 명백 할 때 인터페이스에 반대한다고 주장한다.

4. Java의 다중 인터페이스는 다중 상속의 형식이 아닙니다. 사실, C ++의 다중 상속 문제를 해결하기 위해 Java에 인터페이스가 도입 된 것 같습니다. :) 이와 별도로, 나는이 것에 대해 전적으로 동의합니다.

5. 결합 수업? 에서 것과 같이, 클래스 A는 클래스 B에 의존하고 클래스 B는 클래스 A에 의존한다. 그것은 스파게티 코드와 디자인 문제 일뿐입니다. :)

6. JAX-RS에서 할 수있는 것처럼 구현과 API를 분리하여 구현을 비밀  로 유지하면서 인터페이스를 공유하는 것은 인터페이스를 훌륭하게 사용한다. 사실, JAX-RS 인터페이스를 기반으로 REST 클라이언트를 빌드 할 때 실제로 인터페이스의 프록시 구현을 작성하게됩니다. 거기에는 여러 구현이 있습니다. 그와 싸우지 마라. 그리고 또한 항상 일어난다. :)

RE : 추신 : 그래, 어쩌면 내가 충분히 명쾌하게 만들지는 못했지만, 한 번만 구현하면 예측 가능한 미래에 인터페이스가 추출되지 않을 것이라고 주장한다. 다음 인터페이스를 추출 할 수 있습니다 :) 그것은 YAGNI 원칙의 단지 실용적인 응용 프로그램입니다 :)

RE.PSS : 나는 네가 이상한 편지로 내 이름을 추측 할 수있는 원어민이 아니다. 아마도 세계에서 가장 많은 사람들 일 것이다.  


---


1. 물론, 옵션. 나는 그것을 사용하지 않는다.

2. 나는 현재 통제가 뒤집어지지 않은 프로젝트는 없을 것이라고 생각한다. 대개 이것은 @Configuration 클래스의 @Bean 메서드 하나 또는 XML의 <bean> 하나입니다.

3. 나는 당신의 수업의 가독성과 그 목적의 범위가 무엇인지를 의미합니다. 물론 SOLID를 따라야하지만, 그러한 상황에서도 항상 명확한 것은 아닙니다. 인터페이스를 사용하면 목적에 초점을 맞추고 자신을 제한 할 수있을뿐 아니라 다른 개발자에게 자신이 만든 것을 이해하게 할 수 있습니다.

4. 기존 코드를 실제로 다시 작성하지 않고도 어떤 이유로 든 구현을 쉽게 추가 할 수 있다는 것을 의미했습니다.

5. 죄송합니다, 결합되지 않았습니다 (물론 나쁜 습관입니다), 나는 클래스에 따라 말하고 싶었습니다. 마찬가지로 : 한 개발자는 프론트 엔드와 서비스가 다른 개발자에 의해 작성되는 동안 서비스를 사용하는 컨트롤러를 작성합니다.



나는 단지 원칙을 따른다 : 그것은 유감스러운 것보다 안전하다.

구현을 한 번 더 추가 할 경우 나보다 더 많은 변경을 수행한다는 데 동의 할 수 있습니다.
