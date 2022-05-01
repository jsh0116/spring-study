# MicroService (마이크로서비스)

참고 : [마이크로서비스 정의 - 마틴 파울러](https://martinfowler.com/articles/microservices.html)

## Monolith와 MicroService 비교
---
### Monolith
---
> 전통적인 시스템 구조
> 하나의 단위로 개발되는 일체식 Application
> 'Client - 전체 System - DB' 형식

* Server 측 Application이 하나에 개발되므로 작은 변화에도 새로운 버전으로 전체를 빌드해서 배포가 필요하기 때문에 특정 기능만 확장할 수 없으며, 반드시 전체 애플리케이션을 동시에 확장해야 한다.
* Service가 점차 성장하고 발전하며 그에 상응하는 요구사항으로 인해 서비스는 점차 복잡해질 수 있다.
  *  '새로운 기능들도 생기고, 기존에 있던 기능들도 커지기 때문이다.'
*  보통 Load Balancer를 앞에 두고 여러 instance 위에 큰 덩어리를 복제해 수평으로 확장한다.

#### Monolith의 단점
* 빌드 시간 및 테스트 시간이 길어진다.
  * 여러 개의 Monolith System 전체를 build & 배포해야 한다.
* 개발 언어에 종속적이다.
* 선택적으로 확장할 수 없다.
* 하나의 서비스가 다른 모든 서비스에 영향을 준다.
* DB에서 장애가 일어날 시 탄력적으로 대응할 수 없다.
  * Application의 경우에는 병렬로 되어 있다보니 확장을 하더라도 사용량 증가에 대응할 수 있지만 DB는 보통 1개로 되어있다보니 대응이 어려운 경우가 많다.

### MicroService
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
