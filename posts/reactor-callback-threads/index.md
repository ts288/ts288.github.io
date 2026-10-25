---
layout: article
title: "Reactor에서 콜백 스레드 지정하기"
permalink: /posts/reactor-callback-threads/
category: spring
topic: webflux-reactor
list_order: 14
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1cc3abb1a94d80349f44db0fe2bcd52c?pvs=204)

# Reactor에서 콜백 스레드 지정하기

- 이벤트 루프 방식은 ‘적은 스레드로 많은 요청을 빠르게 처리할 수 있다’는 장점이 있다.
- 그런데 이 스레드가 무거운 작업을 수행하게 된다면?
  - 이벤트 루프 방식의 장점을 살릴 수 없게 됨!
- 그래서 reactor 는 구독과 콜백에 사용할 스레드를 설정할 수 있는 방법을 제공한다.
  - subscribeOn() 이랑 publishOn() 으로 지정할 수 있다!
    - 둘 모두 작업을 처리하는 스레드를 지정하는 연산자이다
    - subscribeOn 은 체인을 시작하는 시점에 로직을 처리할 스레드를 지정하는 연산자이다. 여기서 지정한 스레드는 publishOn() 으로 스레드가 변경되기 전까지 유지된다.
      - <b>cf ) </b><code>subscribeOn</code><b>은 체인 내에서</b> <b>가장 마지막(=subscribe()와 가장 가까운 위치)</b> 에 있는 것 <b>하나만 적용된다! 이건 구독 연결이 실행 시점에 역방향으로 진행되기 때문인데, 나중에 자세히 다루도록 하겠음</b>!
    - publishOn() 은 연산자 호출 시점 이후의 로직을 처리하는 스레드를 지정한다.

예제 코드를 보면서 학습해보자

```javascript
Mono.fromCallable { long_time_transaction() }   // ①
    .subscribeOn(Schedulers.boundedElastic()) // ②
    .map { 결과를_변형() }              // ③
    .publishOn(Schedulers.parallel())  // ④
    .subscribe { final_transaction() }      // ⑤
```

gpt 가 준 이런 코드가 있다.

이 코드는 세 가지 스레드에서 실행된다.

1. 메인 스레드 (webFlux 라면 reactor netty 이벤트 루프 스레드, mvc 라면 톰캣 스레드)
2. boundedElastic 스레드
3. parallel 스레드

각 스레드는 다음의 과정을 처리한다.

1. 메인 스레드는 Mono 의 전체 블럭을 읽고 코드를 <b>생성</b>한다.
   1. <b>주의 ) 코드를 생성만 하지 실행을 하진 않는다. 실행되는 시점은 subscribe() 되는 시점!</b>
2. boundedElastic 스레드는 <code>long_time_transaction</code> 를 처리한다.
   1. <code>subscribeOn</code> 에서 지정한 스레드로 체인의 처리를 시작하기 때문
3. map 도 boundedElastic 에서 처리한다.
   1. 아직 publishOn 으로 스레드가 변경되지 않았기 때문
   2. 만약 ③ 과 ④ 의 위치가 변경되어 publishOn 다음에 map 이 온다면 map 은 <code>parallel</code> 이 처리함
4. publishOn 이 호출되어 이후 트랜잭션을 처리하는 스레드가 <code>parallel</code> 으로 변경된다.
5. <code>parallel</code> 이 <code>final_transaction</code> 을 처리한다.
   1. 이 과정은 비동기로 동작하기 때문에 메인 스레드가 결과를 기다리지는 않는다.
   2. mvc + block 으로 동작했다면 메인 스레드가 결과를 <b>기다리게 할 수</b>도 있다.

### Q. 처리 중이던 트랜잭션을 다른 스레드로 넘기는 것은 어떻게 동작하는 것인가?

- reactor 가 scheduler(스레드 풀) 에서 적절한 worker(스레드를 캡슐화 한 객체) 를 선택해 작업을 위임함
  - subscribeOn(parallel) 이나 publishOn(parallel) 이 호출되면, 콜백이 명시된 스레드(여기서는 parallel) 의 스레드 풀(scheduler) 에 등록되고, 사용 가능한 worker 를 reactor 가 선택해서 로직을 처리하게 함
  - reactor 에는 dispatcher 가 없기 때문에, subscribeOn 이나 publishOn 으로 scheduler 를 지정하면, scheduler 가 dispatcher 의 역할을 수행함
- 스레드가 바뀌더라도 이어서 값을 처리할 수 있는 것은!
  - 체인과 체인 사이가 onNext(value) 로 이어지고, value 안에 현재까지 처리된 데이터가 담겨서 넘어가기 때문임! (물 흐르듯이 자연스럽게\~ reactor 하게!)

### Q. 어떤 스레드를 사용할 수 있는가?

<table>
<tr>
<td>
이름
</td>
<td>
특징
</td>
<td>
사용 목적
</td>
</tr>
<tr>
<td>
<code>Schedulers.immediate()</code>
</td>
<td>
현재 스레드에서 실행
</td>
<td>
디버깅 또는 테스트용
</td>
</tr>
<tr>
<td>
<code>Schedulers.single()</code>
</td>
<td>
단일 스레드에서 실행
</td>
<td>
순차 실행 보장
</td>
</tr>
<tr>
<td>
<code>Schedulers.parallel()</code>
</td>
<td>
CPU 코어 수만큼 스레드 생성
</td>
<td>
CPU 집약적 작업
</td>
</tr>
<tr>
<td>
<code>Schedulers.boundedElastic()</code>
</td>
<td>
필요 시 최대 10배까지 확장 가능한 스레드 풀
</td>
<td>
블로킹 I/O (DB, 파일 등)
</td>
</tr>
<tr>
<td>
<code>Schedulers.newSingle("name")</code>
</td>
<td>
새 단일 스레드 생성, 커스텀 이름 지정 가능
</td>
<td>
커스텀 단일 스레드 용도
</td>
</tr>
<tr>
<td>
<code>Schedulers.newBoundedElastic()</code>
</td>
<td>
기본 boundedElastic보다 세부 설정 가능, 스레드 수 제한 가능
</td>
<td>
사용자 정의 블로킹 I/O 처리 스레드 풀
</td>
</tr>
</table>

### Q. 현재 사용하는 스레드를 알고 싶다면?

- <code>println(Thread.currentThread().name)</code> 이거 사용해서 스레드 이름 찍어볼 수 있음!
