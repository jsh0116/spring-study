# Kafka

## [Intro](https://kafka.apache.org/intro)

## Event Streaming

* [위키백과에서의 Event Stream Processing] -> **실시간 스트리밍**을 의미한다.
* Event Streaming이 중요한 이유는 **데이터 가용성(Availability)** 때문이다
* 스트리밍은 **데이터를 추출하는 시점부터 시간과 주체에 구애받지 않고 접근이 가능**하다.
* Event Streaming의 형태로 실시간으로 데이터를 캡처하는 방식이다.
* 검색할 수 있도록 Event Streaming을 영구적으로 저장한다.
* 실시간으로 Event Streaming을 조작, 처리 및 반응한다.
* 필요에 따라 Event Streaming을 다른 목적지 기술로 routing할 수 있다.

## 특징

* 실시간으로 필요한 기능들을 처리할 수 있다.
* 실시간으로 추적하고 모니터링할 수 있다.
* 센서 데이터를 지속적으로 캡처하고 분석할 수 있다.
* 고객 상호 작용 및 데이터를 수집하고 즉시 대응할 수 있다.
* 여러 곳에서 생성된 데이터를 연결, 저장 및 사용 가능하게 한다.
* Event 중심 Architecture 및 MicroService의 기반 역할을 한다.

## Kafka - Event Streaming Platform

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

### 특징
* 다른 message queue들과는 지향하는 바가 다르다.
* publish - subscribe 구조를 가진다 
  * message를 보내는 publish 역할과 subscribe 역할이 완벽하게 분리되어 있다.
  * loosely coupling을 통해 한쪽 System에서 문제가 발생하더라도 서로 의존성이 없으므로 연쇄작용이 발생할 확률이 낮다.
  * Consumer의 Server가 추가 되더라도 Kafka로만 보내면 되기 때문에 Server 추가에 대한 부담이 없다.
  * 하나의 topic에 여러 Producer & Consumer들이 접근 가능한 구조다.
    * 하나 이상의 topic으로 message를 보내고 가져올 수 있다.
* Disk에 messagbe를 저장한다.
  * TCP/IP 통신을 통해 Disk에 바로 저장한다.
    * `RabbitMQ(APMQ 프로토콜 이용)`
    * kafka 외의 MQ의 경우 memory에 저장하는 방식을 사용한다.
    * 다른 MQ 또한 별도의 설정을 통해 Disk에 저장하는 방식을 사용하여 Server Down에 대응할 수 있지만 Kafka는 별도의 설정을 할 필요가 없다.
  * 통신방법의 차이로 초당 10만건의 데이터 처리가 가능하다.
  * 특별한 설정 없이 `데이터의 영속성(Persistence)` 가 보장된다.
  * 트래픽이 일시적으로 폭주해 Consumer의 처리가 늦어지더라도 Disk에 보관되기 때문에 Consumer는 손실 없이 message를 가져갈 수 있다.
* 분산 환경에 특화되도록 설계되었다.
  * 하나의 Kafka cluster는 3대의 Broker로부터 시작해 수십대의 Broker로 확장 가능하다.
  * 확장 작업은 Kafka Service의 중단 없이 온라인 상태에서 작업이 가능하다.
  * 트래픽 및 사용량 증가로 Cluster를 확장하는 작업은 간단하다.
* Cluster 구성, fail-over, replication등 여러 특징이 있다.
  * 고성능을 유지하기 위해 내부적으로 분산 처리, Batch 처리 등 다양한 기법을 사용한다.
  * 단일 시스템보다 성능이 좋다.

## Kafka Data Model

### Topic
* message를 논리적으로 묶은 개념
  * DB Table 및 File System의 폴더와 유사한 개념
  * Producer가 message를 보낼 경우 Topic에 message가 저장

