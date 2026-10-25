---
layout: series-article
title: "소프트웨어 아키텍처 4주차"
permalink: /series/software-architecture/week-04/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 5
---

원문: [Notion에서 보기](https://app.notion.com/p/2773abb1a94d806ebb6bcaa25b9ef621?pvs=204)

# 소프트웨어 아키텍처 4주차

## Inception Phase

- 프로젝트의 일반적인 비전과 기본 범위를 설정하기 위한 초기 단계
  - 모든 요구사항을 정의하거나 프로젝트 계획을 세우는 것이 목적이 아니다!
- 프로젝트의 기본 범위와 요구사항의 10% 정도를 이해하는 것에 초점
  - 상세한 분석과 설계는 다음 단계에서 수행
- 과도한 문서 작업을 피하고, 필요한 정보만을 간단히 문서화
- Inception 단계는 짧게, 보통 한 주 또는 몇 주 동안 진행 (1 Iteration)

## Business Object Model

- 내가 만들고자 하는 시스템의 주변은 어떻게 구성되어있는지 그리는 것

![]({{ '/series/software-architecture/assets/7d6e63d4-a53f-47dc-9d0f-535f4fca8505.png' | relative_url }})

- 여기서 구현하고자 하는 건 Garage Door System 인데, 설계 전에 주변을 먼저 표기하는 것

## UseCase Model

![]({{ '/series/software-architecture/assets/4ff8689d-a8fb-46d1-ade5-f3b08ffbd8fe.png' | relative_url }})

- 관리 시스템이 존재하는 경우, 진단 useCase (Diagnosis the system) 을 고려해야함
- 이 경우에는, 동작은 사용자의 동작과 동일하지만, 고려해야 할 게 많음
- 상세 설명을 적을 때에는 중요한 것만 적으면 됨. 예를 들어서 동작이 겹치거나, 반대되거나 하는 경우에는 하나만 적으면 됨
  - e.g. open 과 close 는 반대이므로 하나만 상세 설명해도 됨

![]({{ '/series/software-architecture/assets/6fd26941-11e6-4ef2-906c-37514305781c.png' | relative_url }})

- 여기서는 제일 중요한 primary function 은 Buy Items!
- 나머지는 일단 OK 로 넘어가고 제일 중요한 걸 자세하게 구현해야함

![]({{ '/series/software-architecture/assets/a2a2f313-e408-48fc-87af-1990a5e12165.png' | relative_url }})

- 명사 보이면 Class 로, 동사 보이면 Method 로 뽑아야함
  - e.g. 

    1 에서 Customer, Cashier 등의 명사는 클래스로 분리

    1 에서 check-out 같은 동사는 메소드로 분리

![]({{ '/series/software-architecture/assets/3b83fbff-5c12-407b-81f3-d1b7336c96d5.png' | relative_url }})

- 오른쪽은 primary function
  - 잘못 뽑앗어도 왜 당시에 선정햇는지랑, 왜 잘못됫는지 설명
- 이번 팀플 목적은 설계 → 구현까지.
- 이번 목적이 방법론 익히고 배우는 것이기 때문에, 핵심만 뽑아서 아키텍처 설계 + 디자인 패턴 적용 + 핵심 부분 설계해서 작동되는 거 보여주기
- 요구사항 분석해서 적되, 어떤 걸 구현까지 할지 몇개 추려도 됨

## OO Functional Analysis

### Functional AD Process & Artifacts

![]({{ '/series/software-architecture/assets/b764809b-a6ba-4cf7-baf5-1ae90749cfff.png' | relative_url }})

- Conceptual Model >> 이거 시험에 나올 수 있음 (이게 뭐냐, 어느 단계에서 나오냐)
  - 도메인 안에 존재하는 개념(개념적 클래스)나 실제 상황의 객체를 시각적으로 표현한 것
    - 소프트웨어 객체가 아니다!
  - UML 표기법을 적용하기 위해, 도메인 모델은 오퍼레이션(메소드)들이 정의 되지 않은 클래스. 다이어그램(class diagram)을 사용하여 표현
  - 만드는 방법
    1. 개념적 클래스 찾기
       1. 명사(구) 식별하기
          1. 앞서 살펴본 Customer, Cashier 등등

       ![]({{ '/series/software-architecture/assets/dca33716-72f3-49a1-ad5d-be5bc7985898.png' | relative_url }})
    2. 개념적 클래스를 UML 클래스 다이어그램으로 그리기

    ![]({{ '/series/software-architecture/assets/ee93aaf7-48f4-4eaa-99a0-02cafde1197c.png' | relative_url }})

    3. 연관관계와 속성을 추가
       1. Association(연관관계)
          1. 개념 클래스(개념, 분석 클래스)들 사이의 의미 있고, 기억할 필요가 있는 연결 관계(relationship)
          2. 서로 연관되어 있는가를 기억할 필요가 있는가? 를 기준으로 판단

          ![]({{ '/series/software-architecture/assets/9f325cd3-f691-46fe-ba05-9d8b79459bc1.png' | relative_url }})

          ![]({{ '/series/software-architecture/assets/6d2ac3a6-c264-425c-a72c-cab04498785b.png' | relative_url }})

          - 필요한 부분만 채우기
          - 이건 디자인임. 아직 설계가 아님
