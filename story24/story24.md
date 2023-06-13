# 애플리케이션에서 점검해야 할 대상들

### 패턴과 아키텍처는 잘 구성되어 있는가?
- 너무 많은 패턴을 사용하지 않았는가?
  - 패턴은 반드시 적용해야 하지만 너무 많이 적용하면 오히려 유지보수성이 떨어지고 문제가 발생했을 때 추적하기 어려워진다.
- 데이터를 리턴할 때 TO(혹은 VO) 패턴을 사용하였는가? 아니면 Collection관련 클래스를 사용하였는가?
  - 일반적으로 TO패턴을 주고 받지만 때에 따라서는 Collection 관련 클래스(ex) HashMap)를 사용하기도 한다. 
이러한 패턴을 적용하지 않거나 관련 표준을 정하지 않고 개발을 할 경우 시스템의 응답 시간도 영향이 있고, 유지 보수성도 떨어진다.
- 서비스 로케이터 패턴은 적용이 되어 있는가?
  서비스 로케이터 패턴을 사용하면 애플리케이션에서 필요한 대상을 찾는 룩업작업을 할 때 소요되는 대기 시간을 줄일 수 있다.

### 기본적인 애플리케이션 코딩은 잘 되어 있는가?
- 명명 규칙은 잘 지켰는가?
- 필요한 부분에 예외 처리는 되어 있는가?
  - 예외 처리를 제대로 하지 않으면 사용자는 아무런 응답을 받지 못하고, 시스템을 더 이상 사용하지 않을 수도 있다. 
  문제가 발생했을 때 원인을 밝히기 위해서 예외 처리는 필수다.
- 예외 화면은 지정되어 있는가?
  - 만약 예외 화면을 구성하지 않고 지정하지도 않는다면, 사용자는 서버의 종류가 어떤 것인지 알게 될 것이다.
- 예외 정보를 혹시 e.printStackTrace()로만 처리하고 있지 않은가?
  - e.printStackTrace() 메서드를 호출하면 서버에서 스택 정보를 취합하여야 하기 때문에 서버에 많은 부하가 발생하게 된다.
- System.gc() 메서드가 소스에 포함되어 있지 않은가?
  - 개발할 때는 GC가 '언제 되어야 할지'에 대해서 절대로 신경쓰면 안 된다. System.gc() 메서드를 소스 이곳저곳 작성한다면 서버 성능이 매우 나빠짐을 주의해야한다.
- System.exit() 메서드가 소스에 포함되어 있지 않은가?
  - 이 메서드가 수행되면 WAS의 프로세스가 죽는다. 스레드가 죽는 것이 아니라 프로세스가 죽는다.
- 문자열을 계속 더하도록 코딩하지는 않았는가?
  - JDK 5.0 이상을 사용하면 자동으로 StringBuilder 클래스로 변환을 해주지만, 
  루프를 수행하면서는 컴파일러도 어쩔 수가 없으니 반드시 필요에 따라서 StringBuffer나 StringBuilder 클래스를 선택하여 사용하자.
- StringBuffer나 StringBuilder 클래스도 제대로 사용했는가?   
  - sb.append("aaa"+"bbb"); 라고 사용하는 경우는 없어야 한다. 
- 무한 루프가 작동할 만한 코드는 없는가?
- static을 남발하지 않았는가?
  - 메모리를 아낀다고 static을 남발하다가는 시스템이 심각한 오류를 발생시킬 수 있다.
- 필요한 부분에 synchronized 블록을 사용하였는가?
  - 성능 저하를 발생시킬 수 있다. 
- IO가 계속 발생하도록 개발되어 있지 않은가?
  - 가장 많이 실수하는 부분이 설정 파일을 매번 파일에서 읽도록 개발하는 것이다.
- 필요 없는 로그는 다 제거했는가?
  - 프린트하지도 않을 로그 데이터를 문자열로 만들기 위해서 아까운 메모리를 사용하고, 그 메모리를 청소하기 위해서 GC를 해야 하는 불쌍한 JVM을 생각하자.
- 디버그용 System.out.println은 다 제거했는가?
  - 디버그용 로그는 다 지우자 
  
### 웹 관련 코딩은 잘 되어 있는가?
- JSP의 include는 동적으로 했는가? 아니면 정적으로 했는가?
  - JSP를 동적으로 include하면 서버에도 부하를 줄 뿐만 아니라 응답 시간에도 영향을 준다. 꼭 필요한 부분에만 동적 include를 해야한다.
- 자바 빈즈는 너무 많이 사용하지 않았나?
  - 하나의 TO를 사용해서 처리할 수도 있는 데이터라면, 여러 개의 자바 빈즈를 사용해서 응답시간에 영향을 주지 말자.