![image](https://user-images.githubusercontent.com/62865808/166153510-934454bc-83ba-4602-b938-e508770b89df.png)

### Partition
* Topic을 구성하는 데이터 저장소이며 message가 저장되는 위치이다.
* 여러개의 Producer에서 한개의 partition으로 message를 보낼 경우 병목 현상이 발생하고, message의 순서를 보장할 수 없게 된다.
* partition을 여러개로 늘리고 그 수만큼 producer도 늘려 하나의 partition마다 하나의 producer message를 받으면 빠르다.
* partition이 무작정 많아질 경우
  * File handler 낭비가 존재한다.
    * 각 partition은 Broker의 Directory와 mapping되고 저장되는 데이터마다 2개의 파일(index, 실제 파일)이 있기 때문에 많은 file handle이 발생할 경우 resource가 낭비된다.
  * 장애 복구 시간이 증가할 수 있다.
    * Kafka는 Replication을 지원하고, 이를 통해 지속적으로 Leader Partition을 Follower Partition으로 Replication하게된다.
    * partition 수가 너무 많을 경우 Replication 수행이 느려져 장애복구시간이 증가할 수 있다.
  * partition 수를 줄이는 것은 불가능하다.
    * Kafka에서 partition 수를 늘리는 것은 아무때나 가능하지만 partition 수를 줄이는 방법은 제공하지 않는다.
    * 줄이고 싶다면 topic 자체를 삭제하는 것 말고는 방법이 없다.

### offset과 message 순서
* offset
  * partition마다 메시지가 저장되는 위치.
  * partition 내에서 순차적으로 unique하게 증가하는 숫자 형태로서 동일 partition 내 message의 순서를 보장해준다.
  * Consumer는 message를 가져올 때마다 offset 정보를 commit함으로써 기존에 어디 위치까지 가져왔는지 알 수 있게 된다.

### partition과 message 순서
* partition을 여러개 지정할 경우
  * Producer가 message를 보낼 때 partition이 여러개인 경우 message는 각각의 partition으로 순차적으로 배분한다. (Round-robin 방식)
  * message의 순서는 오로지 동일 partition 내 offset을 기준으로만 보장된다.
  * 여러개의 partition을 사용할 경우 동일 partition 내에서는 순서가 보장되지만, partition과 partition 사이에서는 순서를 보장하지 못하기 때문에 전체 message를 출력할 경우 순서가 섞일 수 있다.
  * 전체 message의 순서를 보장하고 싶은 경우 partition을 1개로만 설정
    * 이 경우, partition이 하나이므로 분산 처리가 불가능

### Replication
![image](https://user-images.githubusercontent.com/62865808/166154138-4345311a-e02f-4556-b032-fcf1a34269c8.png)

* 고가용성 및 데이터 유실을 막기 위해 Replication을 수행한다.
* Replication 그룹 내 동기화 및 신뢰성을 유지한다.
* Follower는 Read/Write 권한이 없고 오로지 reader로부터 데이터를 복제하기 때문에 특정 Follower가 down되서 Replication을 못할 경우 동기화 문제가 발생한다.
* 문제가 감지된 Follower 즉, 설정된 일정주기(replica.lag.time.max.ms)만큼 요청이 오지 않는 팔로워는 ISR 그룹에서 추방된다.

### Consumer Group
* Consumer가 message를 소비하는 시간보다 Producer가 message를 전달하는 속도가 더 빨라서 message가 점점 쌓일 경우를 대비한다.
* 동일 topic에 대해 여러 Consumer가 message를 가져갈 수 있도록 Consumer Group이라는 기능을 제공한다.
* 하나의 Consumer가 Producer의 message 전송 속도를 따라가지 못할 경우
![image](https://user-images.githubusercontent.com/62865808/166154307-97279688-5edd-468b-a1ab-6eb90613dd02.png)

* Consumer를 확장하여 하나의 partition 당 하나의 Consumer가 연결되도록 할 수 있다. (rebalance)
![image](https://user-images.githubusercontent.com/62865808/166154340-0671ec56-c71a-48de-9442-8141683bb4af.png)

* 하나의 partition 당 하나의 Consumer가 1:1 연결되어야 하므로 아래와 같이 확장할 수는 없다.
![image](https://user-images.githubusercontent.com/62865808/166154373-b270ceea-d18e-409a-a35d-de32e032168f.png)

* Consumer Group은 Consumer가 일정한 주기로 하는 HeartBeat(Consumer가 poll하거나 message의 offset을 commit할때 보냄)를 통해 Consumer가 message를 처리하고 있다는 것을 인지하며, 만약 오랫동안 HeartBeat가 없다면 해당 Consumer의 session이 Timeout되고 rebalance를 수행한다.
* Kafka의 Message Queue System은 큐에서 message를 가져가도 사라지지 않기 때문에 여러 Consumer 그룹이 동일 topic에 붙을 수 있다.
[Kafka Quickstart](https://kafka.apache.org/quickstart)
[카프카 설치 및 실행 관련 블로그](https://velog.io/@qlgks1/2%EC%9E%A5.%EC%B9%B4%ED%94%84%EC%B9%B4-%EC%84%A4%EC%B9%98%EC%99%80-%EC%8B%A4%ED%96%89)
[카프카 기본 개념 설명 영상](https://www.youtube.com/watch?v=waw0XXNX-uQ)

### Reference
[아파치 카프카란 무엇인가](https://velog.io/@jaehyeong/Apache-Kafka%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
[KafKa란?](https://velog.io/@qlgks1/0%EC%9E%A5.-%EC%B9%B4%ED%94%84%EC%B9%B4Kafka%EB%9E%80)
[Message Queue](https://steady-snail.tistory.com/165)
