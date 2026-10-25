---
layout: series-article
title: "소프트웨어 아키텍처 12주차"
permalink: /series/software-architecture/week-12/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 11
---

원문: [Notion에서 보기](https://app.notion.com/p/2af3abb1a94d804fb4aae268ea3f2ac5?pvs=204)

# 소프트웨어 아키텍처 12주차

![]({{ '/series/software-architecture/assets/7ad702f0-409b-4c30-a5a5-0f67205ce696.png' | relative_url }})

- 서비스를 만들면 누가 사용할까?
- 사용자의 규모는 어떻게 될까?
- 요구사항은 어떻게 될까?
  - 안정성이 중요한가?
    - 쓰다가 앱이 종료되어도 트랜잭션이 처리되나?
  - 속도가 중요한가?

를 고려해야함

- 문제는 대부분의 요구 사항이 피상적이라는 것
- 진짜 서비스를 배포한다고 할 때 어느 것이 중요하게 될 것인지 판단을 잘 해야함
- 텀프로젝트에서도 요구사항이 고려햇다는 게 아키텍처에 반영이 되어야 함
  - 제일 중요한 품질이 뭘까 생각하고 설계를 해야함

![]({{ '/series/software-architecture/assets/895a38a6-6c0c-4753-8c5b-6088545e2e3f.png' | relative_url }})

## ADD 란?

- 어떤 시스템을 잘게 잘라서 반복하며 시스템을 만들어가는 과정
- 제일 중요한 품질을 뽑고
- 스타일을 결정하고
- 모듈을 만들고
  - 여기서 모듈은 크기가 어떠냐에 따라서 시스템일 수도, 컴포넌트일 수도 있음
- 검증하고
- 이 과정을 반복하는 것

![]({{ '/series/software-architecture/assets/2539d848-b1c8-4292-a08e-5fcd5635b5fa.png' | relative_url }})

- 분석 단계에서 initial Skeleton Architecture 를 만든 게 중요함
- 그리고 기능을 구현하는 게 중요함 (OO Functional Class Model)
- 둘 다 포함되지 않지만 고려해야할 사항들을 반영하는 게 중요함
  - e.g. \~\~ 알고리즘을 쓰면 되겟다, \~\~ 패턴을 쓰면 되겟다 등등

![]({{ '/series/software-architecture/assets/651ce42e-7df4-4ab9-8e25-44550e40f92e.png' | relative_url }})

## ADD vs UP

- 둘 다 아키텍처 중심
- Define a Candidate archtecture 가 architecture analysis 랑 연관
- use-case alaysis 가 behavior 만족하는걸 먼저 만들어두는 거
- architecture 디자인이 refiended architecture
- detalil designgn 이 컴포넌트 디자인, 데베 디자인

![]({{ '/series/software-architecture/assets/0488b3be-a834-4212-8a01-9e9d286e778a.png' | relative_url }})

- Function 을 Allocating Functionality to Initial Skeleton Architecture 로 집어넣어야 함
- 바운더리를 나누고, 어디에 들어가야할지 판단해야함

![]({{ '/series/software-architecture/assets/c3e8cc38-69d1-4342-891c-567bc0e6bbd5.png' | relative_url }})

- 하나의 컨트롤러에서도 여러 함수가 있음
- 근데 역할이 비슷한 함수가 있고 다른 함수가 있음
  - 여기서는 diagonosis the system, detected
- 이걸이제 크리티컬한 거랑, 크리티컬하지 않은 걸 구분해서 나누면 됨

이와 같이 변형 가능성, 성격, 역할 등을 고려해서 다 나누면 됨

그럼 이렇게 됨

![]({{ '/series/software-architecture/assets/081a18cf-d705-41ce-aa47-cfcb44fa1382.png' | relative_url }})

- 퀄리티가 서브 모듈 각각에 할당된 것을 확인할 수 있음

![]({{ '/series/software-architecture/assets/d71f8182-9c0d-410a-b907-6595618fb1c0.png' | relative_url }})

- analysis : persistency
- design : Rdbms 를 써야 한다
- implementation : JDBC 써야 한다

이렇게 단계가 깊어질 수록 구체적으로 작성해야함

![]({{ '/series/software-architecture/assets/18665240-7efe-423a-8a46-7592267ae0da.png' | relative_url }})

- 싱글톤 패턴은 이렇게 표현한다

- 아키텍처 동인을 선택할 때에는
  - 비즈니스적으로 얼마나 중요한가?
  - 기능적으로 얼마나 중요한가?
- 품질을 정할 때에도
  - 변경 가능성이 중요한가?
  - 성능이 중요한가?

를 항상 고려해야함
