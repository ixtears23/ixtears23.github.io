---
layout: java-logging-post
title: "log4j2 TimeBasedTriggeringPolicy"
date: 2019-05-19
excerpt: ""
tags: [log,log4j2]
java-logging: true
comments: true
---


### TimeBasedTriggeringPolicy

TimeBasedTriggeringPolicy는 날짜/시간 패턴이 더 이상 활성 파일에 적용되지 않으면 롤오버를 발생시킵니다.  
이 정책은 시간 패턴과 변조 부울 속성에 따라 롤오버가 발생하는 빈도를 나타내는 간격 속성을 허용합니다.  

#### TimeBasedTriggeringPolicy Parameters
 - interval  
   - Type: integer  
   - 날짜 패턴에서 가장 구체적인 시간 단위를 기준으로 롤오버가 발생하는 빈도입니다.  
   - 예를 들어 가장 구체적인 항목으로 시간이 포함 된 날짜 패턴을 사용하면 4 시간마다 4 단위로 증가합니다.  
   - 기본값은 1입니다.  


 - modulate
   - Type: boolean  
   - 구간 경계에서 다음 롤오버가 발생하도록 간격을 조정해야하는지 여부를 나타냅니다.  
   - 예를 들어, 항목이 시간 인 경우 현재 시간은 오전 3시이며 간격은 4이고 첫 번째 롤오버는 오전 4시에 발생하고 다음 롤오버는 오전 8시, 정오, 오후 4시에 발생합니다.  

 - maxRandomDelay  
   - Type: integer  
   - 롤오버를 무작위로 지연시키는 데 걸리는 최대 시간 (초)을 나타냅니다.  
   - 이 설정은 여러 응용 프로그램이 동시에 로그 파일을 롤오버하도록 구성된 서버에서 유용하며 시간이 지남에 따라 부하를 분산시킬 수 있습니다.
