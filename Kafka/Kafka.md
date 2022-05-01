# Kafka

## [Intro](https://kafka.apache.org/intro)
---
## Event Streaming
---
* [위키백과에서의 Event Stream Processing] -> **실시간 스트리밍**을 의미한다.
* Event Streaming이 중요한 이유는 **데이터 가용성(Availability)** 때문이다
* 스트리밍은 **데이터를 추출하는 시점부터 시간과 주체에 구애받지 않고 접근이 가능**하다.
* Event Streaming의 형태로 실시간으로 데이터를 캡처하는 방식이다.
* 검색할 수 있도록 Event Streaming을 영구적으로 저장한다.
* 실시간으로 Event Streaming을 조작, 처리 및 반응한다.
* 필요에 따라 Event Streaming을 다른 목적지 기술로 routing할 수 있다.

## 특징
---
* 실시간으로 필요한 기능들을 처리할 수 있다.
* 실시간으로 추적하고 모니터링할 수 있다.
* 센서 데이터를 지속적으로 캡처하고 분석할 수 있다.
* 고객 상호 작용 및 데이터를 수집하고 즉시 대응할 수 있다.
* 여러 곳에서 생성된 데이터를 연결, 저장 및 사용 가능하게 한다.
* Event 중심 Architecture 및 MicroService의 기반 역할을 한다.

## Kafka - Event Streaming Platform
---
> kafka는 세 가지 주요 기능을 통해 Event Streaming을 구현할 수 있다.
1. To **publish** (write) and **subscribe to** (read) streams of events, including continuous import/export of your data from other systems.
2. To **store** streams of events durably and reliably for as long as you want.
3. To process streams of events as they occur or retrospectively.

* **Producer** : Message를 생산하여 Broker의 topice으로 전달하는 역할
* **Broker** : Kafka Application이 설치되어 있는 Server 혹은 Node를 지칭
* **Consumer** : Broker의 topic으로부터 저장된 message를 전달받는 역할
* **Zookeeper**
  * 분산 Application 관리를 위한 코디네이션 시스템이다.
  * 분산된 Node의 정보를 중앙에 집중하고 구성관리, Group naming, 동기화 등의 서비스를 수행한다.

### 작동방식
* Producer는 새 message를 Kafka에 전달한다.
* 전달된 message는 Broker의 topic이라는 message 구분자에 저장한다.
* Consumer는 Subscribe한 topic에 접근하여 message를 가져온다. (pull 방식)

## [주요 개념 및 용어](https://kafka.apache.org/intro)
  * 링크의 Main Concepts and Terminology 참조

