
## Configuration

### status
콘솔에 기록해야하는 내부 Log4j 이벤트의 레벨.  
이 속성의 유효한 값은 "trace", "debug", "info", "warn", "error"및 "fatal"입니다.  
Log4j는 초기화, 롤오버 및 기타 내부 작업에 대한 세부 정보를 상태 로거에 기록합니다.  
status = "trace"설정은 log4j 문제를 해결해야하는 경우 사용할 수있는 첫 번째 도구 중 하나입니다.  
(또는 시스템 등록 정보 log4j2.debug를 설정하면 구성 파일이 발견되기 전에 발생한 내부 로깅을 포함하여 내부 Log4j2 로깅이 콘솔에 인쇄됩니다.)  

!!!! 한마디로 log4j 의 내부 로깅 정보를 확인할 때 사용 합니다.  

상태 로거는 log4j2 구성 요소에 의해 내부적으로 사용됩니다.  
구성에 status = "debug"(또는 "trace")를 설정하면이 내부 로깅이 명령 줄에 출력됩니다.  
어떤 log4j2 플러그인 구성 요소가로드되었는지 (모든 구성 요소는 log4j2 플러그인에 매핑 됨), 발견 된 appenders 및 logger와 같은 세부 사항, 매개 변수 및 결합 방법에 대한 디버그 정보를 인쇄합니다.  

이는 **구성 문제를 해결할 때 유용** 합니다.  

Log4j 2.9부터는 시스템 등록 정보 log4j2.debug (값 필요 없음)를 사용하여 구성 파일이로드되기 전에도 내부 Log4j2 상태 로깅을 활성화 할 수 있습니다. 버전 2.9 이전에는 시스템 속성을 사용하여 동일한 결과를 얻을 수있었습니다.  
`Dorg.apache.logging.log4j.simplelog.StatusLogger.level=TRACE.`  



## Appender
### RollingFile

#### fileName
쓸 파일의 이름.  
파일 또는 상위 디렉토리가 존재하지 않으면 파일이 작성됩니다.  

#### filePattern
archived log file의 파일 이름 패턴.  
패턴의 형식은 사용되는 롤오버 정책에 따라 다릅니다.  
`DefaultRolloPolicy`는 `SimpleDateFormat`과 호환되는 날짜/시간 패턴 및 또는 정수 카운터를 나타내는 %i를 모두 허용합니다.  
이 패턴은 런타임시 interpolation도 지원하므로 모든 조회 (예 : DateLookup)가 패턴에 포함될 수 있습니다.  


#### ignoreExceptions
기본값은 true이며 내부적으로 기록 된 이벤트를 추가하는 동안 예외가 발생하여 무시됩니다.  
false로 설정하면 예외가 발신자에게 전파됩니다.  
이 Appender를 FailoverAppender에 랩핑 할 때 이것을 false로 설정해야합니다.  
