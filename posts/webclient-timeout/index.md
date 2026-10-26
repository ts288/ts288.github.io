---
layout: article
title: "webClient Timeout 정리"
permalink: /posts/webclient-timeout/
category: spring
topic: webflux-reactor
list_order: 16
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1c63abb1a94d80e18233ccb9a496418d?pvs=204)

# webClient Timeout 정리

Spring

- webClient 타임아웃 정리
  - 타임아웃은 발생한 시점에 따라서 정리 가능
  - request 를 보내고 response 를 받으려면 다음의 과정을 거쳐야 함
    1. 목적지와의 tcp 연결 수립 (handshake)
    2. request 보내기
    3. 서버에서 트랜잭션 처리
    4. response 받기
    5. 받은 response 를 tcp 소켓에서 application 레벨로 가져와서 처리
  - 타임아웃이 발생한 시점이
    1. 번 이라면 - connectTimeout
       1. tcp 3way handshaking 이 이뤄졌는가? 로 판단
    2. 번 이라면 - writeTimeout
       1. request 를 보내고 tcp ack 을 받았는가? 로 판단
  - responseTimeout 과 readTimeout 은 어느 단계에서 발생했는지 명확하게 알 수 없음!
    - 이 둘을 구분하는 기준은 ‘응답 헤더 (e.g. 200 ok) 가 도착했는가?’ 임!
    - <b>문제는 트랜잭션이 모두 처리되기 전에도 서버가 클라이언트에게 응답 헤더를 보낼 수 있다는 것! 그래서 이 두 가지 타임아웃이 발생한 경우에는 서버에서 트랜잭션이 처리되었음을 보장할 수 없다!</b>
      - 이렇게 설계한 이유는 크게 몇 가지가 있는데..
        1. 200 Ok 를 먼저 보내고 이후에 body 를 보낼 수 있게 하면, UX 경험 향상시킬 수 있음
        2. http 는 스트림 데이터 친화적으로 설계되어서 데이터를 나눠보낼 수 있음
        3. 비동기 작업에서 클라이언트의 대기 여부를 결정할 수 있기 때문<br />(e.g. 202 Accepted 로 받으면 ‘아직 처리는 안됐는데, 요청은 받음\~ 그니까 좀만 ㄱㄷ\~’ 이런 뜻이라서 클라이언트에서 기다리게 할 수 있음.)
    - <b>그래서 </b><b><span style="color: red">재시도</span></b><b>하게 되면 서버에서 동일한 트랜잭션이 한 번 더 수행될 수 있음!</b>

## Q. responseTimeout 은 응답 헤더를 못 받은 경우라면서요. 그럼 무조건 트랜잭션 처리 안된 거 아니에요?

- A. 그럴 가능성이 높은 건 사실이지만, 무조건 그렇다고 할 수 없음! 이론적으로는 트랜잭션이 처리되엇으나 responseTimeout 이 발생할 수 있음
  - e.g. 트랜잭션이 처리된 상태에서, 네트워크 문제로 응답을 보내지 못한 경우

    - 이 경우, 클라이언트는 응답 헤더를 받지 못하므로 responseTimeout 으로 판단한다.

## Q. 그럼 spring 에서는 내부적으로 어떻게 타임아웃 종류를 판단하나?

- A. webClient 에서 사용하는 reactor netty (비동기 논블로킹 라이브러리) 라는 친구가 처리해준다!
- netty 는 한국인이 만들었대!
  - [reactor netty 설명글]({{ '/posts/reactor-netty/' | relative_url }})
