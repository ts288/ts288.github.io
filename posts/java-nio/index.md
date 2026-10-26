---
layout: article
title: "Nio (new i/o)"
permalink: /posts/java-nio/
category: spring
topic: webflux-reactor
list_order: 19
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1c43abb1a94d804584c2cfcac8fb2cec?pvs=204)

# Nio (new i/o)

- nio 는 효율적인 입출력(i/o) 처리를 위해 자바에서 고안된 저수준 api 이다. 그래서 new i/o 임!
- <b>하나의 스레드가 여러 연결을 감시</b>할 수 있다. (selector 기반)
- 데이터가 들어올 때만 처리한다. (논블로킹)
- 읽기, 쓰기 이벤트만을 감지하여 처리한다. (i/o)
- 비동기 논블로킹 처리를 위한 요소는 다음과 같다.
  - selector, channel, bytebuffer
- 이 기술 덕분에 이벤트 루프 같은 데이터 처리 패턴 구현이 가능해짐!

Q. 이름이 new i/o 이면, 이전에 사용하던 i/o 처리 api 도 있는가?

- 있다! Bio (Blocking i/o) 라고 함!
- <b>하나의 스레드가 하나의 연결</b>을 감시하는 구조!
- 이 때문에, 효율적인 처리가 불가능하다는 문제가 있었다!
  - 스레드가 대기하거나 점유되는 경우가 발생! 그래서 많은 연결을 효율적으로 관리할 수 없음!
