---
layout: series-article
title: "소프트웨어 아키텍처 7주차"
permalink: /series/software-architecture/week-07/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 7
---

원문: [Notion에서 보기](https://app.notion.com/p/28c3abb1a94d8082b7f3e398e1860ca9?pvs=204)

# 소프트웨어 아키텍처 7주차

- Contracts
  - 시스템 오퍼레이션마다 만듦
  - how 가 아니라 what
    - what : 시스템 외부에서 봣을 때 시스템이 나한테 뭘 해주는지
    - how: what 을 내부적으로 어떻게 처리햇는가?
  - 작성은 오퍼레이션마다 함

  ![]({{ '/series/software-architecture/assets/b369e49b-9c9c-4628-9ef1-06eb1a6b1cc0.png' | relative_url }})

### 시험

- 10문제 : 이론 6 \~ 7, 나머지는 실습
- 한 주에 한 문제
  - 1주차에는 없음
  - 2주차에는 아키텍쳐 개념 모델, 4 + 1 view, 아키텍처 중요성, UML 디테일한 부분은 안나옴
  - 3주차에는 uified process, up phases 4개 - 중요 , up discipline - 중요, up 4가지 특징, 아키텍처 샘필, up phase의 마일스톤 (네모 선 페이지 기록)

    ![]({{ '/series/software-architecture/assets/7a29cbc3-0a77-4a48-961d-11cc06d1966c.png' | relative_url }})
  - 4주차에는 비즈니스 모델과 conceptual 모델, class diagram 세 가지의 차이점,
  - 5주차에는 분석이 뭔지 디자인이 뭔지 구체적으로 what 이 뭐고 how 가 뭐고(오늘 배운 거) 제목 전 후 슬라이드, 

    ![]({{ '/series/software-architecture/assets/491d512e-0018-4d44-bf2f-15e6e0f6f027.png' | relative_url }})

    ![]({{ '/series/software-architecture/assets/c201a1ad-3d53-477c-81a6-006f240d63a7.png' | relative_url }})
  - 6주차 solid, grasp
  - 7주차에는 설계 어떻게 하는지에 대해(프로세스 정확히 이해하는지, contract 가 뭔지, 콜라보레이션과 인터렉트 다이어그램 설계하는 원리에 대해 설명)

- 실습에서는 코드 주고 설계 바꿔봐라
