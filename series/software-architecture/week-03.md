---
layout: series-article
title: "소프트웨어 아키텍처 3주차"
permalink: /series/software-architecture/week-03/
series_key: software-architecture
series_title: "소프트웨어 아키텍처"
series_url: /series/software-architecture/
series_order: 3
---

원문: [Notion에서 보기](https://app.notion.com/p/2703abb1a94d8012a26deaba86866744?pvs=204)

# 소프트웨어 아키텍처 3주차

## Architecture

- 시스템의 기능과 품질을 모두 고려한 구조 설계도로 컴포넌트와 그들 간의 동적/정적 관계도

## OOA/D

![]({{ '/series/software-architecture/assets/c01f647d-f85a-4446-addd-1e0e188ccfa3.png' | relative_url }})

### 아키텍처적 관점에서 유지보수 및 변화에 유리하게 하기 위해서는 모듈화해야함.

### Agile UP(Unified Process)

- 짧은 고정된 기간 내의 반복적이고 진화적이며 적응력 있는 개발 방식

- 모든 아키텍처를 다 설계할 필요 없음. 입금 출금 과 같은 프로세스가 반대인 건 하나만 설계하고 다른 건 반대로 표시해도 됨
- 그런데, 비즈니스적인 요소를 고려해야함. 예를 들어서 이체와 같은 경우에는 다른 시스템에 접근을 해야하는 것이기 때문에 이런 부부은 고려할 것.
- 기능은 같아도 품질은 다를 수 잇음.
  - e.g. 트랜잭션 수.
- 기능보다 품질이 더 중요할 수 잇음. 만들고자 하는 시스템에서 잘 고민해야함
  - 수강신청 시스템하고 입시지원 시스템
