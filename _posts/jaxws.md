

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:soap="http://cxf.apache.org/bindings/soap"
	xmlns:web-services="http://www.springframework.org/schema/web-services" xmlns:jaxws="http://cxf.apache.org/jaxws"
	xmlns:jaxrs="http://cxf.apache.org/jaxrs" xmlns:context="http://www.springframework.org/schema/context" xmlns:core="http://cxf.apache.org/core"
	xmlns:addressing="http://cxf.apache.org/ws/addressing"
	xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://cxf.apache.org/core http://cxf.apache.org/schemas/core.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://cxf.apache.org/bindings/soap http://cxf.apache.org/schemas/configuration/soap.xsd
		http://cxf.apache.org/jaxws http://cxf.apache.org/schemas/jaxws.xsd
		http://schemas.xmlsoap.org/ws/2004/08/addressing http://schemas.xmlsoap.org/ws/2004/08/addressing
		http://cxf.apache.org/jaxrs http://cxf.apache.org/schemas/jaxrs.xsd
		http://cxf.apache.org/ws/addressing http://cxf.apache.org/schemas/ws/addressing.xsd
		http://www.springframework.org/schema/web-services http://www.springframework.org/schema/web-services/web-services.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">


	<core:bus>
		<core:features>
			<core:logging />
		</core:features>
	</core:bus>

  <!--
    endpoint:JAX-WS 서버를 구성합니다. 이 요소는 org.apache.cxf.jaxws.EndpointImpl 오브젝트와 연관됩니다.
      address: 엔드 포인트의 HTTP 주소를 지정합니다. 이 값은 서비스 계약에 지정된 값을 대체합니다.
  -->
	<jaxws:endpoint id="ws.kmaMngtContainer" address="/ws.kmaService" name="kmaService">

		<!-- inInterceptors:들어오는 요청을 처리 할 인터셉터 목록을 지정합니다. -->
		<jaxws:inInterceptors>
			<ref bean="참조 빈 이름"/>
		</jaxws:inInterceptors>

    <!-- implementor:서비스를 구현하는 Bean을 구성합니다. 이 자식이 사용되면 implementor 특성을 사용하지 않아야합니다. -->
		<jaxws:implementor>
			<ref bean="참조 bean 이름" />
		</jaxws:implementor>

		<!-- outInterceptors:나가는 응답을 처리 할 인터셉터 목록을 지정합니다. -->
		<jaxws:outInterceptors>
			<bean class="패키지명.클래스명">
			</bean>
		</jaxws:outInterceptors>

	</jaxws:endpoint>


</beans>
~~~
