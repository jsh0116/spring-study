# Reactive Programming

## Reactive Programming이란?
> In computing, reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change. With this paradigm it is possible to express static (e.g., arrays) or dynamic (e.g., event emitters) data streams with ease, and also communicate that an inferred dependency within the associated execution model exists, which facilitates the automatic propagation of the changed data flow.<br/>
[Wikipedia에서의 리액티브 프로그래밍에 대한 설명](https://en.wikipedia.org/wiki/Reactive_programming)<br/>

리액티브 프로그래밍은 데이터의 흐름과 변화의 전파에 중점을 둔 프로그래밍 패러다임이다.
작성한 코드의 순서대로 진행되는 기존의 명령형 프로그래밍과는 다르게 데이터의 흐름을 먼저 정의하면 데이터의 변화 혹은 작업의 종료에 따라 반응하여 진행되는 프로그래밍을 의미한다.

## Reactive Programming의 목적
* resource를 효율적으로 사용하기 위해 Server에서 Reactive Programming을 사용하기 시작했다.
* 이전에는 Multi-Thread로써 병렬처리를 했지만, Thread의 확장으론 CPU와 Memory의 제한이 있기에 Async && Non-Blocking이라는 새로운 프로그래밍 모델이 제안됐다.
* Non-Blocking은 일반적인 Thread를 통한 비동기 작업과는 다르게 Thread를 점유하지 않고 작업을 수행하여 Thread 내에서 동시에 많은 작업을 수행할 수 있다.
* 위와 같은 작업에 있어, 너무 많은 트래픽이 몰릴 경우 문제가 발생하거나 성능이 제대로 나오지 않을 수 있기에 Back-pressure을 통해 요청 갯수의 제한이 필요하다.

### Observer Pattern
* 이벤트 처리를 기반으로 시스템을 구현할 때 필수적이다.
* [GoF 디자인 패턴(Gang of Four)](https://en.wikipedia.org/wiki/Design_Patterns) 중 하나이다.
* 관찰자(Observer)라고 불리는 자손의 리스트를 가지고 있는 주제(subject) 를 필요로 한다.
* MVC(Model-View-Controller) Pattern의 중요한 부분이다.

### Reactive Framework RxJava
* Reactive Extensions(ReactiveX)의 자바 구현체이다.
* 동기식 또는 비동기식 stream과 관계없이 명령형 언어를 이용해 Data Stream을 조작할 수 있는 도구이다.
* Observer Pattern, Iterator) Pattern 및 Functional Programming의 조합이다.

#### Observer + Iterator = Reactive Stream
```java
  // Observer Pattern
  
  public interface Observer<T> {
    void notify(T event);
  }
  
  public interface Subject<T> {
    void registerObserver(Observer<T> observer);
    void unregisterObserver(Observer<T> observer);
    void notifyObservers(T event);
  }
```
* 무한한 Data Stream에 대해서는 매력적이다.
* Data Stream의 끝을 알리는 기능이 필요하다.
* Consumer가 준비하기 전에 Producer가 event를 생성하는 것은 바람직하지 않다.
* 동기식에서 위 문제를 대비한 Iterator Pattern이 존재한다.
```java
  // Iterator Pattern
  
  public interface Iterator<T> {
    T next();
    boolean hasNext();
  }
```
* 하나씩 항목을 검색하기 위해 Iterator는 next() method를 제공한다.
* hasNext() 호출의 결과로 false 값을 반환해 sequence의 끝을 알려줄 수 있다.
```java
  // Observer Pattern에 의한 비동기 실행 + Iterator Pattern => RxJava 기본 개념인Observer Interface

  public interface RxObserver<T> {
    void onNext(T next);
    void onComplete();
    void onError(Exception e);
  }
```
* RxObserver는 Iterator과 유사하지만 next 메서드 대신 onNext() Callback에 의해 RxObserver에 새로운 값이 통지된다.
* onComplete() method를 통해 stream의 끝을 알린다.
* Producer에서 오류 전파 매커니즘으로 Callback onError를 추가한다.
* Reactive Stream의 모든 Component 사이에 데이터가 흐르는 방법을 정의한다.
* Observer Interface는 모든 곳에서 발견된다.
* Observable Class
  * Observer Pattern의 주체(Subject)
  * Observable은 event를 발생시킬 때 Event Source 역할을 수행한다.
  * Reactive Stream을 초기화하는 수십 가지의 Factory Method와 수백 가지의 Stream 변환 Method가 있다.
* Subscriber Abstract Class
  * Observer Interface를 구현하고 event를 consume한다.
  * 실제 Subscriber 클래스 기본 구현체로도 사용된다.
  * Runtime 상에서 Observable과 Subscriber 간의 관계는 메시지 구독 상태를 확인하고 필요한 경우 이를 취소할 수도 있는 구독에 의해 제어된다.

추가 학습 권장: [RxJava](https://github.com/ReactiveX/RxJava)

## [Reactive System이란](https://umbum.dev/1032)?
* 분산 시스템으로 구현되는 모든 비즈니스의 핵심 가치는 응답성(Responsive)
* 높은 응답성을 확보한다는 것은 탄력성(Resilient)과 유연성(Elastic)을 가지고 있다는 의미이다.
* 응답성, 탄력성, 유연성을 확보하기 위해 `메시지 기반 통신` 을 사용
* 메시지 기반(Message-Driven) 통신
  * 기존 HTTP 통신의 경우 호출이 일어날 때마다 외부 서비스에 대한 추가 HTTP 요청을 발생시키고 차례로 다음 단계가 실행됐다.
  * 처리 시간의 일부만 효과적인 CPU 사용을 위해 할당되고, 나머지 시간 동안 Thread는 I/O에 의해 차단되며 다른 요청을 처리할 수 없다.
  * Java에는 병렬 처리를 위해 추가 Thread를 할당할 수 있는 Thread Pool이 존재한다.
  * 부하가 높은 상태에서 이러한 기법은 새로운 I/O 작업을 동시에 처리하는 데에 비효율적일 수 있다.
  * I/O 측면에서 resource 활용도를 높이려면 비동기 논블로킹(asynchronous and non-blocking) 모델을 사용할 수 있다.
    * 요청에 대한 응답을 기다리지 않고 다른 일을 하다가 응답이 왔을 때 그에 따른 업무를 진행하면 된다.
  * 분산 시스템에서 Service 간에 통신을 할 때 resource를 효율적으로 사용하기 위해서는 메시지 기반(message-driven) 통신 원칙을 따라야 한다.
  * 메시지 기반 통신의 전반적인 상호작용 흐름
    * 구성 요소들은 메시지 도착을 기다리고 이에 반응하며, 나머지 시간에는 휴면 상태에 있지만, 동시에 Non-Blocking 방식으로 메시지를 보낼 수 있어야 한다.
    * 접근 방식은 위치 투명성을 활성해 시스템 확장성을 향상시킨다.
  * 메시지 기반 통신 수행 - 메시지 브로커(Message Broker)
    * 메시지 대기열을 모니터링해 시스템이 부하 관리 및 탄력성을 제어할 수 있다.
    * 명확한 흐름 제어를 제공하고 전체 설계를 단순화한다.
    * 분산 시스템으로 구현되는 모든 비즈니스의 핵심 가치는 `응답성`이다.
    * 높은 응답성을 확보한다는 것은 탄력성 및 유연성 같은 기본 기법을 따른다는 의미이다.
    * 구성 요소가 독립적이고 적절하게 격리되어 있기 때문에 유지 보수 및 확장이 매우 용이하다.
* Reactive 선언문
  * 응답이 잘되고 탄력적이며 유연하고 메시지 기반으로 동작하는 시스템 → Reactive System이라 부른다.
  * 단순히 메시지 기반 통신을 쓴다고 다 Reactive는 아니다.
* BackPressure: 트래픽이 몰릴 때 Reactive하게 동작하면서 요청을 제대로 처리해낸다는 컨셉
  * Subscriber가 처리할 수 있는 만큼만 데이터를 당겨오는 방식

## [Reactive 선언문](https://www.reactivemanifesto.org/ko)
* Reactive System으로 구축된 시스템은 **보다 유연**하고, **느슨한 결합**을 가지며, [확장성](https://www.reactivemanifesto.org/ko/glossary#Scalability)이 있다.
* 개발 및 변경 사항을 적용을 쉽게 할 수 있게 해준다.
* 더욱 강한 **[장애](https://www.reactivemanifesto.org/ko/glossary#Failure) 내성**을 지니며, 비록 장애가 발생 하더라도, 문제를 보다 간단한 방식으로 해결할 수 있게 해준다.
* 또한 Reactive System은 **높은 응답성**을 가지며 사용자에게 효과적인 **상호 피드백**을 제공한다.

## Reactive System의 특성

### 응답성(Responsive)
[시스템](https://www.reactivemanifesto.org/ko/glossary#System)이 가능한 한 즉각적으로 응답하는 것을 응답성이 있다고 한다. 응답성은 사용자의 편의성과 유용성의 기초가 되지만, 그것뿐만 아니라 문제를 신속하게 탐지하고 효과적으로 대처할 수 있는 것을 의미한다. 응답성이 있는 시스템은 신속하고 일관성 있는 응답 시간을 제공하고, 신뢰할 수 있는 상한선을 설정하여 일관된 서비스 품질을 제공한다. 이러한 일관된 동작은 오류 처리를 단순화하고, 일반 사용자에게 신뢰를 조성하고, 새로운 상호작용을 촉진한다.

### 탄력성(Resilient)
시스템이 장애에 직면하더라도 응답성을 유지 하는 것을 탄력성이 있다고 한다. 탄력성은 고가용성 시스템, 미션 크리티컬 시스템에만 적용되지 않는다. 탄력성이 없는 시스템은 장애가 발생할 경우 응답성을 잃게 된다. 탄력성은 [복제](https://www.reactivemanifesto.org/ko/glossary#Replication), 봉쇄, [격리](https://www.reactivemanifesto.org/ko/glossary#Isolation), [위임](https://www.reactivemanifesto.org/ko/glossary#Delegation)에 의해 실현된다. 장애는 각각의 [구성 요소](https://www.reactivemanifesto.org/ko/glossary#Component)에 포함되며 구성 요소들은 서로 분리되어 있기 때문에 이는 시스템이 부분적으로 고장이 나더라도, 전체 시스템을 위험하게 하지 않고 복구 할 수 있도록 보장한다. 각 구성 요소의 복구 프로세스는 다른(외부의) 구성 요소에 위임되며 필요한 경우 복제를 통해 고가용성이 보장된다. 구성 요소의 클라이언트는 장애를 처리하는데에 압박을 받지 않는다.

### 유연성(Elastic)
시스템이 작업량이 변화하더라도 응답성을 유지하는 것을 유연성이라고 한다. 리액티브 시스템은 입력 속도의 변화에 따라 이러한 입력에 할당된 [자원](https://www.reactivemanifesto.org/ko/glossary#Resource)을 증가시키거나 감소키면서 변화에 대응한다. 이것은 시스템에서 경쟁하는 지점이나 중앙 집중적인 병목 현상이 존재하지 않도록 설계하여, 구성 요소를 샤딩(sharding)하거나 복제하여 입력을 분산시키는 것을 의미한다. 리액티브 시스템은 실시간 성능을 측정하는 도구를 제공하여 응답성 있고 예측 가능한 규모 확장 알고리즘을 지원한다. 이 시스템은 하드웨어 상품 및 소프트웨어 플랫폼에 비용 효율이 높은 방식으로 [유연성](https://www.reactivemanifesto.org/ko/glossary#Elasticity)을 제공한다.

### 메시지 구동(Message Driven)
Reactive System은 [비동기 메시지 전달](https://www.reactivemanifesto.org/ko/glossary#Message-Driven)에 의존하여 구성 요소 사이에서 느슨한 결합, 격리, [위치 투명성](https://www.reactivemanifesto.org/ko/glossary#Location-Transparency)을 보장하는 경계를 형성한다. 이 경계는 장애를 메시지로 지정하는 수단을 제공한다. 명시적인 메시지 전달은 시스템에 메시지 큐를 생성하고, 모니터링하며 필요시 [Back-Pressure](https://www.reactivemanifesto.org/ko/glossary#Back-Pressure)을 적용함으로써 유연성을 부여하고, 부하 관리와 흐름제어를 가능하게 한다. 위치 투명 메시징을 통신 수단으로 사용하면 단일 호스트든 클러스터를 가로지르든 동일한 구성과 의미를 갖고 장애를 관리할 수 있다. [Non-Blocking](https://www.reactivemanifesto.org/ko/glossary#Non-Blocking) 통신은 수신자가 활성화가 되어 있을 때만 [자원](https://www.reactivemanifesto.org/ko/glossary#Resource)을 소비할 수 있기 때문에 시스템 부하를 억제할 수 있다.

![image](https://user-images.githubusercontent.com/62865808/170988336-5ac7e147-61f7-4974-a0a5-7252ec50336b.png)
