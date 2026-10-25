---
layout: article
title: "Spring WebSocket, NIO, STOMP, TCP 버퍼 동작 정리"
permalink: /posts/spring-websocket-nio-stomp/
category: spring
topic: general
list_order: 10
home_section: spring-general
home_section_title: "Spring · 일반"
---

원문: [Notion에서 보기](https://app.notion.com/p/3b23abb1a94d80719a8ce43bba7b3b52?pvs=204)

# Spring WebSocket, NIO, STOMP, TCP 버퍼 동작 정리

## 1. WebSocket 연결 하나로 여러 도메인과 채팅을 처리할 수 있는가

가능하다.

WebSocket 연결은 사용자마다 하나만 유지하고, 그 위에서 STOMP destination을 나눠 여러 종류의 메시지를 처리하면 된다.

```txt
ws://server/ws
```

하나의 물리 연결 위에서 다음과 같이 논리 채널을 나눌 수 있다.

```txt
/app/rooms/{roomId}/chat
/topic/rooms/{roomId}/chat
/topic/rooms/{roomId}/events
/user/queue/events
```

예를 들면 다음과 같다.

- <code>/app/rooms/{roomId}/chat</code>
  - 클라이언트가 서버로 채팅 메시지를 보낼 때 사용한다.
- <code>/topic/rooms/{roomId}/chat</code>
  - 해당 방의 채팅 메시지를 구독할 때 사용한다.
- <code>/topic/rooms/{roomId}/events</code>
  - 방 참여, 사진 등록, 남은 장수 변경 등의 방 단위 이벤트를 받는다.
- <code>/user/queue/events</code>
  - 특정 사용자에게만 전달되는 개인 알림을 받는다.

즉, 채팅용 WebSocket과 방 이벤트용 WebSocket을 따로 열 필요는 없다.

```txt
물리 연결: 하나
논리 채널: 여러 개
```

다만 연결은 하나를 공유하더라도 서버 내부의 컨트롤러와 비즈니스 책임은 도메인별로 분리하는 것이 좋다.

```txt
WebSocketConfig
├── ChatWebSocketController
├── RoomWebSocketController
└── NotificationWebSocketController
```

---

## 2. WebSocket도 연결을 계속 유지하면 스레드를 계속 점유하는가

그렇지 않다.

Tomcat이 NIO Connector를 사용한다면 WebSocket 연결마다 Tomcat 작업 스레드 하나가 계속 붙어 있는 구조는 아니다.

```txt
WebSocket 연결 유지 중
→ OS와 Tomcat NIO가 소켓 상태를 관리
→ 평소에는 작업 스레드를 점유하지 않음
→ 데이터가 들어오거나 전송할 작업이 생길 때만 스레드가 사용됨
```

SSE도 기본 원리는 비슷하다.

```txt
SSE
→ HTTP 연결을 열어둠
→ 유휴 상태에서는 요청 스레드를 반환
→ 이벤트를 보낼 때만 작업 수행

WebSocket
→ HTTP Upgrade 이후 WebSocket 연결 유지
→ 유휴 상태에서는 연결만 유지
→ 메시지를 송수신할 때만 작업 수행
```

둘 다 연결당 스레드 하나를 계속 점유하지는 않지만, 연결마다 다음 자원은 유지한다.

- TCP 소켓
- 파일 디스크립터
- 커널 송수신 버퍼
- WebSocket 세션 객체
- 인증 사용자 정보
- STOMP 세션과 구독 정보
- heartbeat 상태
- 전송 대기 메시지

따라서 연결 수가 많아지면 메모리와 소켓 자원은 증가한다.

---

## 3. NIO가 담당하는 일과 Spring이 담당하는 일

NIO가 WebSocket과 STOMP의 모든 처리를 담당하는 것은 아니다.

전체 흐름은 다음과 같이 나뉜다.

```txt
클라이언트
    ↓
TCP 패킷
    ↓
OS Kernel
    ↓
Tomcat NIO
    ↓
WebSocket 프레임 처리
    ↓
Spring STOMP 처리
    ↓
@MessageMapping 또는 메시지 브로커
```

### Tomcat NIO의 역할

Tomcat NIO는 주로 다음을 담당한다.

- 다수의 소켓 연결 감시
- 읽을 수 있는 소켓 감지
- 쓸 수 있는 소켓 감지
- 실제 바이트 읽기와 쓰기 연계
- 연결 상태 관리

NIO는 대략 다음 정도를 감지한다.

```txt
이 소켓에서 읽을 데이터가 생겼다.
이 소켓에 다시 쓸 수 있다.
```

### WebSocket 계층의 역할

WebSocket 계층은 바이트 스트림을 WebSocket 프레임으로 해석한다.

```txt
바이트
→ TEXT / BINARY / PING / PONG / CLOSE 프레임
→ payload 추출
```

### Spring STOMP 계층의 역할

STOMP를 사용하는 경우 Spring이 다음을 처리한다.

- <code>SEND</code>, <code>SUBSCRIBE</code>, <code>CONNECT</code>, <code>DISCONNECT</code> 등의 command 파싱
- destination 파싱
- 구독 정보 관리
- 메시지 라우팅
- heartbeat 처리
- <code>@MessageMapping</code> 호출
- simple broker를 통한 구독자 전송

즉 다음 항목은 NIO 자체의 책임이 아니다.

```txt
STOMP 프레임 파싱
destination 관리
채널 구독 관리
heartbeat 협상과 감시
```

이 작업들은 Spring 애플리케이션의 CPU와 메모리를 사용한다.

---

## 4. 실제 WebSocket 메시지를 처리하는 스레드는 무엇인가

이 부분은 단계별로 보는 것이 정확하다.

```txt
Tomcat Poller
    ↓
Tomcat Container Thread
    ↓
Spring WebSocket 처리
    ↓
Spring clientInboundChannel
    ↓
@MessageMapping
```

### Tomcat Poller Thread

Poller는 연결을 감시하고 I/O 이벤트를 감지한다.

```txt
읽을 데이터가 있음
쓸 수 있음
연결이 종료됨
```

Poller가 비즈니스 로직을 직접 실행하는 것은 아니다.

### Tomcat Container Thread

소켓에서 실제 데이터가 들어오면 Tomcat 측 스레드가 WebSocket 프레임을 읽고 컨테이너의 WebSocket 처리 계층으로 전달한다.

### Spring 메시징 스레드

Spring STOMP를 사용하면 들어온 메시지가 보통 <code>clientInboundChannel</code>로 전달된다.

<code>clientInboundChannel</code>은 자체 <code>TaskExecutor</code>를 사용할 수 있기 때문에, <code>@MessageMapping</code> 비즈니스 로직이 항상 처음 데이터를 읽은 Tomcat 스레드에서 끝까지 실행된다고 단정하면 안 된다.

정확한 표현은 다음과 같다.

```txt
Tomcat 스레드가 WebSocket 프레임 수신을 시작하고,
Spring 메시징 채널에 전달된 이후에는
Spring의 inbound channel executor가 STOMP 메시지와 @MessageMapping을 처리할 수 있다.
```

따라서 다음처럼 이해하는 것이 좋다.

```txt
Tomcat NIO / Container
→ 네트워크 수신과 WebSocket 프레임 전달

Spring clientInboundChannel
→ STOMP 메시지 라우팅과 @MessageMapping 실행
```

---

## 5. heartbeat가 들어오면 누가 처리하는가

heartbeat도 연결을 유지한 채 스레드 하나가 계속 기다리는 방식은 아니다.

클라이언트의 STOMP heartbeat가 들어오는 흐름은 대략 다음과 같다.

```txt
heartbeat 도착
    ↓
Tomcat NIO가 읽기 가능 상태 감지
    ↓
WebSocket 데이터 읽기
    ↓
Spring STOMP가 heartbeat로 해석
    ↓
마지막 수신 시간 또는 세션 상태 갱신
    ↓
작업 종료
```

heartbeat를 보내거나 timeout을 검사하는 작업은 Spring의 scheduler와 STOMP broker 관련 컴포넌트가 담당할 수 있다.

즉 heartbeat에는 두 종류의 작업이 있다.

### heartbeat 수신

```txt
네트워크에서 데이터 도착
→ Tomcat/WebSocket 계층
→ Spring STOMP 처리
```

### heartbeat 송신 및 timeout 검사

```txt
Spring TaskScheduler
→ 주기적으로 heartbeat 송신
→ 마지막 수신 시각 검사
→ timeout이면 세션 종료
```

heartbeat 때문에 사용자 연결마다 전용 스레드가 생기는 것은 아니다. 공용 스케줄러와 메시지 처리 스레드가 짧게 작업한다.

---

## 6. 느린 클라이언트 때문에 메시지 큐가 쌓인다는 의미

서버가 메시지를 생성하는 속도보다 해당 클라이언트에게 실제로 전송할 수 있는 속도가 느리면 전송 대기 데이터가 쌓일 수 있다.

예를 들어 서버는 초당 100개의 이벤트를 생성하지만 클라이언트 쪽 연결은 초당 20개만 처리할 수 있다고 하자.

```txt
1초:
100개 생성
20개 전송
80개 대기

2초:
100개 추가 생성
20개 전송
160개 대기
```

이때 대기 데이터는 한 군데에만 존재하는 것이 아니다.

```txt
Spring outbound channel queue
Tomcat/WebSocket 전송 버퍼
OS TCP send buffer
```

상황에 따라 이 여러 계층 중 하나 또는 여러 곳에서 적체될 수 있다.

중요한 점은 느린 클라이언트 때문에 반드시 Tomcat 작업 스레드가 계속 점유되는 것은 아니라는 것이다.

Non-blocking I/O에서는 소켓이 다시 쓸 수 있는 상태가 될 때까지 데이터와 상태를 기억한 뒤 스레드는 반환할 수 있다.

```txt
메모리 사용량 증가 가능
전송 지연 증가 가능
전용 스레드가 계속 점유되는 것은 아님
```

---

## 7. 메시지가 느리게 내려간다는 것은 TCP ACK가 늦다는 뜻인가

TCP ACK가 늦는 것은 대표적인 원인 중 하나다. 하지만 정확히는 다음 상황 전체를 의미한다.

```txt
서버가 생성하는 데이터 양
>
현재 TCP 연결이 전송할 수 있는 데이터 양
```

이런 상태가 되는 원인은 다음과 같다.

### 네트워크 지연 또는 대역폭 부족

전송 속도가 낮으면 ACK가 돌아오는 속도도 느려질 수 있다.

### 패킷 유실

패킷이 유실되면 TCP가 재전송해야 한다.

재전송이 완료되고 ACK가 올 때까지 해당 데이터는 송신 버퍼에 남는다.

### 상대방 Receive Window 부족

상대 커널의 수신 버퍼가 가득 차면 상대가 작은 receive window 또는 zero window를 광고할 수 있다.

```txt
ACK는 도착함
하지만 지금은 더 보내지 말라는 상태
```

이 경우 ACK가 오더라도 새로운 데이터를 충분히 보내지 못한다.

### TCP 혼잡 제어

네트워크 혼잡이 감지되면 TCP가 congestion window를 줄여 전송량을 제한한다.

즉 다음 표현이 가장 정확하다.

```txt
ACK가 늦거나,
수신 윈도우가 작거나,
혼잡 윈도우가 줄거나,
재전송이 발생해
커널 send buffer가 충분히 비워지지 않는 상태
```

---

## 8. 서버는 클라이언트가 메시지를 받았는지 어떻게 아는가

계층별로 구분해야 한다.

### TCP ACK

TCP ACK는 상대방 OS 커널이 해당 TCP 바이트를 수신했다는 뜻이다.

```txt
서버 Kernel
    ↓ TCP 데이터
클라이언트 Kernel
    ↓ TCP ACK
서버 Kernel
```

하지만 TCP ACK는 다음을 의미하지 않는다.

```txt
브라우저 JavaScript가 메시지를 읽었다.
앱이 메시지를 처리했다.
사용자가 화면에서 확인했다.
```

### WebSocket 계층

WebSocket <code>send()</code>나 Spring의 <code>convertAndSend()</code>가 성공했다고 해서 클라이언트 애플리케이션 처리까지 완료된 것은 아니다.

대략 다음 정도만 알 수 있다.

```txt
Spring이 메시지를 전송 계층에 전달했다.
또는 로컬 전송 큐에 등록했다.
```

### 애플리케이션 ACK

채팅에서 실제 수신 여부나 읽음 여부가 필요하다면 클라이언트가 별도 메시지를 보내야 한다.

```txt
TCP ACK
→ 상대 커널이 바이트를 받음

MESSAGE_RECEIVED
→ 클라이언트 앱이 메시지를 처리함

MESSAGE_READ
→ 사용자가 메시지를 읽음
```

예를 들어 다음과 같은 별도 이벤트를 설계할 수 있다.

```json
{
  "type": "MESSAGE_RECEIVED",
  "messageId": 123
}
```

```json
{
  "type": "MESSAGE_READ",
  "messageId": 123
}
```

---

## 9. send buffer는 언제 비워지는가

TCP send buffer는 애플리케이션이 <code>send()</code>를 호출하자마자 모두 삭제되는 것이 아니다.

```txt
애플리케이션
    ↓ write/send
TCP send buffer
    ↓ 네트워크 전송
상대 TCP
    ↓ ACK
송신 측 TCP가 ACK된 바이트 제거
```

TCP에서는 상대가 특정 sequence number까지 받았다고 ACK하면 해당 범위의 데이터는 재전송할 필요가 없어지므로 송신 버퍼에서 제거할 수 있다.

다만 다음 데이터를 얼마나 더 보낼 수 있는지는 별도 조건도 함께 영향을 준다.

- 상대 receive window
- congestion window
- 네트워크 RTT
- 패킷 유실과 재전송
- 로컬 소켓 버퍼 크기

즉 다음 두 가지는 구분해야 한다.

```txt
이미 전송한 데이터를 send buffer에서 제거하는 조건
→ ACK

새로운 데이터를 얼마나 더 보낼 수 있는지
→ receive window + congestion window 등
```

---

## 10. HTTP도 느릴 수 있는데 WebSocket에서 적체가 더 문제 되는 이유

HTTP도 TCP 위에서 동작하므로 느린 클라이언트 때문에 응답 전송이 지연될 수 있다.

따라서 느린 전송 문제 자체가 WebSocket만의 문제는 아니다.

차이는 메시지 수명과 연결 사용 방식이다.

### 일반적인 HTTP 요청

```txt
Request
    ↓
Response 하나
    ↓
해당 요청 완료
```

느린 응답이 생겨도 보통 그 요청의 응답 범위 안에서 끝난다.

HTTP keep-alive로 TCP 연결을 재사용하더라도 각 요청과 응답에는 명확한 경계가 있다.

### WebSocket

```txt
하나의 TCP 연결
    ↓
메시지 1
    ↓
메시지 2
    ↓
메시지 3
    ↓
계속 이어짐
```

연결이 살아 있는 동안 서버는 계속 새 메시지를 생성할 수 있다.

앞선 데이터가 충분히 전송되지 않은 상태에서 새로운 이벤트가 계속 발생하면 뒤의 메시지가 계속 대기한다.

```txt
메시지 1 전송 지연
→ 메시지 2 대기
→ 메시지 3 대기
→ 큐 증가
```

따라서 사용자의 표현대로 다음과 같이 이해할 수 있다.

```txt
HTTP:
대부분 요청 하나에 응답 하나가 있고 완료 범위가 명확함

WebSocket:
하나의 장기 TCP 스트림 위로 메시지가 계속 내려가므로
앞의 전송이 밀리면 뒤의 메시지도 같은 연결에서 밀릴 수 있음
```

단, HTTP/1.1 pipelining이나 HTTP/2 multiplexing 등은 세부 동작이 다르므로 “HTTP는 항상 응답 하나라 절대 밀리지 않는다”라고 일반화하면 안 된다.

---

## 11. 하나의 WebSocket 연결에서 앞 메시지가 밀리면 뒤 메시지도 밀리는가

같은 WebSocket 연결은 하나의 TCP 연결 위에서 동작한다.

TCP는 바이트 순서를 보장한다.

```txt
메시지 A
메시지 B
메시지 C
```

가 같은 연결로 순서대로 기록되었다면 앞부분의 TCP 데이터가 전송되지 않은 상태에서 뒷부분만 독립적으로 먼저 완료될 수는 없다.

따라서 같은 연결에서는 앞쪽 데이터의 전송 지연이 뒤쪽 데이터에도 영향을 준다.

```txt
같은 TCP 연결 내부
→ 앞 데이터가 막히면 뒤 데이터도 대기
```

다만 서로 다른 TCP 연결은 각자 소켓 버퍼와 TCP 상태를 가지므로 한 연결의 지연이 다른 연결의 전송 순서를 직접 막지는 않는다.

---

## 12. 커널의 TCP 큐와 UDP 큐는 어떻게 만들어지는가

커널이 애플리케이션마다 큐 하나를 두는 방식은 아니다.

기본적으로 <b>소켓별</b>로 송수신 버퍼와 프로토콜 상태를 관리한다.

```txt
프로세스
├── TCP Socket A
│   ├── send buffer
│   ├── receive buffer
│   └── TCP 상태
├── TCP Socket B
│   ├── send buffer
│   ├── receive buffer
│   └── TCP 상태
└── UDP Socket C
    ├── send buffer
    └── receive buffer
```

같은 애플리케이션도 여러 소켓을 열 수 있다.

```txt
애플리케이션 하나
→ 소켓 여러 개
→ 소켓마다 독립된 버퍼
```

TCP 소켓마다 다음 상태도 별도로 유지된다.

- sequence number
- ACK number
- receive window
- congestion window
- RTT 추정치
- 재전송 타이머
- send buffer
- receive buffer

그래서 TCP 연결 A가 느리더라도 TCP 연결 B는 독립적으로 전송할 수 있다.

---

## 13. TCP와 UDP는 버퍼를 비우는 기준이 어떻게 다른가

### TCP

TCP는 신뢰성 있는 바이트 스트림이다.

```txt
송신 버퍼에 저장
→ 상대에게 전송
→ ACK 수신
→ ACK된 데이터 제거
```

ACK가 오지 않으면 재전송할 수 있도록 데이터를 유지해야 한다.

### UDP

UDP는 데이터그램을 네트워크 계층으로 넘긴 뒤 전달 성공을 보장하지 않는다.

```txt
애플리케이션 send
→ UDP 데이터그램 생성
→ IP/NIC 전송 경로로 전달
→ 로컬 전송 처리가 끝나면 버퍼 해제 가능
```

UDP에는 TCP와 같은 다음 기능이 없다.

- 연결 단위 ACK
- 자동 재전송
- 순서 보장
- 흐름 제어
- TCP식 혼잡 제어

따라서 UDP는 상대가 실제로 받았는지 확인하지 않고 로컬 네트워크 스택이 데이터그램 처리를 넘기면 해당 버퍼를 해제할 수 있다.

---

## 14. 전체 흐름 요약

### 수신 경로

```txt
클라이언트
    ↓
TCP
    ↓
Tomcat NIO Poller
    ↓
Tomcat WebSocket 처리
    ↓
Spring clientInboundChannel
    ↓
STOMP 파싱
    ↓
heartbeat 처리 또는 @MessageMapping 호출
```

### 송신 경로

```txt
비즈니스 로직
    ↓
SimpMessagingTemplate.convertAndSend()
    ↓
Spring broker / clientOutboundChannel
    ↓
WebSocket 전송 계층
    ↓
OS TCP send buffer
    ↓
네트워크
    ↓
클라이언트 TCP ACK
```

### 수신 완료의 단계

```txt
TCP ACK
→ 상대 커널이 바이트 수신

애플리케이션 수신 ACK
→ 클라이언트 앱이 메시지 처리

읽음 ACK
→ 사용자가 메시지 확인
```

---

## 15. 현재 프로젝트에서의 결론

현재 서비스에서는 사용자마다 WebSocket 연결 하나를 열고 다음 채널을 함께 사용하는 방식이 적절하다.

```txt
/ws

/app/rooms/{roomId}/chat
/topic/rooms/{roomId}/chat
/topic/rooms/{roomId}/events
/user/queue/events
```

유휴 WebSocket 연결은 연결마다 Tomcat 스레드를 계속 점유하지 않는다.

다만 다음 비용은 존재한다.

```txt
소켓과 파일 디스크립터
커널 송수신 버퍼
WebSocket/STOMP 세션
구독 정보
heartbeat 상태
Spring inbound/outbound 작업
느린 클라이언트의 전송 대기 메시지
```

따라서 실제 운영에서 중요한 것은 단순한 연결 수만이 아니다.

- 동시 WebSocket 세션 수
- 세션별 구독 수
- inbound/outbound 처리량
- 메시지 큐 크기
- 전송 버퍼 크기
- 느린 세션의 전송 지연
- heartbeat timeout
- 세션당 최대 메시지 크기
- 세션당 최대 전송 대기 시간

특히 채팅처럼 메시지가 지속적으로 생성되는 기능에서는 느린 클라이언트 때문에 동일 연결의 뒤 메시지가 쌓이지 않도록 전송 제한, timeout, 버퍼 크기 제한, 세션 종료 정책 등을 함께 두는 것이 좋다.
