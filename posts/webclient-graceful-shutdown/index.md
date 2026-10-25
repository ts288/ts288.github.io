---
layout: article
title: "webClient 와 graceful shutdown"
permalink: /posts/webclient-graceful-shutdown/
category: spring
topic: webflux-reactor
list_order: 5
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1c53abb1a94d80e39a06f6f6ab56eaf5?pvs=204)

# webClient 와 graceful shutdown

\[webClient → reactor netty 공부하다가, reactor netty 가 톰캣 스레드가 아니라 독립적인 스레드로 동작한다면 graceful shutdown 은 어떻게 구현해야 하지? 생각이 들어서 정리\]

- graceful shutdown
  - 애플리케이션을 종료하기 전에 애플리케이션 내에서 실행 중인 작업 (스레드) 가 존재하는지 확인하여, 모든 스레드가 종료된 후 (모든 트랜잭션이 수행된 후) 에 안전하게 종료하기 위해 사용하는 방식
- spring 서버에서는 특히 수행중인 <b>비동기 작업</b>이 있는지 체크해야 한다.
- 문제는 spring 에는 톰캣 스레드 말고도 webClient 에서 사용하는 reactor netty 이벤트 루프 스레드도 존재한다는 것!
- 이를 체크하여 종료해야한다.
  - 방법은 세 가지 정도가 있다.
    1. 직접 reactor netty 이벤트 루프에 접근하여 상태를 체크하고 종료하는 것 (<b>비추)</b>
       1. netty 에 직접 접근할 수는 없으나, reactor netty 를 통한 간접 접근은 가능함
       2. 그러나 이 방법은 리액티브 프로그래밍의 설계 철학에 어긋남!
          1. 설계 철학인 <b>구독 기반형 선언형 처리 + 추상화</b>에 어긋남!
          2. 선언형 프로그래밍은 ‘구체적인 기술은 라이브러리가 수행, 프로그래머는 믿고 맡기기’ 라는 철학에 기반함.

          > 내부 상태를 체크할 필요가 있다면 너가 명시적으로 리소스를 만들었을 때만 허용할게.<br />자동 생성된 내부 리소스에 대해서는 <b>우리가 알아서 관리함 ㅇㅇ 그니까 터치 ㄴ</b>

          이게 Reactor 팀의 일관된 태도임
          
    2. webClient 를 사용할 때, 요청이 시작되면 count 를 증가시키고 요청이 완료되면 count 를 감소시키게 한 후, count 가 0 일때 종료하기(<b>권장!!</b>)
       1. webClient 가 요청을 처리할 때 톰캣 스레드가 아닌 <b>reactor netty 의 이벤트 루프 스레드를 사용</b>한다는 것에 기반한 방식!
       2. 이 때는 AtomicInteger 를 사용해서 활성화된 스레드 개수를 체크해야 하면 좋음!
          1. 비동기적으로 스레드가 실행되기 때문에 같은 변수 동시에 접근할 수 있다는 문제가 있음.
          2. 그래서 원래 Lock 을 사용해야 하지만, AtomicInteger 사용하면 안정성 보장해줌
          3. CAS (Compare-And-Swap) 이라는 저수준 동기화 기법을 사용해서 <b>lock 을 걸지않고도 원자적으로 값을 수정할 수 있음! → 스레드 안정성 확보 가능!</b>
    3. 이벤트 루프를 spring 에서 직접 관리하는 방식(ReactorResourceFactory)
       1. 1번에서 문제는 netty 의 이벤트 루프에 직접 접근하는 것이 선언형 프로그래밍의 원칙에 위배된다는 것이었음.
       2. 그런데 실제로 개발을 하다 보니 직접 이벤트 루프를 컨트롤해야 하는 상황이 종종 발생함!
       3. 이를 해결하기 위해서 spring 2.3 부터는 <b>ReactorResourceFactory 를 통해 이벤트 루프를 직접 관리</b>할 수 있는 방식을 지원함.
       4. 이거 사용하려면 webClient 정의할 때 직접 생성한 이벤트 루프(LoopResources) 를 매개변수로 전달하면 됨!

### Q. 그럼 우리 서버는 어떻게 처리하는가?

```javascript
object RequestTracker {
    private val activeRequests = AtomicInteger(0)

    fun incrementActiveCount() {
        activeRequests.incrementAndGet()
    }

    fun decrementActiveCount() {
        activeRequests.decrementAndGet()
    }

    fun getActiveCount(): Int = activeRequests.get()
}
```

```javascript
while (RequestTracker.getActiveCount() > 0) {
```

- RequestTracker 를 만들어서 webClient 요청이 시작되면 카운트 증가, 요청이 끝나면 카운트 감소
- 종료하기 전에 카운트 체크해서 모든 webClient 요청이 종료되면 shutdown!
