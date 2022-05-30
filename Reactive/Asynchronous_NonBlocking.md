# Asynchronous & Non-Blocking I/O

## Synchronous & Blocking vs Asynchronous & Non-Blocking 방식의 차이
![image](https://user-images.githubusercontent.com/62865808/170989262-ea14ed17-86f2-4ce5-a016-14f1483dc5b2.png)

[Reactive 프로그래밍 (Spring reactive-1)](https://taes-k.github.io/2020/08/12/spring-reactive-1/)

## API 소요 시간

### 기존 API 취합 방식
* 기존 API 취합해서 전달하는 경우에는 각각의 API가 완료될 때까지 대기
* 총 소요시간 : `각 API들이 1개씩 실행 -> 완료되고, 전체가 완료될 때까지 걸리는 시간`
  
### 리액티브 시스템 방식
* API 요청 후 대기하지 않고 다음 API 요청
* 총 소요시간 : `전체 API 중 가장 오래 걸리는 1개의 API 시간이 전체 경과 시간`
* Thread Pool의 딜레마
  * CPU, Memory가 충분하더라도 **Thread가 모자라는 경우** 처리율이 저하된다.
  * 위 문제 해경 방법으로 Thread를 과도하게 늘릴 경우 **Memory 및 CPU 부하**로 성능이 저하된다.
  * Context Switching이라 하는 CPU간 전환 과정은 엄청난 부하가 가해지는 과정이다.
  * 결국 **Thread를 무조건 늘린다고 문제를 해결할 수 없다.**
* resource의 효율적인 사용
  * Thread, CPU, Memory, Network 등을 최대한 효율적으로 활용해야 한다.
  * 비동기 로직 구성
    * 작업 요청하는 시점과 결과를 받아 처리하는 시점 불일치
      * 결과 데이터를 받으면 처리한다
      * 그때까지는 **다른 작업을 하거나 리소스를 반납한다**
    * 비동기 작업의 결과를 처리할 로직을 담은 Callback을 이용한다.
      * 비동기 작업의 결과를 Callback에 전달한다.
      * 결과를 담는 것이 아니라 **함수**를 담는다.
      * Continuation-style API 이용
    * 비동기 작업을 별도의 Thread에서 수행한다.
      * Callback을 사용하는 동기 요청처리도 있다.
      * Spring의 template/callback, Stream
  * Non-Blocking I/O
    * I/O 작업을 수행하는 동안 Thread를 점유하지 않는다. (User Process 작업을 중단시키지 않는다.)
    * 동시에 많은 I/O 작업 처리 가능하다.
    * Non-Blocking과 Asynchronous의 차이점
      * Non-Blocking I/O는 처리가 완료되지 않으면 에러를 회신하고 block 상태로 만들지 않는다.
      * Asynchronous I/O는 처리를 바로할 수 없을 때, 처리가 완료 되는 시점까지 백그라운드에서 대기하고 종료한 타이밍을 회신하는 차이가 있다.
    
     ![image](https://user-images.githubusercontent.com/62865808/170993443-356daae9-9442-475b-8c97-b7af4cc7fcbf.png)
     
 [I/O Models](https://www.masterraghu.com/subjects/np/introduction/unix_network_programming_v1.3/ch06lev1sec2.html)
