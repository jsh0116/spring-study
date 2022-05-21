# Event Driven Programming(EDP)

## EDP란?
> 이벤트를 기반으로 하는 프로그래밍<br/>
> 참고: [Java tutorial EDP](https://www.youtube.com/watch?v=4_KrupA6y1s), [Simple Event Driven Microservices with Spring Cloud Stream](https://spring.io/blog/2019/10/15/simple-event-driven-microservices-with-spring-cloud-stream)

### [What is event-driven programming?](https://github.com/30-seconds/30-seconds-of-interviews/blob/master/questions/event-driven-programming.md)
> Event-driven programming is a paradigm that involves building applications that send and receive events. When the program emits events, the program responds by running any callback functions that are registered to that event and context, passing in associated data to the function. With this pattern, events can be emitted into the wild without throwing errors even if no functions are subscribed to it.
A common example of this is the pattern of elements listening to DOM events such as click and mouseenter, where a callback function is run when the event occurs.<br/>

Event-driven programming은 이벤트를 송수신하는 어플리케이션을 구축하는 패러다임이다.<br/>
Program이 Event를 보내면, Program은 해당 Event와 상황에 등록된 Callback 함수를 실행해서 관련된 데이터를 전달해 응답한다.<br/>
이 패턴으로, Event는 어떤 함수를 subscribe하지 않아도 에러없이 Event를 보낼 수 있다.<br/>

일반적인 예제는 MouseDown, click과 같은 DOM 이벤트를 listening하는 패턴으로, Event가 발생했을 때 callback이 실행된다.
```js
document.addEventListener("click", function(event) {
  // This callback function is run when the user
  // clicks on the document.
})
```

DOM Context가 없는 pattern
```js
const hub = createEventHub()
hub.on("message", function(data) {
  console.log(`${data.username} said ${data.text}`)
})
hub.emit("message", {
  username: "John",
  text: "Hello?"
})
```
on은 이벤트를 구독하는 방식이고, emit은 이벤트를 보내는 방식이다.

[Event Driven Architecture & Thread](https://velog.io/@limprove89/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EA%B8%B0%EB%B0%98-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%93%B0%EB%A0%88%EB%93%9C)

![image](https://user-images.githubusercontent.com/62865808/169644008-a220489d-048d-43a0-bc54-f4885543c3af.png)

[![SPRING CAMP 2018](http://img.youtube.com/vi/Dn5irt7bClM/0.jpg)](https://youtu.be/Dn5irt7bClM?t=0s)
