---
layout: series-article
title: "소프트웨어 아키텍처 5주차"
permalink: /series/software-architecture/week-05/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 6
---

원문: [Notion에서 보기](https://app.notion.com/p/27e3abb1a94d80de848cf65310224d09?pvs=204)

# 소프트웨어 아키텍처 5주차

- 공통 기능은 분리해서 기저에 깔고(라이브러리), user service 에서 가져다 사용하게 하기
- 변형 가능하고 재사용 가능한 모듈 설게-

![]({{ '/series/software-architecture/assets/bedef36a-6f2f-46a2-9149-224eba95b5f6.png' | relative_url }})

- Reduce coupling and maximize cohesion to build systems that are scalable, manageable and can be extended over time

![]({{ '/series/software-architecture/assets/9652f477-5185-488b-a651-a24dc2e74827.png' | relative_url }})

### SOLID

- 5개 디자인 원리
  - Single Responsibility Principle (SRP)
  - Open Closed Principle (OCP)
  - Liskov Substitution Principle (LSP)
  - Interface Segregation Principle (ISP)
  - Dependency Inversion Principle (DIP)

### 인터페이스 나누기

![]({{ '/series/software-architecture/assets/68f034ac-51a4-4f23-ae03-7d58a8263203.png' | relative_url }})

- interface 에 잇는 걸 모두 구현하지 못하는 객체가 발생하지 않도록 인터페이스 분리하기

### Spring DI 원리

![]({{ '/series/software-architecture/assets/8f04e234-b193-49c4-9ea6-37922b995b47.png' | relative_url }})

- 일반적으로, 한 객체가 다른 객체를 참조하기 위해서는 요청하는 객체가 요청받는 객체의 주소를 알아야 함
- 스프링은 객체의 주소를 DI 컨테이너가 관리함
- 그래서 한 객체에서 다른 객체를 참조할 때 DI 컨테이너를 호출해서 참조할 객체의 주소를 가지고 오기 때문에 DI 라고 하는 거임

인터페이스 원리

SOLID Design Principles<br />• Single Responsibility Principle (SRP)<br />• Open Closed Principle (OCP)<br />• Liskov Substitution Principle (LSP)<br />• Interface Segregation Principle (ISP)<br />• Dependency Inversion Principle (DIP)

모듈 원리

GRASP Design Principles<br />• Low Coupling<br />• High Cohesion<br />• Expert<br />• Creator : 누가 누구 생성?<br />• Controller : 비즈니스에 존재하지 않는 디자인 객체<br />• Don’t talk to Stranger
