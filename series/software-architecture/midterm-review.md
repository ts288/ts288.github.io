---
layout: series-article
title: "소프트웨어 아키텍처 중간고사 정리"
permalink: /series/software-architecture/midterm-review/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 8
---

원문: [Notion에서 보기](https://app.notion.com/p/2903abb1a94d8083a43dc2353dc2b3bb?pvs=204)

# 소프트웨어 아키텍처 중간고사 정리

<details markdown="1">
<summary>일어나서 확인</summary>

4주차 실습 예제 추가된 거 확인 시험에 나올 거 같음

실습 MVC 패턴 어떻게 구현해둿는지

외워야 할 거 외우기

링크

[UML 2.5 Diagrams Overview](https://www.uml-diagrams.org/uml-25-diagrams.html)

[FREE Learning Resources: UML, Agile, TOGAF, PMBOK, BPMN](https://www.visual-paradigm.com/guide/)

[What is Use Case Diagram?](https://www.visual-paradigm.com/guide/uml-unified-modeling-language/what-is-use-case-diagram/)

</details>

## OOP 의 4가지 특성

### 상속

### 다형성

- 동일한 인터페이스를 통해 다양한 유영한 객체에 엑세스 가능
- Java override

### 추상화

- 구현 세부 정보 숨기고 기능만 표기

### 캡슐화

- 데이터와 메서드를 하나의 단위로 묶는 것

## 반복적 소프트웨어 개발 프로세스

### UP

- 대표적인 반복적 소프트웨어 개발 프로세스
- 여기에 Agile 같은 방식이 적용 가능함

---

## 소프트웨어 개발

### 세부

- System (문제)
- Model (아키텍처)
- Software (솔루션)

### 4+1 View

![]({{ '/series/software-architecture/assets/be5dce5c-021f-43b7-ad9d-c5bd71d9aeee.png' | relative_url }})

![]({{ '/series/software-architecture/assets/f31e810d-2359-4595-99f5-05e65a9545f1.png' | relative_url }})

- 제일 유명한 방법
- 모든 게 UseCase 에서 출발함
- Logical View
  - 기능
- Deployment View
  - 품질
  - 어느 하드웨어에 탑재할 것인가?
- Process View
  - 성능
- Implementation View

### 아키텍처 모델링의 중요성에 영향을 미치는 요소

- 점차적으로 시스템이 만들어야 할 기능이 복잡해 지고 있다.
- 예전과 비교해 휠씬 더 높은 소프트웨어 제품의 품질을 요구하고 있다

### 개발 프로세스

- 일단 무슨 기능이 필요한지 다 알아야 함
- 기술적으로 가능한지 검토해야함
- 각자 생각하는 컨셉과 도메인이 달라. 그리고 자주 변경도 돼
- 그래서 그 부분을 가려버리는 게 추상화임

### OOA/D

- OOA (객체지향 분석)
  - 문제 도메인의 문제를 분석
  - 도메인 개념(객체로 만들 수 있는 가능성) 을 찾고 설명
- OOD (객체지향 설계)
  - 설계모델을 객체지향구조로 만드는 단계
  - 유스케이스를 수행하기 위해 객체들이 어떻게 협력하는지 정의

### OOA/D 설계 과정

1. 유스케이스 정의
   1. 사용자가 시스템을 사용하는 경우(이름, 흐름, 시나리오 포함)
   2. 유스케이스 : 도메인 프로세스가 무엇인가?
2. 도메인 모델 정의
   1. 도메인에서 주목할 개념, 속성, 연관관계 식별
   2. conceptual 모델 : 개념이 무엇인가? 를 추출해야함
3. 인터렉션 다이어그램 정의
   1. 객체의 책임이나 다른 객체와의 협동 정의
   2. 동적인 시각에서 정의
   3. e.g. 시퀀스 다이어그램, 커뮤니케이션 다이어그램

![]({{ '/series/software-architecture/assets/d9ccd5ea-c52a-47f4-bb9e-5739c86e8ed4.png' | relative_url }})

4. 설계 클래스 다이어그램 정의
   1. 설계 클래스 다이어그램 : 객체의 속성, 메서드로 표현된 클래스 정의
   2. (<b>정적인 시각</b>)

   ![]({{ '/series/software-architecture/assets/8d4810c3-e003-4f6e-bf8b-2cad2253a736.png' | relative_url }})

시스템 시퀸스 다이어그램 : 시스템 이벤트랑 오퍼레이션이 무엇인가?

Contracts : 시스템 오퍼레이션이 무엇을 하는가?

### 정적 모델

- Class Diagram, Component Diagram, Package Diagram, Deployment Diagram

### 동적 모델

- Use Case Diagram, Activity Diagram, Sequence Diagram, Communication Diagram

## UseCase View

### Activity Diagram

![]({{ '/series/software-architecture/assets/23378f47-c5a0-44f2-8793-5adf7b75128a.png' | relative_url }})

- 구체적인 클래스를 정의하기 전에, 프로세스 흐름을 정리한 것

### Information Flow Diagram

![]({{ '/series/software-architecture/assets/124b70be-95dd-49a0-b04c-661ed9aec7c6.png' | relative_url }})

- 정보의 흐름을 표현. 정확하지 않아도 됨

### UseCase Diagram

![]({{ '/series/software-architecture/assets/0e1d1fe9-d3b2-4060-a986-ba578c6a35ea.png' | relative_url }})

- 제일 많이 사용하는 다이어그램

## Logical & Process View

### Communication (Collaboration) Diagram

![]({{ '/series/software-architecture/assets/65717aa6-2f28-419a-a93e-78f8809f2408.png' | relative_url }})

- 누가 누구를 연결시키는지 쉽게 확인할 수 있다.
- 문제가 발생했을 때 어디를 고쳐야 할지 쉽게 확인할 수 있다.
- 순서를 보기는 어려운데, 아키텍처를 보기는 쉬움

### Sequence Diagram

![]({{ '/series/software-architecture/assets/0c408313-fef7-46b0-8068-08cc93e5b961.png' | relative_url }})

- 한 눈에 누가 누구를 연결하는지 보이지는 않지만, 프로세스를 처리하는데 걸리는 시간을 확인할 수 있음
- 그래서 병목 지점을 확인하고 성능 개선을 할 때 좋음
- 순서를 보기는 좋은데, 아키텍처 보기가 어려움

### Conceptual(Domain) Model Class Diagram

![]({{ '/series/software-architecture/assets/8eae0667-ff2e-4671-bc48-55647a1b8a37.png' | relative_url }})

### Implementation Model Class Diagram

![]({{ '/series/software-architecture/assets/ae86ff03-14fc-430c-bbb1-1b609e4c65cf.png' | relative_url }})

- 상속 관계 표현
- 인터페이스가 뭐고, 구현체가 뭔지 알 수 있음

![]({{ '/series/software-architecture/assets/3b0d40ea-1065-4954-ada8-89bb87ccc614.png' | relative_url }})

---

## UP

- Iterative, Evolutionary, and Agile 한 방법
- 대표적인 반복적 개발 프로세스
  - 반복 & 점진적인 개발
  - Iteration 이라는 짧은 기간에 프로젝트 반복해 완성하는 방식
  - 점진적인 성장, 명세 및 설계의 진화를 목적으로 함
- 유연하고 확장 가능함
- 장점
  - 실패 낮음, 생산 향상, 적은 오류
  - 높은 위험성을 초기에 경감
  - 초기단계부터 개발 진행되어서 진척도 확인 가능
  - 복잡성 관리해 분석 어려움이나 복잡한 활동으로 지치지 않음

![]({{ '/series/software-architecture/assets/bf29a818-a9da-4c9d-a167-174d4ab9dcac.png' | relative_url }})

- 단계마다 초점이 달라짐
  - Inception Phase
    - 모델링의 요구사항
    - 시스템이 뭘 해야 하는가?
    - AS-IS 문제점 분석하고, TO-BE 만들어서 요구사항 식별하고 시스템 범위를 결정함
  - Elaboration Phase
    - 설계에 초점
    - UI 먼저 만들고, 그 다음에 아키텍처 핵심 부분 만들어
    - 요구사항 만족을 위해 시스템 분석하고 설계함
    - 베이스라인 아키텍처!!를 작성
  - Construction Phase
    - 설계된 컴포넌트(구성요소)들을 구현하고 테스트해서 실행 가능한 시스템 만드는 단계
    - 전체를 서브로 나누고, 서브 각각을 구현
  - Transition Phase
    - 제품을 사용자에게 인도

### UP 마일스톤

- 각 단계가 끝나면 무엇이 나와야 하는가?
- Inception 이후
  - 비전과 스코프가 나와야 함
  - TO-BE 모델에는 차별화가 어떻게 들어가나? 어떤 게 강조되나?
- Elaboration 이후
  - 뼈대가 되는 아키텍처가 나와야 함<b>(베이스라인 아키텍처)</b>
- Construction 이후
  - 실제로 딜리버리 할 수 있는 프로덕트가 나와야 함
  - 요구 사항에 대한 구현이 되어야 함
    - 근데 모든 요구 사항 만족하는 게 아니라, 제일 중요한 거가 나오는 거임

### UP Disciplines

- 한 작업 주제에 대한 세부 활동과 관련된 산출물(artifact)의 집합

![]({{ '/series/software-architecture/assets/e34a8616-9dbf-41c2-bcfe-43803c3f3b96.png' | relative_url }})

- 비즈니스 모델링
  - 도메인에 대한 엑터와 거기에 존재하는 엔티티를 포함
    - 액터는 사람, 시스템을 사용하는 다른 시스템 등등 다양하게 될 수 있음
  - 비즈니스 유스케이스
  - 비즈니스 목적
    - 시스템이 어떤 걸 만들어야 하는지 결정됨
  - 비즈니스 프로세스
  - 비즈니스 데이터
  - 비즈니스 결과
- Fuctional Requirements
  - 기능 요구사항
  - 개발자가 구현해야 하는 사항
  - 특정 조건에서의 시스템 동작
- NonFunctional Requirements
  - 시스템 품질
  - 효율, 신뢰, 보안, 유지보수, 호환
  - 제약 조건
- Problem Analysis
  - 문제에 대한 투자
  - 문제의 도메인에 대해 이해하고, 사용자 관점에서 이해하는 것
  - 사용자가 필요로 하는 모든 것을 글을 쭉 써. 그리고 객체를 뽑아(컨셉 추출)
- Solution Design
  - 개발자 관점
  - 컨셉을 바탕으로 내부를 설계하는 단계

<h3>
Designing Software Architecture 
</h3>

![]({{ '/series/software-architecture/assets/6e048005-997a-4bef-9904-b311cec32e01.png' | relative_url }})

## UP 특징

- 유스케이스 드리븐은 나중에 설명

![]({{ '/series/software-architecture/assets/a219b286-68ea-4b72-b85f-501e38ac21d2.png' | relative_url }})

- 인셉션 이후에 elaboration 도 마치면 베이스라인 아키텍쳐가 나옴
- 이때부터 베이스라인 아키텍쳐는 그대로 가고, 세세한 디테일이 변하는 거임

### 아키텍쳐 베이스라인

- 최송 시스템의 ealry version
- skeleton system 이라고도 함
- 최종 시스템이 가져야하는 모든 모델이 잇어야 함
- stable architecture baseline 은 거의 변화되지 않고 계속 감.
  - 약간 변경될 수는 잇음. 구조나 동작 같은 거에서
  - 여기에는 최종 코드의 5\~15 퍼 정도가 구현돼잇음

---

## Inception

### Inception Phase

- 일반적인 비전과 기본 범위 설정하기 위한 초기 단계
  - 모든 요구사항을 정의하거나 프로젝트 계획을 세우는 것이 목적이 아니다
- 프로젝트의 기본 범위와 요구사항의 10% 정도를 이해하는 것에 초점
  - 상세한 분석과 설계는 다음 단계에서 수행
- 일반적인 비전
  - 프로젝트의 비전은 무엇이고 비즈니스 케이스는 무엇인가?<br />• 실행 가능한 프로젝트인가?<br />• 구매해야 하는가 개발해야 하는가?<br />• 비용의 대략적인 범위. 만 달러인가? 십만 달러인가? 백만 달러인가?<br />• 계속 진행해야 하는가 멈춰야 하는가?

![]({{ '/series/software-architecture/assets/1303e9bc-7fb2-455d-be56-03db366ab046.png' | relative_url }})

## Business Object Model

- 비즈니스에 대한 개념을 이해하기 위한 모델

![]({{ '/series/software-architecture/assets/0da4f9ee-5ecc-426c-a7bd-bac935304e95.png' | relative_url }})

- 내부 설계를 제외하고 주변에 대한 설계를 한 것.
- 외부 환경과 어떻게 상호작용을 하는지 정의한 것(소프트웨어 설계가 아님)
  - 시스템 주변에 어떤 게 있는지 파악하고 관계를 표현

## Requirements Capture

- 비즈니스 비전이나 시스템 비전을 위한 요구사항을 추출해서 식별
- 내부 동작
  - 기능 요구사항(Functional Requirements) 추출
    
     A211: Use-Case Model<br /> A212: Actor Description<br /> A213: Use-Case Brief Description<br /> A214: Use-Case Definition
    
  - 품질 요구사항 추출
    
     A221: Quality Requirements Description

## Functional Requirements

### UseCase Diagram

![]({{ '/series/software-architecture/assets/9f87fdf4-78d3-45cd-8aa5-925b540a9a05.png' | relative_url }})

- 열기/닫기 처럼 반대로 동작하는 건 하나만 설계하고 반대로 하면 돼
- 중요한 거 뽑아서 discription 자세히 쓰면 됨

## Quality Requirement

- 요구사항을 작성해야함

## Requirement Capture

- 비즈니스 비전이나 시스템 비전을 위한 요구사항을 추출해서 식별

![]({{ '/series/software-architecture/assets/ee42f45a-8afa-46cb-bbea-2731f175d07d.png' | relative_url }})

## Elaboration

- Inception Phase 에서 정한 요구사항 만족을 위해 시스템을 분석하고 설계하는 단계
- 베이스라인 아키텍처(Baseline Architecture)을 작성하는 단계
- <b>Analysis</b>와 <b>Design </b>과정에 초점
- 여러 Iteration 으로 수행

### <b>Analysis 의 핵심은 Conceptual Model 을 만드는 것!</b>

### Conceptual Model

- 도메인 안에 존재하는 개념(개념적 클래스)나 실제 상황의 객체를 시각적으로 표현한 것
  - 소프트웨어 객체가 아니다!
- concept : 하나의 생각, 사물, 객체
- 만드는 법
  1. 개념적 클래스 찾기
     1. 명사구 식별
     2. 컨셉 카테고리를 만들고 명사구를 식별해도 좋음
  2. 개념적 클래스를 UML 클래스 다이어그램으로 그리기
     1. 뽑아진 부분을 클래스로 만들어
  3. 연관관계와 속성 추가하기
     1. 개념 클래스들 사이의 의미가 잇고 기역할 필요가 잇는 연결 관계를 표현
     2. 서로 연관되어 있는지 기억할 필요가 있는가? 를 기준으로 판단
     3. 속성 : 개념클래스들의 본질적 속성

     ![]({{ '/series/software-architecture/assets/2d0206b1-4461-4a36-935c-4a8cf78891dc.png' | relative_url }})

     ![]({{ '/series/software-architecture/assets/06f56d8f-6799-43a9-a3c1-034a105326e0.png' | relative_url }})

![]({{ '/series/software-architecture/assets/45741845-a92c-4889-b812-fb4cd9a14aed.png' | relative_url }})

- 클래스 다이어그램이 아님!
- <b>얘는 컨셉을 뽑고 그냥 컨셉 간의 관계를 연결한 것뿐임!</b>
- 클래스 다이어그램이되려면 클래스에 대한 정리를 추가하면 됨 X!!

![]({{ '/series/software-architecture/assets/491d512e-0018-4d44-bf2f-15e6e0f6f027.png' | relative_url }})

![]({{ '/series/software-architecture/assets/c201a1ad-3d53-477c-81a6-006f240d63a7.png' | relative_url }})

---

![]({{ '/series/software-architecture/assets/c7f2a2e4-6dc5-491d-968a-6c9b1cddeadc.png' | relative_url }})

## SOLID

- 이해가능하고, 유연하고, 재사용 가능성이 있게 디자인하는 5개 원칙
- 전부 인터페이스랑 관련이 잇음

### Single Responsibility Principle (SRP)

- 메서드가 변경되는 이유는 딱 하나여야함
- <b>메서드 또는 클래스는 하나의 책임만 있어야 함</b>
  - 하나의 일만 해야함

### Open Closed Principle (OCP)

변하는 것과 변하지 말아야 하는 것에 대한 구분의 원칙이다.<br />• 새로운 것을 추가할 때 기존 코드를 수정하지 않고도 기능을 추가할 수 있어야 함<br /> 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다.

- OPEN<br />• 클래스 수직관계(Is-a)에서는 열려 있어야 한다.<br />• 기반 클래스에서 파생 클래스로의 확장하는 경우에 해당한다.<br />• CLOSE<br />• 클래스 수평관계(has-a)에서는 독립적인 관계가 형성되어야 한다.<br />• 즉, 서비스 제공자의 수정은 서비스 사용자에게 영향을 미치지 않아야 한다.
- 추상클래스나 인터페이스를 활용해 달성 가능

### Liskov Substitution Principle (LSP)

- 상속받은 클래스는 부모 클래스의 계약(규칙) 을 지켜야 한다.
  - 부모 클래스(혹은 인터페이스) 를 사용하는 코드가 자식 클래스(구현체)로 교체되어도 프로그램 동작이 깨지면 안된다.

### Interface Segregation Principle (ISP)

- 사용하지 않는 기능이 있는 인터페이스를 상속받는다면, 인터페이스를 분리해라

### Dependency Inversion Principle (DIP\~ IoC(Inversion of Control)일종)

- 의존성 역전 원칙
- 상위 모듈이 하위 모듈에 의존하면 안됨. 둘 다 추상화에 의존해야함

![]({{ '/series/software-architecture/assets/c61b0088-5654-4c08-a2b0-bc1e63b63c71.png' | relative_url }})

- 인스턴스(혹은 메서드) 호출을 하려면 메서드의 주소를 알아야 함.
- 그래서 인스턴스 내부에서 인스턴스를 생성해야 햇음(주소를 알아야 호출을 하니까.)
  - 즉 컴파일 타임에 객체가 결정되어야 하는 불편함이 잇엇음
- 그런데 스프링은 DI 컨테이너가 인스턴스를 생성하고 주소를 알고 있음
- 스프링에서는 인스턴스에서 다른 인스턴스를 호출할 때 DI 컨테이너를 거쳐서 주소를 확인하고 호출함
  - 그래서 런타임에 동적으로 객체를 호출할 수 있게 됨

## GRASP (실습에 한국어로 돼잇음)

- 소프트웨어 디자인 패턴
- 다지인에 클래스가 뭐가 있어야 하는지, 클래스에 메서드를 어떻게 할당해야하는지

### Low Coupling

- 다른 클래스랑 얼마나 긴밀하게 연결되었는지 (정보를 가지고 있거나, 레퍼런스를 가지고 있거나, 다른 클래스를 이용하거나)
- 결합도가 높아지면 유지보수가 힘들고 변경이 힘듦
- 그래서 인터페이스를 사용해서 구현

### High Cohesion

- 클래스 안에 존재하는 메서드가 얼마나 집중되어 있는가?
  - 책임이 얼마나 집중되어 있는가?
- 여러 일을 하지 않고 하나의 일만 하면 응집력이 높다고 함
- 이것저것 들어가면 응집력이 낮아져서 이해도 힘들고 변형도 어려워짐

![]({{ '/series/software-architecture/assets/b47c7d08-b7a2-413b-8c6a-59595ecf6268.png' | relative_url }})

### Information Expert

- 정보를 가지고 있는 객체에 정보를 활용한 메서드를 만들어라

### Creator

- B 에게 인스턴스 A 를 할당받게 하려면 이런 조건이 충족되어야 함
  - B aggregates A objects<br />• B contains A objects<br />• B records instances of A objects<br />• B closely uses A objects<br />• B has the initializing data that will be passed to A when it is created(thus<br />B is an Expert with respect to creating A)
- • B는 A개의 객체를 집계합니다 • B에는 A개의 객체가 포함되어 있습니다 • B는 A 객체의 인스턴스를 기록합니다 • B는 A 객체를 밀접하게 사용합니다 • B는 생성될 때 A에게 전달될 초기화 데이터를 가지고 있습니다 (따라서) B는 A를 만드는 데 있어 전문가입니다)

### Controller

- 외부 이벤트 소스와 내부 이벤트 핸들러의 결합을 낮추기 위해 두는 걸 컨트롤러라고 함
- 이벤트 수신을 담당하는 객체로, 여기서 적절한 내부 이벤트 처리 객체로 전달함

## 설계 순서

1. 인터렉션 다이어그램 그리기(인터페이스)
   ![]({{ '/series/software-architecture/assets/0cf4d348-0c94-4997-b6bf-764fb6e7b681.png' | relative_url }})
2. Conceptual Model 그리기(내부 설계 시작)
   1. Contract 그리기

   ![]({{ '/series/software-architecture/assets/5b43a02c-5d4d-4c8d-8d1b-a0254ef160a1.png' | relative_url }})

   - 책임이 뭔지, 타입이 뭔지, 레퍼런스는 어떻게 연결되어있는지 정의
   - 컨셉을 보고 어떤 객체가 뭘 처리해야하는지 만들어

---

![]({{ '/series/software-architecture/assets/f988c293-52ad-4339-bece-549736057f2b.png' | relative_url }})

![]({{ '/series/software-architecture/assets/63b4cb78-f64b-4024-8cbb-d4928b95eea6.png' | relative_url }})

![]({{ '/series/software-architecture/assets/5752cca7-4e0f-4087-8d5c-3e64b12b4c02.png' | relative_url }})

![]({{ '/series/software-architecture/assets/d51d6bfc-3148-4d6f-baea-85e2cf05c398.png' | relative_url }})

![]({{ '/series/software-architecture/assets/3d3dbb1c-e54a-4c36-a372-486bbdf13e59.png' | relative_url }})

```kotlin
좋아 👍 지금 올려준 다섯 장의 슬라이드는 객체지향 분석 단계에서 “시스템 동작(behavior)”을 정의하는 전체 흐름을 정리한 자료야.
이걸 순서대로 보면 “Use Case → System Interaction Diagram → System Operation Contracts → Object Interaction Diagram” 으로 이어지는 논리적 분석 절차를 보여줘.

아래는 각 슬라이드 내용을 단계별로 정리한 요약이야 👇

🧩 1️⃣ System Interaction Diagram (SID)
📘 개념

시스템 인터랙션 다이어그램은
👉 “외부 행위자(Actor)가 시스템에 보낸 이벤트(Event)”를 보여주는 다이어그램이야.

즉, Actor → System 방향의 이벤트 흐름을 시각화한 것.

시스템이 무엇을 해야 하는지(What) 를 외부 시각에서 기술함.

📍 특징

특정 Use Case 시나리오에 대해

외부 행위자가 발생시키는 이벤트

그 이벤트의 순서(order)

시스템 간(inter-system) 이벤트
를 보여줌.

시스템의 Behavior(행동) 를 “무엇을 하는가” 수준에서 표현, “어떻게 하는가”는 포함하지 않음.

🧠 2️⃣ (System Operation) Contracts
📘 개념

Contract = “시스템 오퍼레이션이 수행되었을 때 시스템이 어떤 상태로 변해야 하는가”를 서술한 문서.

즉, 시스템 오퍼레이션이 실행된 후 시스템의 상태 변화(what happens) 를 정의함.

📍 핵심 포인트

Declarative Style — “무엇이 일어날지(what)”를 기술하고,
“어떻게(how)” 수행되는지는 다루지 않음.

System Operation Contract는
시스템 오퍼레이션이 호출될 때 전체 시스템 상태 변화를 기술.

예: enterItem(upc, quantity) → “새로운 SaleLineItem이 생성되고, Sale에 추가된다.”

🧱 3️⃣ How to Make a Contract (작성 절차)
🧭 작성 단계 요약

Identify system operations
→ System Interaction Diagram(SID)에서 시스템 오퍼레이션들을 식별한다.

Construct a contract for each operation
→ 각 오퍼레이션마다 계약(Contract)을 작성한다.

Write Responsibilities
→ 해당 오퍼레이션이 수행해야 할 목적(책임)을 간단히 서술.

Write Postconditions
→ 오퍼레이션이 끝난 뒤 시스템 내 객체의 상태 변화를 기술한다.

Postcondition 작성 시 세 가지 형태로 구분

Instance creation/deletion (객체 생성·삭제)

Attribute modification (속성 값 변경)

Association formed/broken (연관관계 생성·해제)

🧭 4️⃣ 관계: Use Case → SID → System Operation → Contracts
📘 흐름 설명

Use Case
→ 사용자 관점에서 “시스템이 수행해야 할 시나리오(행동)”를 정의.

System Interaction Diagram (SID)
→ 각 Use Case에서 Actor → System으로 전달되는 이벤트(시스템 오퍼레이션)를 식별.

System Operations
→ SID에서 식별된 이벤트를 시스템 오퍼레이션(메서드) 로 정의.

Contracts
→ 각 시스템 오퍼레이션이 실행되었을 때,
시스템 상태에 어떤 변화가 생기는지를 “Postcondition”으로 명세.

📊 그림 설명
Use Case  →  System Sequence Diagram  →  System Operations  →  Contracts
(사용자 시나리오)    (Actor → System 이벤트)       (식별된 연산)          (상태 변화 정의)

예시:

Use Case: “구매하기”

SID: Cashier → System : enterItem(upc, quantity)

Contract: “새로운 SaleLineItem이 생성되고 현재 Sale에 추가된다.”

⚙️ 5️⃣ (Object) Interaction Diagram
📘 개념

Contract는 “무엇이 바뀌어야 하는가(What)”를 말하지만,
어떻게 구현할지(How) 는 말하지 않음.

그래서 그 Postcondition을 실제로 만족시키기 위해
내부 객체들이 어떻게 협력하는지를 보여주는 것이 Object Interaction Diagram이야.

📍 특징

UML에서의 Interaction Diagram 종류:

Sequence Diagram: 메시지의 순서 중심

Communication Diagram (또는 Collaboration Diagram): 객체 간 연결 중심

표현 내용:

객체/클래스들이 어떤 메시지를 주고받는가

순서, 호출 관계, 책임 분배

📚 전체 구조 요약
단계	다루는 관점	초점	산출물	키워드
Use Case	사용자 관점	시스템이 해야 할 일	시나리오	기능 요구사항
System Interaction Diagram (SID)	외부 이벤트 관점	Actor → System 이벤트 흐름	System Sequence Diagram	What (Event)
System Operation Contract	시스템 상태 관점	오퍼레이션 수행 후의 결과	Contract 문서	What (State Change)
Object Interaction Diagram	내부 객체 관점	시스템 내부의 협력 방식	Sequence/Communication Diagram	How (Implementation)
🧭 전체 흐름 도식
Use Case
   ↓
System Interaction Diagram (SID)
   ↓
System Operations
   ↓
Contracts (what changes)
   ↓
Object Interaction Diagram (how it changes)

📌 요약 문장으로 정리하면:

“Use Case로부터 시스템이 외부와 어떻게 상호작용하는지를 System Interaction Diagram으로 정의하고,
그 과정에서 식별된 각 시스템 오퍼레이션이 수행해야 할 결과(what)를 Contract로 명세한다.
이후, 그 Contract의 Postcondition을 만족시키기 위해 내부 객체들이 어떻게 협력하는지(how)를 Object Interaction Diagram으로 설계한다.”
```
