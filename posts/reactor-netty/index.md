---
layout: article
title: "Reactor Netty"
permalink: /posts/reactor-netty/
category: spring
topic: webflux-reactor
list_order: 17
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1c43abb1a94d80a3bdd3de1cec6a258f?pvs=204)

# Reactor Netty

## Reactor

- 리액티브 프로그래밍을 위한 비동기 스트림 처리 도구
- 논블로킹 + 스트림 기반 + 이벤트 반응형 으로 동작함
  - 스트림 기반
    - 데이터를 연속적인 흐름으로 보고 처리함
    - 데이터가 map, filter 등의 체인을 통과하며(파이프라인을 따라 흐르며) 처리되는 것!
    - 데이터를 한 번에 다 처리하는 게 아니라, 들어올 때마다 하나씩 처리함!
    - map, filter, delay, take 등의 연산자 체인을 통해 흐름을 <b>선언적</b>으로 제어할 수 있음!

      - 여기서 선언적이란?
        - 구체적으로 프로그래머가 ‘이렇게 저렇게 데이터 처리해줘!’ 라고 하는 게 아니라, ‘믿고 맏길게\~ 이 조건만 맞으면 처리해줘!’ 하는 것!
        - 어떻게 처리할지는 라이브러리가 결정하기 때문에 신경쓰지 않아도 되지만, <b>어떤 순서로 처리할지</b>는 프로그래머가 정해야 함!
    - 이렇게 통과된 데이터를 마지막에 subscribe() 가 받게 됨!
  - 이벤트 기반
    - subscribe() 후에만 실행됨
    - 반응적(콜백)으로 동작함
  - 논블로킹
    - 스레드를 점유하지 않음! (노는 시간이 없다\~)

## Netty

- nio 기반 고수준 네트워크 프레임워크
  - [<span style="color: blue">nio 소개</span>]({{ '/posts/java-nio/' | relative_url }})
- 한 개의 스레드가 수백 \~ 수천 개의 소켓 채널을 관리 가능!
- 이벤트 루프 기반으로 동작함!
  - [<span style="color: blue">이벤트 루프 설명</span>]({{ '/posts/event-loop/' | relative_url }})
  - nio 기반이기 때문에 이벤트 루프 패턴으로 구현할 수 있었다!

  1. 비동기로 동작하면서 http 요청을 보내고 응답을 받아오거나,
  2. 서버 모드일 때 http 요청을 받아서 처리하거나,
  3. tcp 통신을 리액티브 방식으로 가능하게 함!
- 두 가지의 스레드 그룹으로 동작
  1. Boss Group
     1. 클라이언트의 접속 수락
  2. Worker Group
     1. 실제 요청 처리 (읽기, 쓰기, I/O 이벤트 처리 등의 트랜잭션 수행)

## webClient 에서는 어떻게 동작하는가?

- webClient 는 내부적으로 reactor netty 를 사용함
  - reactor : 스트림 방식으로 데이터 처리
  - netty : 요청 보내고 받기
- 구체적으로 이렇게 동작한다.
  - 메인 스트림에서 webClient 가 호출되면

  1. 응답 데이터를 처리하기 위한 리액티브 체인(Publisher)이 생성된다.
     1. .map, .filter, bodyToMono() 등의 선언형 연산자 체인이 Mono/Flux 객체 형태로 만들어진다.
  2. subscribe() 호출하면 Subscriber 객체가 생성된다.
     1. 여기에는 이전에 생성한 Publisher 가 담겨있다.
  3. 생성된 Subscriber 객체는 netty 내부 ChannelHandler(HttpClientOperations 등) 에 전달되어, 응답이 도착했을 때 실행할 콜백으로 보관된다.
  4. 연결을 위한 tcp 소켓이 생성된다.
  5. 소켓 채널(소켓을 추상화 한 객체)이 selector 에 등록된다.
     1. OP\_CONNECT, OP\_READ 등 감지할 이벤트와 함께 selector에 등록됨
  6. 소켓 채널(5번에서의 소켓 채널과 같은 거)이 netty 이벤트 루프에 등록된다.
     1. 이벤트 루프 스레드가 요청을 전송하거나 콜백을 실행하기 위해 이벤트 루프에 등록됨!
  7. 요청이 전송된다.
  8. 응답이 오면, selector 가 감지하여 이를 이벤트 루프에 알린다.
  9. 이벤트 루프는 netty 핸들러에 등록된 Subscriber 를 호출한다.
  10. reactor 는 Subscriber 객체에 명시된 콜백에 따라 데이터를 처리한다.
  11. 처리된 데이터를 subscriber (여기서는 메인 스트림) 으로 넘긴다.

  <b>resource clean</b>

  {:start="12"}
  12. channel.close() 함수로 이벤트 루프에서 소켓 채널 종료가 시작된다.
      1. 소켓은 바로 닫힘!
      2. 채널은 종료 상태로 들어감. 실제로 종료되는 건 다음 이벤트 주기!
      3. graceful shutdown 방식을 사용하는 건, 현재 처리 중인 트랜잭션을 안전하게 처리하고 종료하기 위함! 이건 우리 서버도 마찬가지!
  13. SelectionKey.cancel() 가 자동으로 호출되어 selector 에서도 채널 종료가 시작된다.
      1. 얘도 종료 상태로 들어가는 것. 실제 종료되는 건 다음 이벤트 주기에서!
  14. 다음 이벤트 주기에서 소켓 채널이 이벤트 루프와 selector 에서 제거된다.
