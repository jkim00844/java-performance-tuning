# Story 2. 내가 만든 프로그램의 속도를 알고 싶다.

자바 기반 시스템에 대하여 응답 속도나 각종 데이터를 측정하는 프로그램은 많다.

분석 툴로는 프로파일링 툴이나 APM 툴 등이 있다.

이 툴을 사용하면 병목 지점을 쉽게 파악할 수 있다.

하지만 실제 운영사이트에서는 예산상의 이유로 분석 툴을 사용하지 않는다.

프로파일링 툴과 APM(Application Performance Monitoring) 툴 

프로파일링 툴

- 소스 레벨 분석을 위한 툴이다.
- 애플리케이션의 세부 응답 시간까지 분석할 수 있다.
- 메모리 사용량을 객체나 클래스, 소스의 라인 단위 까지 분석할 수 있다.
- 가격이 APM 툴에 비해 저렴하다.
- 보통 사용자수 기반으로 가격이 정해진다.
- 자바 기반의 클라이언트 프로그램을 분석 할 수 있다.

ex) 퀘스트 소프트웨어의 JProbe, ej-technologies의 JProfiler, YourKit의 YourKit

기본적으로는 아래 2가지 기능은 제공한다.

1) 응답 시간 프로파일링

메서드 단위나 소스라인 단위 응답 속도를 측정 할 수 있다.

응답시간은 보통 CPU 시간과 대기 시간을 제공한다.

CPU 시간 : CPU를 점유한 시간 

대기 시간 : CPU를 점유하지 않고 대기하는 시간

CPU 시간과 대기 시간을 더하면 실제 소요 시간이 된다.

2) 메모리 프로파일링 

메모리 프로파일링을 하는 주된이유는 잠깐 사용하고 GC의 대상이 되는 부분을 찾거나 메모리 부족 현상이 발생하는 부분을 찾기 위함이다. 클래스 및 메서드 단위의 메모리 사용량이 분석 된다.

APM 툴

- 애플리케이션의 장애 상황에 대한 모니터링 및 문제점 진단이 주 목적이다.
- 서버의 사용자 수나 리소스에 대한 모니터링을 할 수 있다.
- 실시간 모니터링을 위한 툴이다.
- 가격이 프로파일링 툴에 비해 비싸다.
- 보통 CPU 수를 기반으로 가격이 정해진다.
- 자바 기반의 클라이언트 프로그램 분석이 불가능하다.

ex) 미소 정보사의 WebTune, 케이와이즈사의 Pharos, CA Willy의 Introscope, Compuwar의 dynaTrace

프로파일링 툴은 느린 메서드나 클래스를 찾는 것을 목적으로 하지만 

APM 툴의 목적에 따라 용도가 상이하다. 

어떤 APM 툴은 문제점 진단에 강하지만 어떤 APM 툴은 시스템 모니터링에 강하다.

프로파일링 툴이나 APM 툴이건 자동으로 분석해주는 툴은 없다.

사용법을 모르면 무용지물이다. 

툴에서 제공하는 여러 기능을 이해하고 결과를 분석할 수 있는 사람이 사용해야 한다.

툴 업이 더 간단하게 프로그램 속도를 측정할 수는 없을까?

System 클래스

System.currentTimeMillis와 System.nanoTime

System.currentTimeMillis: 현재 시간을 ms로 리턴

System.nanoTime: 현재 시간을 ns로 리턴

예제 

위 메서드를 사용해서 사용메서드를 사용한 시작시간과 종료시간을 구하고 그 차이값인 응답시간을 확인한다.

결과

currentTimeMillis를 사용한 결과가 nanoTime보다 더 느린것으로 나타난다.

느리게 나온 이유는 여러가지아만 클래스가 로딩되면서 성능저하도 발생하고 
JIT Optimizer가 작동하면서 성능 최적하도 되기 때문이라고 보면된다.

JDK 5.0이상이면 nanoTime 메서드를 사용하기 권장한다.

전문 측정 라이브러리를 사용하는 것도 좋은 방법

ex) JMH, Caliper, JUnitPerf, JUnitBench, ConitPef

프로파일링 툴이나 APM 툴은 프로젝트에서 적어도 하나 정도는 사용하는 것이 좋다.
성능이 이슈가 되는 사이트의 경우 필수이다.

---

속성(Properties) : JVM에서 지정된 값

환경(Environment) : 서버(장비)에 지정된 값

System 클래스중 운영중인 코드에서 절대 사용해서는 안되는 메서드

- static void gc() : 자바에서 사용하는 메모리를 명시적으로 해제하도록 GC를 수행하는 메서드
- static void exit() : 현재 수행중인 자바 JVM을 멈춘다.
- static void runFinalization() : Object 객체에 있는 finalize()라는 메서드는 자동으로 호출되는데 가비지 컬렉터가 알아서 해당 객체를 참조할 필요가 없을 때 호출한다. 하지만 이 메서드를 호출하면 참조 해제 작업을 기다리는 모든 객체의 finalize() 메서드를 수동으로 수행해야 한다.