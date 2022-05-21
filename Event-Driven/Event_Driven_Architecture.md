# Event Driven Architecture

## EDA란?

> **Event Drive Architecture (EDA)**<br/>
> Event 기반의 Architecture 설계 방식<br/>
> 비동기 통신 방식을 이용해 Loose Coupling를 지향하는 Architecture<br/>
> 분산 시스템 상에서 producer가 Event를 publish하고, Event를 subsribe하고 있던 consumer가 Event를 받아 처리하는 시스템을 의미한다.<br/>
> 참고 : [분산 환경과 EDA: SAGA, Event Sourcing, CQRS](https://velog.io/@borab/%EB%B6%84%EC%82%B0-%ED%99%98%EA%B2%BD%EA%B3%BC-Event-Driven-Architecture)

## EDA의 필요성

* Decoupling : 다른 구성 요소를 분리할 수 있다.
* Dependency Inversion : 의존성을 반전시킬 수 있다.
* Scalability : 더 나은 확장을 제공한다.

### 기존 Service의 경우
* Service1이 Service2를 호출할 때 Service2를 알아야 한다.
* 두 Service 간 Dependency가 존재한다.

### EDA의 경우
* Service1에서 Event를 보낸다
* Service2에서 Service1을 알아야 할 필요가 없으며, Event만 알고 Event 처리만 하면 된다.
* 두 Service간 Dependency가 없다

## Event Driven 구조
![image](https://user-images.githubusercontent.com/62865808/169640434-f95337a8-c9fa-41ec-877e-fb39315069d9.png)

![image](https://user-images.githubusercontent.com/62865808/169640439-a05b80c4-8618-484a-a09b-9e1bf00a5cd6.png)

[Understanding the Event-driven Architecture](https://softobiz.com/understanding-the-event-driven-architecture/)

![image](https://user-images.githubusercontent.com/62865808/169640488-acab6a2c-c2ef-4caf-b1e6-30068cc1710c.png)

[What is an Event-Driven Architecture?](https://aws.amazon.com/ko/event-driven-architecture/)

## Event Driven 설계

> EDA는 설계 과정이 복잡하며, 많은 비용을 발생시킨다.<br/>
> 따라서, 개발하고자 하는 기능을 EDA로 설계하는 것이 적합한지 아래의 CheckList를 통해 검토할 필요가 있다.

### CheckList: Event Driven 구조가 적합한가?
1. **Transaction 정보를 포함된 Event를 전달하는 경우인가?**
    - 메세지 자체만으로 이벤트에 대한 설명이 가능해야 한다.
    - 대규모 트래픽 처리 서버의 어플리케이션의 경우 stateless한 구성이 선호된다.
2. **추가 비용 없이 Event를 생성하고 subscribe할 수 있는 구조인 경우인가?**
3. **강력하게 분리된 MicroService 구성이 필요한 경우인가?**
    - 동기 방식의 호출의 경우 특정 MicroService의 `확장 or 축소`에 따라 Dependency 있는 MicroService를 같이 확장 or 축소해야하기 때문에 MicroService 간의 비동기 방식의 호출 및 buffer 구조를 사용한다.
4. Publish & Subscribe 구조가 필요한 경우인가?
    - Service 간 message 송수신 Publish & Subscribe Model이 적합한지 확인이 필요하다.

### EDA의 필수 구성 요소

1. **Event Generator : Publisher, Producer, Creater**
    - 표준화된 형식의 Event를 produce 및 publish하고 생성된 Event는 `Event Channel`로 전송한다.
2. **Event Channels : Bus**
    - `Event Generator → Event Processing Engine`으로 수집된 데이터를 전파시킨다. 즉, Event를 필요로 하는 System까지 발송한다.
3. **Event Processing Engine : Consumer, Processor**
    - 수신한 Event를 식별/처리한다.
    - 처리 결과에 따라 새로운 Event를 생성하며, Consumer는 Event의 송신자에 대한 정보를 알 필요가 없다.

## EDA 동작 방식

1. **Message 생성 (Publish / Subscribe)**
    - Event 생성 시 Subscriber 에게 전달한다.
    - 반복되어 전달되지 않으며, 송신자의 정보는 알 필요가 없다.
2. **Event Source**
    - `Event Processor`에게 이벤트를 전달하는 역활을 하며, 1개 이상일 수 있다.
3. **Event Processor**
    - 수신된 Event에 대한 여러 행동을 수행하는 역활을 한다.
4. **Event Consumer**
    - Event에 대한 실질적인 처리를 한다.

### Event Processing Style
> 각 특징에 맞게 4가지로 구분된 Event Process Style이 있다.

1. **Simple Event Processing**
    - 각 Event가 직접적으로 수행할 Action과 mapping된다.
    - 일반적으로 실시간 **작업의 흐름(Flow of Work)**을 처리할 때 사용한다.
    - 처리 시간과 비용이 적다.
2. **Event Streaming Processing**
    - 일반적인 Event와 중요한 Event가 모두 발생하며 이를 filtering하여 수신자에게 전달한다.
    - 일반적으로 실시간 정보의 흐름(Flow of Information)을 처리할 때 사용한다.
3. **Complex Event Processing**
    - 일반적인 Event Pattern을 통해 복잡한 Event를 추론하는 방법한다.
    - ex: 일반적인 주식의 등락을 파악하여 투자할 타이밍을 추론
4. **Online Event Processing**
    - 비동기 분산 Event Log를 사용하여 복잡한 이벤트를 처리하며 지속해 데이터를 관리한다.
    - 높은 확정성과 일관성을 가지며 유연하게 대처 가능한 패턴이다.
    - 단점 : 처리 시간을 보장할 수 없다.

## 주요 Topology

### 중재자 토폴리지 (Mediator Topology)
* 단일 Event 대기열과 각 Event를 관련 Event Processor로 보내는 중재자가 있다.
* 여러 단계의 과정을 중재자를 통해 조율할 필요성이 있으면 사용될 수 있다.
* Queue를 이용하여 사전 처리를 진행한 후, 관련된 Consumer에게 전달한다.
* Event Queue의 구현은 `단순 Message Queue 또는 Rabbit MQ` 와 같은 복잡한 Messaging Protocol을 포함하는 대규모 분산 System에서 Interface를 전달하는 message의 형태일 수 있다.
* Mediator Topology Architecture Diagram

![image](https://user-images.githubusercontent.com/62865808/169643205-88790f3c-6da3-45c7-b53a-8906fdb870b6.png)

### 브로커 토폴리지 (Broker Topology)
* Queue나 Mediator 없이 Event와 Response을 직접적으로 연관시킬 때 사용한다.
* Broker Component와 Event Processor Component가 있다.
* Event Processor는 Event 대기열 없이 Event를 획득하고 종료를 나타내는 다른 Event 처리 및 게시하는 역할을 한다.
* Event Processor는 Event를 연결하는 Broker의 역할을 한다.
* Event가 Processor에 의해 처리되면 다른 Processor가 진행할 수 있도록 다른 Event가 게시된다.
* Broker Topology Architecture Diagram

![image](https://user-images.githubusercontent.com/62865808/169643380-faa5b4f1-ac5f-47e4-bc92-9dc7130b3dd7.png)

> 참고: [Event Driven Architecture Pattern](https://towardsdatascience.com/event-driven-architecture-pattern-b54fc50276cd)
