[Spring classpath prefix 차이](https://stackoverflow.com/questions/3294423/spring-classpath-prefix-difference)  

`classpath*:conf/appContext.xml` 은 단순히 `classpath`에있는 모든 `jar`의 `conf` 폴더에있는 모든 `appContext.xml` 파일을 선택하여 하나의 큰 응용 프로그램 컨텍스트로 결합한다는 것을 의미합니다.  

반대로 `classpath:conf/appContext.xml` 은 클래스 패스에서 처음 발견되는 파일 하나만로드합니다.  


궁금점..

 - `src/test/resources` 경로는 어떻게 찾는지..  
 - `src/test/resources` 에서도 `profile` 설정이 되는지  
 지금 이게 말도 안되는 궁금증인지..  

 
