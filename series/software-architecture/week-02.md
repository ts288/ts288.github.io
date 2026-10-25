---
layout: series-article
title: "소프트웨어 아키텍처 2주차 1차"
permalink: /series/software-architecture/week-02/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 2
---

원문: [Notion에서 보기](https://app.notion.com/p/2683abb1a94d80138bf5c2c4bff77882?pvs=204)

# 소프트웨어 아키텍처 2주차 1차

## Architecture 란?

### system

- 우리가 사용하고자 하는 서비스

### model

- 아키텍쳐를 가지고 잇는 부분.
- 특정 관점에서의 시스템의 핵심(요약)

### solution

- 모델의 구현

![]({{ '/series/software-architecture/assets/d4b15381-1346-42cf-8ec1-6d419a21fc06.png' | relative_url }})

### 시험 문제로 나옴! e.g. 그려봐라\~ or view 가 뭐냐?

![]({{ '/series/software-architecture/assets/7443cc95-79f8-47d0-aab9-261ad6ccee7f.png' | relative_url }})

### 모델링

- 시스템과 모델 사이에 모델링이라는 과정을 거침
- UML 은 모델을 표현하거나, 스펙을 표기하거나, 구조화, 문서화하기도 함
- 이를 통해 상호작용에 대해 공통적인 이해가 가능하다.
- 또한 품질(ISO 표준, 9가지) 이 좋은 소프트웨어 시스템 개발이 가능하다(기능 & 품질)
- 품질을 위한 설계를 중점으로 배워야 함

![]({{ '/series/software-architecture/assets/597142e3-16fc-4534-949b-3ce6e46e513f.png' | relative_url }})

### Architectural Views

- 4 + 1 view

![]({{ '/series/software-architecture/assets/0734e776-ccb2-4a96-995c-ffb87b6bfa5e.png' | relative_url }})

- 왼쪽은 기능, 오른쪽은 품질
- Logical 은 기능을 어떻게 만들 것이냐
- Process 는 어떻
- Implemetation 은 어떻게 구현할 것이냐
- Deployment 는 어떻게 배포(어디에 탑재)할 것이냐
- 모든 건 UseCase 로부터 시작됨!

- 아키텍쳐는 기능이 복잡해지거나, 높은 제품의 품질을 요구할 수록 중요해짐

### 소프트웨어 시스템의 복잡성

![]({{ '/series/software-architecture/assets/65f7512e-344e-4370-a73a-c9c243b93476.png' | relative_url }})

- x 축은 기능적 복잡성, y 축은 기술적 복잡성
  - e.g. 은행 시스템은 보안, 안정성 부분에서는 복잡함.

## 소프트웨어 모델링의 개발 프로세스(공정)

![]({{ '/series/software-architecture/assets/4816825a-7d35-4bb0-a803-58dbb17b202f.png' | relative_url }})

### 객체지향 분석(OOA)

- sw 시스템의 내부 구성 도메인 개념(객체가 될 가능성) 을 찾고 설명

### 객체지향 설계(OOD)

- 구현을 위해 논리적 설계모델(solution) 을 객체지향 구조로 만드는 단계
- 유스케이스를 수행하기 위해 객체들이 어떻게 협력하는지 정의

### OOAD 설계과정

![]({{ '/series/software-architecture/assets/0b1b3fef-d6b6-4200-889b-aa29322a18a2.png' | relative_url }})

1. UseCase 정의
2. 개념 모델 정의
3. 협력 관계 정의
4. 디자인 클래스 정의

- UseCase 정의에는 프로그래머가 필요없음. 이건 사용자가 서비스를 왜 사용하려 하는지, 어떤 기능이 필요한지 정의하는 부분이기 때문!

![]({{ '/series/software-architecture/assets/ca64461e-266c-4c65-9957-d302964a755a.png' | relative_url }})

- 아키텍쳐를 보기 쉬움

![]({{ '/series/software-architecture/assets/9a21cd78-0411-4387-ba6d-0e334ae70fc9.png' | relative_url }})

- 아키텍쳐를 보기 어려운데, 시간을 체크 가능

![]({{ '/series/software-architecture/assets/49f5de08-632a-4a68-b86e-687021bcd6e7.png' | relative_url }})

### Summary

- Architecture 란?
  - 시스템의 기능과 품질을 모두 고려한 구조 설계도로 컴포넌트와 그들 간의 정적 / 동적 관계도
- Architecture 개발 공정 및 핵심 설계 단계
  - : Problem Analysis and Solution Design
- Architecture Views in UML
  - UML이란?
    - Logical, Process, Implementation, Deployment
  - 4+1 Views
    - 표준 모델링 언어 (Unified Modeling Language)
  - 정적모델 (구조적 다이어그램)
    - Class Diagram, Component Diagram, Package Diagram, Deployment Diagram
  - 동적모델 (행동 다이어그램)
    - Use Case Diagram, Activity Diagram, Sequence Diagram, Communication Diagram