- 태그 라이브러리는 적절하게 사용했나?
  - 많은 양의 데이터를 태그 라이브러리로 처리할 경우에는 성능에 많은 영향을 끼친다.
- EJB는 적절하게 사용하였나?
  - EJB 하나하나는 일반 클래스보다 많은 메모리를 점유해야 하며, 서버를 기동할 때에도 많은 시간이 소요된다. 꼭 필요한 EJB만 사용하자
- 이미지 서버를 사용할 수 있는 환경인가?
  - 이미지, 자바스크립트, CSS, 플래시등 정적 컨텐츠가 많고 사용자 요청이 많은 경우 웹 서버 이외에 이미지 서버를 사용할 수 있다.
- 사용 중인 프레임워크는 검증되었는가?
  - 검증되지 않은 프레임워크에서 많은 문제가 발생할 수 있다.

### DB 관련 코딩은 잘 되어 있는가?
- 적절한 JDBC 드라이버를 사용하는가?
  - 가장 최신의 WAS 및 DB 벤더에서 추천하는 문제 없는 JDBC를 사용하자.
- DB Connection, Statement, ResultSet은 잘 닫았는가?
  - 반드시 finall 구문을 사용해서 Connection, Statement, ResultSet을 명시적으로 닫아주자. 그렇지 않으면 사용 가능한 연결이 부족해져 시스템이 사용 불가능한 상태가 된다.
- DB Connection Pool은 잘 사용하고 있는가?
  - 개발자의 역량에 맡기면 여러가지 형태로 디비를 연결하게 되므로 관련되는 표준을 반드시 정하고, DB Connection Pool은 반드시 사용해야 DB의 리소스를 보다 효율적으로 사용할 수 있다.
- 자동 커밋 모드에 대한 고려는 하였는가?
  - 조회성 프로그램도 자동 커밋 여부를 지정하게 되면, 약간의 응답 시간 저하가 발생하게 된다. 반드시 필요하지 않은 경우에는 자동 커밋을 하자.
- ResultSet.last() 메서드(:전체 건수 조회)를 사용하였는가?
  - 데이터의 건수가 많을수록 응답 시간이 느려진다. 쿼리를 두 번 수행하더라도 전체 건수를 가져오기 위한 last()메서드의 사용은 자제해야한다. 
- PreparedStatements를 잘 사용하였는가?
  - Statement를 사용하면 매번 쿼리를 수행할 때마다 SQL 쿼리를 컴파일하게 되어 DB에 많은 부하를 준다.

### 서버의 설정은 잘 되어 있는가?
- 자바 VM 관련 옵션들은 제대로 설정되어 있는가?
  - 64비트 기반의 시스템을 사용하면서 -d64 옵션을 사용하지 않는다면, 그냥 32 비트로 시스템이 운영된다.
  - 클래스 패스는 순차적으로 인식된다. 여러 JAR 파일 중 만약 같은 패키지명에 같은 클래스가 존재할 경우에는 앞에 명시한 클래스 패스에 우선권이 있다.
- 메모리는 몇 MB로 설정해 놓았는가?
  - 설정하지 않으면 서버는 기본 64MB로 시작한다.
- GC 설정은 어떻게 되어 있는가?
  - 혹시 -client로 설정되어 있는지 확인하자.
  - 가능한 성능 테스트를 통해서 시스템과 서버에 맞는 GC옵션을 설정하자.
- 서버가 운영 모드인지 개발 모드인지 확인하였는가?
  - 서버가 개발 모드라면, WAS는 주기적으로 변경된 클래스가 있는지 확인할 것이다. 이 작업은 서버에 많은 부하를 주게 된다.
- WAS의 인스턴스가 몇 개 기동되고 있는가?
  - WAS에서 하나의 인스턴스당 적어도 한 개는 있어야 제대로 된 운영이 가능하다.
  - 서버 및 애플리케이션의 상황에 맞게 성능 테스트를 통해서 시스템의 적절한 인스턴스 개수를 도출해야 한다.
- JSP Precompile 옵션은 지정해 놓았는가?
  - 서버를 기동할 때 JSP를 미리 컴파일하도록 해 놓으면 사용자는 JSP가 수정이 되었는지 여부와 상관없이 일정한 응답 속도를 느낄 것이다.
하지만 이 옵션을 지정해 놓으면 서버가 기동될 때 JSP를 컴파일하기 위한 시간도 많이 소요되기 때문에 상황에 맞게 옵션을 지정하자.
- DB Connection Pool 개수와 스레드 개수는 적절한가?
  - 스레드 개수가 DB Connection Pool의 수보다 절대로 적어서는 안 된다. 보통 10개 정도 더 많이 설정한다.
