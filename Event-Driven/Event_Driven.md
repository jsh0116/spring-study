# Event & Event Driven

## Event란?

> System에 business Domain의 중요한 의미를 가지는 상태 변경을 의미한다.
> **Event는 중요한 개념으로서 `명령`이 아닌 `관찰`을 의미한다.**
* `명령`: Event 생성 주체가 상대방의 어떠한 동작을 기대하는 것
  * Event Producer가 특정 대상을 상대로 message를 전달하고 수신자는 그에 대한 회신을 돌려준다
  * 높은 결합도
* `관찰`: 이벤트 생성 주체가 상대방의 어떠한 동작도 기대하거나 관심을 가지지 않는 것
  * Event Producer는 필요한 이벤트에 대한 정보를 게시하고 해당 이벤트에 관심이 있는 Event Consumer가 선택적으로 이벤트를 소비한다.
  * 낮은 결합도
* 참고 - [wikipedia](https://ko.wikipedia.org/wiki/%EC%9D%B4%EB%B2%A4%ED%8A%B8_(%EC%BB%B4%ED%93%A8%ED%8C%85)) [mdn web docs](https://developer.mozilla.org/en-US/docs/Web/Events)

## Event의 특성

* **발생한 사건**을 표현하는 message 형식이다.
* **중요한 정보**를 포함한다.
  * Event 자체만으로도 요청에 대한 충분한 정보를 얻을 수 있어야 한다.
* **불변성**을 지닌다.
  * 과거의 메시지를 변경할 수 없음을 보장한다.
* **과거 시제의 동사**로 표현된다.
  * 발생한 사건에 대한 결과 상태를 전달하기 때문이다.
  * e.g. "새로운 주문이 생성되었다."
* Event 생성 시스템은 어떻게 Event가 처리되는지 **관여하지 않는다**. 

## Event Driven 주요 개념

* `Producer(생성자)`: Event 감지 및 생성을 담당한다.
* `Consumner(소비자)` : Event Producer가 생성한 Event를 소비한다.
* `Event Channel` : 이벤트 생성기에서 이벤트 소비자로 이벤트 전송을 담당한다.
* `Event` : 작업을 수행할 때 발생하는 개체 상태의 중요한 변경한다.
* `Event Handler` : Event 발생을 처리한다.
* `Event Loop` : Event와 Event Handler 간의 상호 작용 흐름을 처리한다.
* `Event Flow Layer` : Event Producer, Consumer 및 Event Channel(=Event Bus)의 세 가지 논리적 계층에 구축된다.

![image](https://user-images.githubusercontent.com/62865808/169639218-9b59863b-b57b-4b3d-828c-ed69f6b32224.png)

* `Event 형식의 표준화 시도` : CloudEvent
```
// e.g.
{
  "specversion" : "1.0",
  "type": "/com.example.someevent",
	"source": "/mycontext",
	"id": "E000-0000-0000",
	"time": "2022-02-20T01:00:00Z",
	"comexampleextension1": "value",
	"comexampleothervalue": 5,
	"datacontenttype": "application/json",
	"data": {
		"appinfoA": "abc",
		"appinfoB": 123,
		"appinfoC": true
	}
}
```
* `다양한 Event 형식 지원`: JSON, Avro, Parquet 등의 serialize 형식을 사용할 수 있다.
* `Event 처리 관련 병목 현상`: message의 형식이 문제를 발생시키기보다 message를 수신하는 Sysmtem 혹은 System 저장소 레벨이 문제를 발생시킨다.
