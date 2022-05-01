# MicroService (마이크로서비스)

참고 : [마이크로서비스 정의 - 마틴 파울러](https://martinfowler.com/articles/microservices.html)

## Monolith와 MicroService 비교
---
### Monolith
---
> 전통적인 시스템 구조 <br/>
> 하나의 단위로 개발되는 일체식 Application <br/>
> 'Client - 전체 System - DB' 형식 <br/>

* Server 측 Application이 하나에 개발되므로 작은 변화에도 새로운 버전으로 전체를 빌드해서 배포가 필요하기 때문에 특정 기능만 확장할 수 없으며, 반드시 전체 애플리케이션을 동시에 확장해야 한다.
* Service가 점차 성장하고 발전하며 그에 상응하는 요구사항으로 인해 서비스는 점차 복잡해질 수 있다.
  * `새로운 기능들도 생기고, 기존에 있던 기능들도 커지기 때문이다.`
* 보통 Load Balancer를 앞에 두고 여러 instance 위에 큰 덩어리를 복제해 수평으로 확장한다.

#### Monolith의 단점
* 빌드 시간 및 테스트 시간이 길어진다.
  * 여러 개의 Monolith System 전체를 build & 배포해야 한다.
* 개발 언어에 종속적이다.
* 선택적으로 확장할 수 없다.
* 하나의 서비스가 다른 모든 서비스에 영향을 준다.
* DB에서 장애가 일어날 시 탄력적으로 대응할 수 없다.
  * Application의 경우에는 병렬로 되어 있다보니 확장을 하더라도 사용량 증가에 대응할 수 있지만 DB는 보통 1개로 되어있다보니 대응이 어려운 경우가 많다.

### MicroService
---
* Server 측이 여러 개의 조각으로 구성돼 각 Service가 별개의 instance로 로딩된다.
  * 여러 Service instance가 모여 하나의 Business Application을 구성한다.
* 각기 다른 저장소로 업무 단위로 모듈 경계가 명확하게 구분된다.
* 확장 시에는 특정 기능별로 독립적으로 확장할 수 있고, 특정 Service를 변경할 필요가 있다면 해당 Service만 build해서 배포하면 된다.
* 각 Service가 독립적이어서 서로 다른 언어로 개발하는 것도 가능하므로 각 Service의 소유권을 분리해 서로 다른 팀이 개발 및 운영할 수 있다.
* 각 Service가 분리되어 별도의 DB를 가지고 있으며, 특정 Service의 확장이 필요할 경우 해당 Service만 scale out하면 된다.

#### MicroService의 단점
* 성능 이슈
  * 서로 독립적인 Service로 분리된 Component들은 API 통신을 통해 데이터를 주고 받아야 하기 때문에 성능 저하가 발생할 수 있다.
* Transaction 관리의 어려움
  * 서로 독립적인 Service로 분리된 Component들 간의 발생한 transaction을 관리하기 어렵다.
* Data 무결성 보장의 어려움
  * Service 단위로 분리된 Data Layer 간의 DeNormalization(역정규화)를 하게 될 경우, Data Integrity(데이터 무결성)을 보장하기 어렵다.
* System의 복잡도가 높고 높은 기술력을 요구한다.

## SOA와 MicroService
---
### 소프트웨어 공학에서의 모듈화(modularity) 개념 발전 흐름
---
* 구조적 방법론 : 단순히 기능을 하향식 분해해서 설계해 나가는 방법
* 객체지향적 방법론 : 객체 단위로 모듈화하기 위한 방법
* CBD (Component Based Development) : 모듈화의 단위가 기능별로 재사용할 수 있는 좀 더 큰 Component가 되는 방법
* SOA (Service Oriented Architecture) : Component를 모아 Business적으로 의미 있고 완결적인 Service 단위로 모듈화하는 방법

`넓게 보면 CBD & SOA는 단위 Component나 Service를 구성해서 System을 만드는 개념이며, MicroService System의 구조와 매우 유사하다.`

#### MSA (MicroService Architecture)
> MicroService 기반으로 시스템을 개발하는 Architecture 및 개발 방식

### Service 기반 Architecture를 칭하는 SOA와 MSA 개념적 차이점
* 여러 개의 응집된 Business Service의 집합으로 System을 개발한다는 점에서는 SOA와 MSA는 개념적으로 큰 차이가 없다.
* SOA는 구체적이지 않고 이론적이며, 실제 Business 성공 사례가 많지 않다.
* MSA는 Cloud Infra 기술의 발전과 접목되어 Amazon과 Netflix에 의해 구체화되고 Business 성공 사례가 존재한다.

`이상적이었지만 성공을 증명 못한 SOA ⇒ 클라우드 인프라 등장으로 Hardware를 유연하게 다룰 수 있게 되며 실현되어 성공적으로 증명된 시스템 구조 MSA`

#### 마틴 파울러 - 성공 사례의 특징을 뽑아 MicroService 정의
> MicroService는 여러 개의 작은 서비스 집합으로 개발하는 접근 방법이다.<br/>
> 각 Service는 개별 Process에서 실행되고, HTTP 자원 API 같은 가벼운 수단을 사용해서 통신한다.<br/>
> 또한 Service는 Business 기능 단위로 구성되고, 자동화된 배포 방식을 이용하여 독립적으로 배포된다.<br/>
> Service에 대한 중앙 집중적인 관리는 최소화하고, 각 Service는 서로 다른 언어와 데이터, 저장 기술을 사용할 수 있다.

![image](https://user-images.githubusercontent.com/62865808/166147488-fce29c55-d9f4-4aa1-82ce-db52dead4fa0.png)

* 각 Service와 저장소는 다른 Service 및 저장소와 격리돼 있으며, API를 통해서만 느슨하게 연결된다.
* 독립적으로 확장 가능하며, 하나의 Service만 독립적으로 배포 가능하다.
* 다른 Service와 연계된 API에 영향을 주지 않는다면 내부의 언어나 저장소는 자율적으로 선택할 수 있다.
* 특정 Service를 구축하는데 사용되는 언어나 저장소를 자율적으로 선택할 수 있는 방식을 `폴리글랏(Polyglot)하다` 라고 표현하며 클라우드 등의 가상 인프라가 지닌 유연성이 이를 가능하도록 지원한다.

#### MSA와 SOA 차이점 및 모듈화 극대화 특징들
* CBD/SOA의 접근법에서는 Application은 모듈별로 분리했으나 데이터 저장소까지는 분리하지 못했다.
 * 여러 Application이 하나의 저장소를 공유한다.
* 데이터의 강한 결합으로 Application도 독립적으로 사용하기가 힘들었다.
* MSA는 두가지 개념으로 모듈화 방식을 강화했고 실현할 수 있게 됐다.
* Service별 저장소를 분리해서 다른 서비스가 저장소를 직접 호출하지 못하도록 캡슐화한다.
* 다른 Service의 저장소에 접근하는 수단은 API밖에 없다.
* REST API 같은 가벼운 개방형 표준을 사용해 각 Service가 느슨하게 연계되고 누구나 쉽게 사용할 수 있다.
