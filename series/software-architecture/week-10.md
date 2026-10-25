---
layout: series-article
title: "소프트웨어 아키텍처 10주차 1차시"
permalink: /series/software-architecture/week-10/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 9
---

원문: [Notion에서 보기](https://app.notion.com/p/2a13abb1a94d80b8be26db108b968fcb?pvs=204)

# 소프트웨어 아키텍처 10주차 1차시

## ADD

- 시스템을 설계할 때, 기능 뿐만 아니라, 성능, 보안, 확장성 등의 qualityu attributes 를 만족시키도록 아키텍처를 구조적으로 설계하는 방식

## Architecture Drivers(동인)

- 아키텍처 설계 방향을 결정짓는 요인들
  - <b>Design Purpose</b> : 설계 목적
  - <b>Quality Attributes</b> : 품질 속성
  - <b>Primary Functionality</b> : 주요 기능
  - <b>Architectural Concerns</b> : 아키텍처적 고려사항
  - <b>Constraints</b> : 제약조건
- 이건 stakeholders 가 비즈니스 목표, 미션 목표에 따라 우선순위를 정한 것

## Design Purpose

- 아키텍처 설계의 이유와 목적을 명확히 하는 단계
- 설계 시작 전에 설계 목표와 목적을 정해야함
- 예시
  - 프로토타입 제작을 위한 것인지
  - 개발 중인 시스템의 일부 설계인지
  - mature domain 인지, novel domain 인지
    - 여기서 성숙한 건 기존에 아는 거, 새로운 건 기존에 모르는 거를 개발하는 경우를 말한다.

## Quality Attributes

![]({{ '/series/software-architecture/assets/a0ae6c0e-adab-440f-84cc-c9167403b883.png' | relative_url }})

- 비기능적인 요구사항의 핵심 요소
- 시스템이 얼마나 잘 작동하는가를 나타내는 측정 가능하거나 테스트 가능한 속성
- <b>품질 속성</b>이 가장 중요함
- 설계 전에 품질 속성 목록을 완성해야함. 그리고 추출, 명세, 우선순위 결정, 검증해야함

### Functional Suitability

- Functional Completeness
  - 요구한 기능이 모두 있는가?
- Functional Correctness
  - 기능이 정확하게 동작하는가? (요구사항을 충족하는가?)
- Functional Appropriateness
  - 기능이 사용자의 목적 달성에 도움이 되는가?

### Performance Efficiency

- Time Behaviour
  - 시스템의 시간 성능(응답 시간, 처리 속도 등)
- Resource Utilization
  - 자원 활용도(cpu, 메모리, 네트워크 등)
- Capacity
  - 감당할 수 있는 최대 처리량

### Compatibiity

- Co-existence
  - 다른 소프트웨어와 충돌 없이 동시에 동작할 수 있는가?
    - 가상 환경에서 여러 대의 머신이 동작하는 것
- Interoperability
  - 서로 다른 시스템에서 데이터를 교환하고 사용할 수 있는가?

### Usablity (유저 경험)

- Appropriateness Recogniablity
  - 보기만해도 알 수 잇나?
- Learnalblity
  - 얼마나 빠르게 배울 수 잇나?

### Reliablity

- matuality
  - 이게 제일 중요함
  - 오류 없이 안정적으로 운영될 수 있나?
- Availability
  - 필요한 순간에 사용 가능한가? 중간에 멈추거나 하지는 않는가?
- Fault Tolerance
  - 일부 오류가 시스템 전체로 전파되지 않는가?
- Recoverability
  - 얼마나 빠르게 복구되는가?

## Primary Functionality

- 비즈니스 목표 달성에 중요한 기능
- 기술적으로 난이도가 높거나, 여러 아키텍처 요소와 상호작용이 필요한 기능
- 일반적으로<b> 전체 UseCase 의 10% 정도가 핵심</b> 기능임
- 어떤 기능을 어떤 모듈에 넣는지도 중요함
- 일부 품질 속성은 Primary Functionality 랑 직접 연결돼있음

## Architectural Concerns

### 일반적인 고려

- 전체 시스템 구조 설정
- 기능 배분(모듈에)
- 모듈 배분(팀에)

등등

### 구체적인 고려

- 예외처리, 의존성 관리
- 설정, 로깅
- 인증, 인가
- 캐싱

## Constraint

- 아키텍처 설계 시 통제할 수 없는 결정 사항
  - 아키텍처 설계 시 반드시 따라야 하지만 스스로 바꿀 수 없는 조건
- 반드시 사용해야 하는 기술이 있거나, 준수해야하는 법규나 표준이 있거나, 마감기한이나 호환성 등등

## Architecture Tactics

- 높은 품질 속성을 보장하기 위해 사용하는 설계 기법
- Tactic 은 하나의 품질 속성을 제어하는데 초점을 둠
- Style 은 여러 품질을 균형있게 조율하는데 초점을 둠

![]({{ '/series/software-architecture/assets/ae33e749-8674-4505-a747-7ee823e54133.png' | relative_url }})

- 계층형 아키텍처 스타일
- 자신보다 아래 계층의 서비스만 호출할 수 있음
- 위에서 아래 방향성

![]({{ '/series/software-architecture/assets/46091b22-0373-44af-a355-301ad9d293a1.png' | relative_url }})

1. 모듈 분해
   1. 전체를 적절한 수준의 서브시스템으로 분해하며 ADD 실행
   2. 다음 반복에서는 서브시스템을 다시 또 적절한 수준으로 분해하여 ADD 실행
   3. 이걸 반복
2. 아키텍처 동인 결정
   1. 제일 중요한 요구사항을 선택해 동인으로 선정
3. 아키텍처 스타일 선정
   1. 동인 달성에 가장 적합한 아키텍처 스타일 선택