- 세션 타임아웃 시간은 적절한가?
  - 세션을 더 이상 사용하지 않을 때 세션 정보는 삭제되어야 한다. 만약 세션 타임아웃 시간을 하루 이상의 큰값으로 지정하고, 해당 서버를 재시작하지 않으면 해당 서버는 메모리 릭이 발생하여 더 이상 사용할 수 없는 상황이 될 수 도 있다.
- 검색 서버가 있다면, 검색 서버에 대한 설정 및 성능 테스트를 하였는가?
  - 일반적으로 검색 서버에서 주기적으로 데이터를 모으기 위해서 서버의 리소스를 많이 사용하게 된다.
만약 검색 서버의 세팅을 점검하지 않고, 성능 테스트를 하지 않는다면, 예기치 못한 부분에서 시스템의 리소스를 점유하고 있을지도 모른다.

### 모니터링은 어떻게 하고 있는가?
- 웹 로그는 남기고 있는가?
  - 애플리케이션의 사용량과 자주 사용하는 애플리케이션을 분석하기 위해서, 추후 용량 산정의 기초 자료가 되는 웹 로그는 반드시 남겨서 관리하자.
- verbosegc 옵션은 남기고 있는가?
  - GC가 어떤 형태로 발생하는지를 확인하고자 한다면, 반드시 verbosegc 옵션을 사용하여 로그를 남기자. 
메모리의 크기를 지정하기 위해서나, GC 옵션을 변경하기 위해서라도 verbosegc 옵션은 매우 유용하게 활용될 것이다. 하지만, GC 튜닝을 하지 않을 시스템에 verbosegc 옵션을 남기는 것은 리소스 낭비다.
- 각종 로그 파일에 대한 규칙은 있는가?
  - 로그 파일이 그냥 몇 기가바이트씩 쌓이고 있지 않은가? 일별로 로그를 쌓도록 옵션을 지정하면, 특정 이슈가 발생한 날짜의 데이터를 분석하기에도 좋고 백업을 하기에도 편리하다.
- 서버의 시스템 사용률은 로그로 남기고 있는가?
  - WAS나 DB가 얼마나 사용을 하고 있는지 로그를 남겨야 한다. 
  대부분 사이트에서는 SMS나 APM을 설치하여 서버를 모니터링하고 있겠지만, 그렇지 않은 사이트에서는 서버의 사용량을 유닉스의 vmstat나 sar 명령어를 사용해서 남겨야 시간대별 서버 사용량을 구할 수 있다.
  나중에 서버를 증설할 때에도 이 데이터는 매우 유용하게 사용된다.
- 모니터링 툴은 사용 중인가?
  - WAS를 모니터링하기 위한 툴이 필요하다. 여력이 안된다면 JMX 기술을 사용하여 서버를 모니터링하는 것도 좋은 방법이다.
- 모니터링 툴에 대한 설정은 적절하게 되어 있는가?
  - 모든 메서드에 대해서 프로파일링을 하도록 지정을 하면 대부분의 툴이 성능에 많은 영향을 주게 된다. 꼭 필요한 내용만 모니터링이 되도록 설정을 잘 맞추자.
- 서버가 갑자기 코어 덤프를 발생시키지 않는가?
  - 서버가 지속적으로 코어 덤프를 발생시킨다면 다음 내용을 점검해 보자
    - 10,000건 이상 조회하는 것이 있나 확인해야 한다. 한꺼번에 많은 양의 데이터를 처리하려면 많은 메모리가 필요한데, 이 때 코어덤프가 발생할 수 있다. 데이터를 처리하는데 필요한 메모리가 지정된 메모리 크기보다 클 수도 있기 때문이다.
    - 메모리 릭이 있는지 확인해야 한다. 메모리를 점유하고 해제하지 않는 로직이 있으면 서버를 매일 재기동해도 메모리는 점차적으로 부족해진다. 서버가 기동된 후 힙 덤프를 받아 놓은 뒤, 운영 중과 운영 후에 각각 덤프를 받아서 메모리 사용량의 추이를 확인해야 한다.
- 응답 시간이 너무 느리지 않은가?
  - 이런 경우 상황을 정확히 판단해야 한다. WAS단 이후에서 느린 것인지, 그 이전 단계인 네트워크나 웹 서버에서 느린지 정확히 확인해야 한다. 
  만약 WAS에서 느리다면, 정확히 어느 부분에서 느린지 프로파일링을 해서 확인해 봐야 한다.
  
   