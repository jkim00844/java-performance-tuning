## HotSpot VM은 어떻게 구성되어 있을까?

자바를 만든 Sun에서는 자바의 성능을 개선하기 위해서 Just In Time(JIT) 컴파일러를 만들었고, 이름을 HotSpot으로 지었다.

여기서 JIT 컴파일러는 프로그램의 성능에 영향을 주는 지점에 대해서 지속적으로 분석하여 부하를 최소화하고, 높은 성능을 내기 위한 최적화의 대상이 된다. 이 HotSpot은 자바 1.3 버전부터 기본 VM으로 사용되어 왔기 때문에, 지금 운영되고 있는 대부분의 시스템들은 모두 HotSpot 기반의 VM이라고 생각하면 된다. HotSpot VM은 세 가지 주요 컴포넌트로 되어 있다.

- VM(Virtual Machine) 런타임
- JIT(Just In Time) 컴파일러
- 메모리 관리자

<br>

## JIT Optimizer 라는게 도대체 뭘까?

자바는 javac라는 컴파일러를 사용한다. 이 컴파일러는 소스코드를 바이트코드로 된 class라는 파일로 변환해준다. 그렇기 때문에 JVM은 항상 바이트코드로 시작하며, 동적으로 기계에 의존적인 코드로 변환한다.

JIT는 각각의 메서드를 컴파일할 만큼 시간적 여유가 많지않다. 그래서 모든 코드는 초기에 인터프리터에 의해서 컴파일되고, 해당 코드가 충분히 많이 사용될 경우에 JIT가 컴파일할 대상이 된다.

HotSpot VM에서 이 작업은 각 함수에 있는 카운터를 통해서 통제되며, 함수에는 두 개의 카운터가 존재한다.

- 수행 카운터(Invocation counter): 함수를 시작할 때마다 증가
- 백에지 카운터(backedge counter): 높은 바이트 코드 인덱스에서 낮은 인덱스로 컨트롤 흐름이 변경될 때마다 증가

이 카운터들이 인터프리터에 의해서 증가될 때마다 그 값들이 한계치에 도달했는지를 확인하고, 도달했을 경우 인터프리터는 컴파일을 요청한다

<br>

## JRockit의 JIT 컴파일 및 최적화 절차

> Oracle JDK와 OpenJDK는 HotSpot VM을 기반으로 하고, IBM JVM과 JRockit은 각각 독자적인 JVM 구현을 사용한다.

JVM은 각 OS에서 작동할 수 있도록 자바 코드를 입력 값(정확하게는 바이트코드)으로 받아 각종 변환을 거친 후 해당 칩의 아키텍처에서 잘 돌아가는 기계어 코드로 변환되어 수행되는 구조로 되어 있다.

![image](https://github.com/rlatmd0829/java-performance-tuning/assets/70622731/36b6311b-335d-4b48-baeb-50d5cd2b0f96)

JRockit은 이와 같이 최적화 단계를 거치도록 되어 있으며, 각각의 단계는 다음의 작업을 수행한다.

### JRockit runs JIT compilation

자바 애플리케이션을 실행하면 기본적으로는 1번 단계인 JIT 컴파일을 거친 후 실행이 된다. 이 단계를 거친 후 함수가 수행되면, 그 다음부터는 컴파일된 코드를 호출하기 때문에 처리 성능이 빨라진다.
JIT를 사용하면 시작할 때의 성능은 느리겠지만, 지속적으로 수행할 때는 더 빠른 처리가 가능하다. 따라서 모든 함수를 컴파일하고 최적화하는 작업은 JVM 시작 시간을 느리게 만들기 때문에 시작할 때는 모든 함수를 최적화하지는 않는다.

### JRockit monitors threads
JRockit에는 ‘sampler thread’라는 스레드가 존재하며 주기적으로 애플리케이션의 스레드를 점검한다. 이 스레드는 어떤 스레드가 동작 중인지 여부와 수행 내역을 관리한다. 이 정보들을 통해서 어떤 함수가 많이 사용되는지를 확인하여 최적화 대상을 찾는다.

### JRockit JVM performs optimization
‘sampler thread’가 식별한 대상을 최적화한다. 이 작업은 백그라운드에서 진행되며 수행중인 애플리케이션에 영향을 주지는 않는다.


## IBM JVM의 JIT 컴파일 및 최적화 절차

### 인라이닝

호출된 메서드가 단순할 경우 그 내용이 호출한 메서드의 코드에 포함해 버린다.

### 지역 최적화

작은 단위의 코드를 분석하고 개선하는 작업을 수행한다.

### 조건 구문 최적화

메서드 내의 조건 구문을 최적화 하고, 효율성을 위해서 코드의 수행 경로를 변경한다.

### 글로벌 최적화

메서드 전체를 최적화하고, 효율성을 위해서 코드의 수행 경로를 변경한다.

### 네이티브 코드 최적화

아키텍처에 따라서 최적화를 다르게 처리한다.

<br>

## 클래스 로딩 절차도 알고싶어요?

1. 주어진 클래스의 이름으로 클래스 패스에 있는 바이너리로 된 자바 클래스를 찾는다.
2. 자바 클래스를 정의한다.
3. 해당 클래스를 나타내는 java.lang 패키지의 Class 클래스의 객체를 생성한다.
4. 링크 작업이 수행된다. 이 단계에서 static 필드를 생성 및 초기화하고, 메서드 테이블을 할당한다.
5. 클래스의 초기화가 진행되며, 클래스의 static 블록과 static 필드가 가장 먼저 초기화 된다.

> loading -> linking -> Initializing 로 구성되어있다.
