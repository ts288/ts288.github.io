---
layout: article
title: "이벤트 루프"
permalink: /posts/event-loop/
category: spring
topic: webflux-reactor
list_order: 18
home_section: spring-webflux-reactor
home_section_title: "Spring · WebFlux·Reactor"
---

원문: [Notion에서 보기](https://app.notion.com/p/1c43abb1a94d80aeb7e5f096d1009f34?pvs=204)

# 이벤트 루프

- 요청을 보내거나, 응답을 받거나, 요청을 받아서 처리하는 등의 동작들을 이벤트로 등록하고, 스레드가 이벤트 루프를 돌면서 이벤트를 처리하는 방식!
- 기존 방식은 하나의 스레드가 하나의 트랜잭션을 끝까지 담당(블로킹 방식)해서, 요청을 보내고 응답을 받아오는 전체 과정을 담당함. 이 방식에서는 자원이 낭비되고 처리량이 제한됨(간단하게 생각하면 스레드의 개수가 한 번에 처리할 수 있는 요청의 개수)
- 이 방식이 개선된 것이, 스레드가 하나의 트랜잭션을 담당하는 게 아니라, 트랜잭션을 이벤트로 쪼개서 루프에 등록하고 스레드가 계속 루프를 순회하면서 처리하는 방식!
- 이 방식 덕분에 적은 수의 스레드로도 많은 양의 트랜잭션을 처리할 수 있다!

[Nio (new i/o)]({{ '/posts/java-nio/' | relative_url }})
