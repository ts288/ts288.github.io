---
layout: series-article
title: "소프트웨어 아키텍처 11주차"
permalink: /series/software-architecture/week-11/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 10
---

원문: [Notion에서 보기](https://app.notion.com/p/2a83abb1a94d80d1aa6efa56ba27e732?pvs=204)

# 소프트웨어 아키텍처 11주차

## 프레임워크와 툴킷

- 인프라, 플랫폼, 프레임워크
- 플랫폼은 OS
- 프레임워크
  - 일종의 규격, 구현된 시스템이라고 생각하면 된다.
  - 스프링에서 톰캣 넣어두고, 싴리티 설정을 두고 하는 거랑 같음
- 툴킷은 라이브러리
- 스켈레톤 시스템(골격 시스템), 아키텍처 베이스라인
  - 플랫폼 + 프레임워크 + 툴킷 + 꼭 필요하거나 제일 중요한 기능까지 구현

## ADD 3.0 Process

![]({{ '/series/software-architecture/assets/ada5cc9e-22ba-4d2f-917d-fd0757338e86.png' | relative_url }})

1. 기능 품질 등등을 보고
2. 그 중에서 가장 중요한 드라이버를 뽑고
3. 이전에는 하나의 엘리먼트를 디컴포지션햇는데, 지금은 여러 개를 한 번에 분해해서 합치기

5. 아키텍처 엘리멘트를 실체화함. 인터페이스 만들거나 책임을 위임
6. 다이어그램을 그리고, 실제로 표현하지 못하는 알고리즘 같은 걸 적어두기
7. 목적에 맞는지, 요구사항을 만족하는지 현재까지 한 결과물을 분석한다.

이후 더 나눌게 잇으면 다시 올라가서 해결안된 input 을 확인해서 반복하고, 더 없으면 종료한다.

## Architecture Analysis

- A311: Subsystem의 High-Level 구조 (Initial Skeleton Architecture) 정의
  - 품질을 분석
- A312: Initial Analysis Class 확인 (Conceptual Class Model)
  - 기능을 분석

## A32: Use Case Analysis

- 기능적인 부분이 잘 동작하는지 분석(품질 x)
  -  A321: Supplement the Use-Case Description
  -  A322: 각 유스케이스에서 Analysis Class 발견
  -  A323: Use-Case Realization
  -  A324: Refined Analysis Class 정의 (OO Functional Class Model)
  -  A325: Analysis Mechanism 확인

![]({{ '/series/software-architecture/assets/4bef67a2-8444-4a73-b9b6-638323ea02fd.png' | relative_url }})

- 단순히 이름만 보고 중요한 기능을 뽑는 게 아님
- 목적이 다른지, 하는 역할이 다른지, 변경 가능성이 있는지 등등을 다 분석해서 Primary Function 을 뽑아야함

## 품질 요구사항이 아키텍처에 이미 반영되어야 함

![]({{ '/series/software-architecture/assets/822fdde6-b86c-4ac7-a0cf-fd3b4f2b2373.png' | relative_url }})

### QR1. 0.1초 내에 멈춰야 하는 시스템을 어떻게 만들어야 하나?

- cpu 스케줄링을 해야함
- Q. 이걸 어떻게 소프트웨어로 구현하나?
  - 큐를 사용해서 태스크를 처리해야함

### QR2. 어떻게 유연한 구조로 드나?

- Plug And Play 를 구현해야함
- 하드웨어 abstraction layer : 모든 드라이버 위에다 인터페이스를 두고, 이 규격에 맞춰서 구현
- 핵심은 디자인할 때 인터페이스를 두게 하는 것

### QR3. 통신 프로토콜과 관계없이 어떻게 동작하게 하지?

- 표준화
- IOT 에서도 이와 동일한 문제가 발생함
  - 여러 기긱에서 사용하는 통신 규격과 관련 없이 하나의 시스템에서 관리할 수 있어야함
- 게이트웨이도 비슷함
  - 통과 후에는 규격을 따르게 함

## 성능 개선하는 방법

![]({{ '/series/software-architecture/assets/96765ba1-7305-4a4e-a204-f2710a18cd07.png' | relative_url }})

### Resource Demand

1. 리소스를 줄이는 것
   1. 버블 소트보다 퀵소트를 사용하는 것
2. 오버헤드를 줄이는 것
3. 이벤트 발생 비율과 샘플링 주기를 조절하는 것
4. 실행 시간을 제안하는 것 (cpu 타임)
5. 큐 사이즈를 조절하는 것

### Resource Management

- 프로세스, 멀티 스레드 등등 사용하는 것
- 멀티 카티, 멀티 데이터를 사용하는 것
  - 같은 데이터를 10 개 복제할지, 샤딩으로 할지
  - 문제는 동기화를 어떻게 할 것이낙?
- 사용가능한 리소스를 증가시키기

### Resource Arbitration

- 스케줄링 전략 세우기

## Quality Attributes

• 변경가능성(Modifiability): QR2 (Device), QR3 (Protocol)

• 사용성(Usability)

• 성능(Performance): QR1 (Realtime)

• 가용성(Availability)

• 보안 달성 방안(Security)

• 시험 가능성 달성 방안(Testability)

## 유연한 구조를 만드는 방법

![]({{ '/series/software-architecture/assets/8570364c-e9db-402e-b2cc-4a8e1f71d04e.png' | relative_url }})

1. Localize Modifiablility
   1. 응집도를 높이고 결합 낮춰서, 변경 예겨나고 일반화해라
   2. E.G. MVC : 모델, 뷰, 컨트롤러로 구분
   3. 방법
      1. 모듈 일반화
      2. 옵션줄이기
2. Prevent Ripple Effects
   1. 정보 숨기고
   2. 인터페이스 유지하고
   3. 커뮤니케이션 경로를 제한하고
      1. e.g. 검문소
   4. intermediary 주기
3. Differ Binding Time
   1. 다이나믹 클래스 로딩을 통해 동적으로 수행하게
   2. Configuration 사용하기
   3. Polymorphism
   4. 컴퐅넌트 교체
      1. 핫리로딩
   5. 정의된 프로토콜에 Adherece

## 아키텍처 스타일 결정

![]({{ '/series/software-architecture/assets/7629f8cc-5882-43bc-8614-7f74753ac5cc.png' | relative_url }})

- 이런 건 IOT 에서 사용하면 좋은 스타일

## 품질 속성의 실체화

![]({{ '/series/software-architecture/assets/0d2781b5-299a-4dc3-afe2-646a39945b8b.png' | relative_url }})

- QR1. 스케줄링이 들어가야 하는 부분과 들어가지 않아야 하는 부분으로 구분
- 0.1 초 제약이 걸려잇는 모듈이 아니면 특별히 시간적 제약을 고려하지 않아도 됨

![]({{ '/series/software-architecture/assets/69e6acc4-4523-4de5-a346-f1744c6efdfc.png' | relative_url }})

- QR2. 변형 가능한 부분을 분리
  - 맨 위에 모듈을 분리해 프로토콜의 변형 가능성을 고려
  - 맨 아래 모듈을 부닐해 디바이스 변형 가능성을 고려

## Initial Analysis Class 확인

![]({{ '/series/software-architecture/assets/01e90484-64f3-4e73-b3c1-1181d3ea7059.png' | relative_url }})

- 외부 인풋과 목적지가 있을 때에는 GRASP 패턴 중에서 컨트롤러 패턴 사용하면 됨
- 이후 Relization 하면 Method 가 생성됨 (스켈레톤)

![]({{ '/series/software-architecture/assets/c2fabe5e-6e42-4855-9ebb-413ac9bc3676.png' | relative_url }})

- 지금까지 이거 한 거임

![]({{ '/series/software-architecture/assets/02241093-19be-4aba-8d05-0030f9eb294b.png' | relative_url }})
