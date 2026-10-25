---
layout: article
title: "리액티브 코드는 왜 '누구한테 받는지' 도 알아야 할까?"
permalink: /posts/reactive-upstream-downstream/
category: spring
topic: webflux-reactor
list_order: 12
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/23a3abb1a94d808baeead76321b5c8f0?pvs=204)

# 리액티브 코드는 왜 '누구한테 받는지' 도 알아야 할까?

[Notion 참조 블록: 원문에서 보기](https://app.notion.com/p/23a3abb1a94d808baeead76321b5c8f0#23a3abb1a94d80d8a27cdcddfc8890fb)

- 이전에 작성한 글에서 이런 이야기를 했었다.

> 체인 내부 연산자의 역할<br />각 연산자는 두 가지를 모두 알아야 한다.<br />upstream publisher : 누구에게 데이터를 받아야 하는가?<br />downstream subscriber : 누구에게 데이터를 넘겨야 하는가?

- 일반적인 코드에서는 누구한테 데이터를 주면 되는지만 신경쓰면 되는 반면,
- <b>리액티브 코드에서는 누구한테 데이터를 받는지도 알아야 한다.</b>
- 오늘은 이 이유에 대해 알아보고자 한다!

### 전통적인 데이터 처리 방식 : push only

- 전통적인 코드에서는, 현재 작업을 마친 후에 다음 코드로 데이터를 emit 하는 과정만이 필요했다.
- 이 방식에서는 producer 의 발행 속도를 consumer 가 따라잡지 못하는 문제가 발생할 수 있었다.
- 때문에 OOM 와 같은 장애의 원인이 될 수 있었다.
- 그래서! ‘consumer 가 처리 가능한 만큼만 데이터를 요청하게 하면 어떨까?’ 하는 생각에서 출발한 게 리액티브 코드이다.

### 리액티브 코드에서의 데이터 처리 방식 : push-with-demand (push && pull)

- 리액티브 코드에서는 pull 방식으로 데이터를 가져오는 것을 지원한다.
- 즉, 데이터를 소비하는 쪽에서 ‘이 만큼의 데이터만 줘!’ 하고 요청하는 게 가능해진 것!
  - 데이터를 소비하는 쪽(Subscriber)이 ‘이만큼만 줘!’라고 수요를 보내면, 그 요청량에 따라 데이터를 생산하는 쪽(Publisher)이 push 해주는 구조이다!
- 이걸 <b>Backpressure</b> 지원이라고 함!
  - 대부분의 연산자가 지원하는데, 지원 안 하는 연산자도 있음!
- 이 덕분에 전통적인 데이터 처리 방식에서 발생하는 문제를 해결할 수 있었다.
- <span color="blue">그래서 리액티브 체인의 연산자는 자기한테 데이터를 주는 주체(upstream publisher) 와, 자기가 데이터를 주어야 하는 주체(downstream subscriber) 를 모두 알아야 한다!</span>

### Q. 그러면 <b>Backpressure 과정에서 데이터를 요청하는 주체는 무엇인가? 모니터링하는 스레드가 있어서 요청을 하나?</b>

- 놉! 모니터링 스레드가 동작하는 방식이 아니라, 연산자 내부의 subscriber 가 미리 정의된 정책에 따라 요청 속도를 조절한다.
- 연산자 내부의 버퍼 상태를 체크해 유동적으로 발행 속도 조절을 요청하는 것!
- 모니터링 스레드를 따로 두지 않은 이유는, 이게 리액티브 철학인 ‘블로킹 스레드를 최소화하자!’ 에 어긋나기 때문
- 리액티브는 스레드를 아끼기 위해 논블로킹 방식을 도입했는데, 모니터링 스레드와 같은 부가적인 스레드를 두는 것은 이 철학에 어긋남!
- 그리고 연산자 내부의 버퍼 상태를 <span color="red">연산자 내부의 subscriber</span> 가 바로 체크하는 방식이 모니터링 스레드가 돌면서 체크하는 것보다 빠르고 비용도 적음!
- 참고 )<b> </b>Reactor ∙ RxJava 내부에는 버퍼 상태를 루프로 확인하거나, 특정 시점에 request 호출을 재개하는 scheduler-기반 작업이 있을 수 있음

### 참고 ) <span color="red">연산자 내부의 subscriber</span>

- 이전에 설명한 ‘역방향 구독’ 과정을 자세히 풀어서 설명하자면, 하위 연산자가 자기 역할을 하는 새로운 subscriber 인스턴스를 만들고, 이를 상위 연산자 내부의 subscriber 로 등록하는 거임!

### Q. 근데 저번에 설명할 때 subscribe() 를 만나기 전의 연산자는 힙에 객체로 생성된다고 하지 않았나? 이미 정의된 객체에 어떻게 새로운 필드를 정의함?

- 런타임에 필드를 새로 정의하는 게 아니라, 연산자 내부에 정의된 override 메서드를 통해 subscriber 를 전달하는 구조임!
- 예를 들어 FluxMap 은 이렇게 생김

```kotlin
class FluxMap<T, R>(
    private val source: Flux<T>,
    private val mapper: (T) -> R
) : Flux<R>() {
    override fun subscribe(actual: CoreSubscriber<in R>) {
        val subscriber = MapSubscriber(actual, mapper)
        source.subscribe(subscriber)
    }
}
```
