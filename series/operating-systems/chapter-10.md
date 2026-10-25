---
layout: series-article
title: "운영체제 10장"
permalink: /series/operating-systems/chapter-10/
series_key: operating-systems
series_title: "운영체제"
series_url: /series/operating-systems/
series_order: 11
---

원문: [Notion에서 보기](https://app.notion.com/p/20d3abb1a94d80a697a0fc859dea92d4?pvs=204)

# 운영체제 10장

## 파일 시스템

- page fault 가 발생했을 때 그 페이지가 어디에 저장되었는지는 어떻게 아나?
  - 운영체제가 파일을 관리하면서 필요한 페이지를 제공해줌

### 파일

- 정보의 집합체
  - bit, byte, 행 or 레코드 등
- 주로 보조기억장치에 저장되어있고, 프로그램 파일 아니면 데이터 파일
  - 프로그램 파일 : 원시프로그램(c, c++), 오브젝트(바이너리) 프로그램 등
  - 데이터 파일 : 텍스트 파일처럼 비정형화 되어있을 수도 있고, db 처럼 정형화 되어있을 수도 있고, 동영상, 이미지 등 정해진 포맷을 따를 수도 있음
- 하나의 파일은 디스크 내의 여러 개의 섹터로 구성됨
  - 파일의 하드디스크의 섹터 단위로 저장됨
  - 섹터는 논리적으로는 블럭에 해당됨
- 파일은 섹터 단위로 저장되는 정보의 집합체임
- 파일은 추상화를 통해 용도나 기능이 확장되어 있음
  - e.g. 가상메모리, 장치 파일
- ㅍ

## 디렉토리 시스템

<ul markdown="1">
<li markdown="1">
디렉토리 - 자신에게 속하는 모든 파일과 부 디텍토리의 목록을 가진 자료구조의 파일. 폴더라고도 함
</li>
<li markdown="1">
가장 간단한 구조는 단단계 디렉토리 - 유지와 이해가 쉬움
</li>
<li markdown="1"></li>
</ul>

