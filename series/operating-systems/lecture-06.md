---
layout: series-article
title: "운영체제 6강"
permalink: /series/operating-systems/lecture-06/
series_key: operating-systems
series_title: "운영체제"
series_url: /series/operating-systems/
series_order: 5
---

원문: [Notion에서 보기](https://app.notion.com/p/1e53abb1a94d800d9edbf0d6a4163a55?pvs=204)

# 운영체제 6강

## 원소적 (Atomic) 실행

- 공유변수를 통해 상호작용을 하는 프로세스 혹은 쓰레드 간에 문맥교환이 언제 일어나도 간섭 없는 실행이 보장되는 것
- 공유변수를 사용하는 코드 영역에 임계구역을 설정
- 한 쓰레드가 먼저 임계구역 내에 진입하여 실행 중 문맥 교환이 발생하여 상대 쓰레드에 선점되더라도 그 쓰레드가 임계 구역에 진입하는 것을 불허하고 대기하도록 함

## 임계구역 (Critical Section) 설정

- Atomic 실행을 위하여 각 프로세스 혹은 쓰래드가 공유변수, 자료구조, 파일 등을 배타적으로 읽고 쓸 수 있도록 설정한 <b>코드 세그먼트</b>
- mutex
  - <b>mut</b>ual <b>ex</b>clusive
  - 스레드가 실행될 때 mutax 를 lock 시키면 다른 스레드에서 mutax lock 을 할 수 없음 → 대기하게 됨

  ```kotlin
  void producer (void * arg)
  {
  	int i;
  	for (i =0; i < 1000; i++) {
  		pthread_mutex_lock(&mutex); // 이 시점에서 lock 이 걸리면
  		if (count == 100)
  			pthread_cond_wait( // count 가 100 인 경우에는 consume 이 되어야 함 -> 잠깐 lock 을 풀어서 consumer 가 동작할 수 있게 해줌
  				&buffer_has_space,
  				&mutex);
  		
  void consumer (void *arg)
  {
  	int i,data;
  	for (i =0; i < 1000; i++) {
  		pthread_mutex_lock(&mutex); // 여기서 lock 을 걸지 못함 -> unlock 될 때까지 대기해야함
  		if (count == 0) // count 가 0 인 경우에는 produce 가 되어야 함 -> 잠깐 lock 을 풀어서 producer 가 동작할 수 있게 해줌
  			pthread_cond_wait(
  				&buffer_has_data
  				&mutex);
  ```
- 동기화
  - 코드 상의 임계 구역 설정을 통해 진입/진출을 순서화(Serialize) 하는 것
  - 운영체제에서 얘기하는 동기화는 “순서화” 임

## 상호작용 프로세스

- 컴퓨터 시스템 자체가 상호 작용하는 여러 프로세스의 집합
  - 한 프로세스 내에서도 여러 스레드가 병행 수행될 수 있음

  ### 주요 이슈

  - <b>결정성 (determinacy) – 관련된 프로세스들이 각각의 실행 속도에 관계없이 항상 주어진 초기값에 대하여 같은 결과를 내는 것</b>
  - 상호배제와 동기화 (mutual exclusion, synchronization) – 어떤 자원에 대하여 한번에 한 프로세스만이 이를 접근해야 한다는 것.
    - 이게 결정성에 가장 많은 영향을 미치게 됨!
  - 교착 상태 (deadlock) – 프로세스들이 서로 상대방이 가지고 내놓지 않는 자원을 요구하여 아무도 진행되지 못하게 되는 상태
  - 기아 (starvation) – 프로세스가 어떤 보장없이 막연히 자원을 기다리는 것

  ### 프로세스 시스템

  - 프로세스 시스템은 두가지 요소 (프로세스 집합, 선행 제약) 로 정의된다.
    - 프로세스 집합 : 프로세스를 모아둔 집합
    - 선행 제약
      - 부분 순서 성질을 갖는 것. 이행성을 가짐
      - 어떤 프로세스 전에 다른 프로세스가 반드시 돌아야 한다는 것
      - 선행 제약 관계가 없는 두 프로세스는 <b>독립적</b>임
      - 프로세스 시스템 내의 프로세스들은 서로 <b>독립적</b>이거나 <b>선행 제약 관계</b>가 있다면 <b>비순환적이어야 함</b>
        - 순환 관계가 있으면 안됨. 왜냐면 누가 선행인지 알 수 없기 때문
          - e.g. a 는 b 의 선행, b 는 c 의 선행, c 는 a 의 선행이라면? → 빙글빙글 노우우

  ### 선행 그래프

  - 프로세스 시스템 내의 프로세스간 선행제약 관계를 Directed 그래프로 표현한 것
  - 정상적인 프로세스 시스템의 선행 그래프는 비순환적
  - 순환 관계를 가진 선행 그래프의 예 : 선행은 순환 관계를 가지면 안된다는 거임!
    ![운영체제 6강 이미지 1]({{ '/series/operating-systems/assets/os-05-001.png' | relative_url }})

### 결정성

- 프로세스 시스템 내의 프로세스들 간의 코드 실행 순열이 매번 다를 수 있지만, 같은 조건과 입력이 주어진다면, <b>항상 같은 결과를 산출</b>해야 한다는 성질
- 이런 식이면 p4 전에 p2 가 선행인지, p3 가 선행인지 달라질 수 있음! 그래서 이러면 안돼
  ![운영체제 6강 이미지 2]({{ '/series/operating-systems/assets/os-05-002.png' | relative_url }})

### Q. 비순환적 선행관계가 있거나 독립적이면 프로세스 시스템의 결정성에 문제가 “전혀” 없는 것인가?

- 독립적이어도 결정성에 문제가 생길 수 있다.
  - e.g. producer, consumer 프로그램
    - 이 둘은 선행관계가 없지만, 논리적인 선행 관계 (producer 가 데이터 넣고 consumer 가 소비 & consumer 가 소비하고 producer 가 데이터 넣기) 가 있기 때문에 결정성에 문제가 생길 수도 있음
- 다른 예시
  <embed />

### 프로세스 비간섭 관계

- 프로세스 시스템에서 두 개의 프로세스가 있을 때, 다음 두 가지 경우에 이들은 비간섭 관계에 있다고 정의한다.
  1. 한 프로세스가 다른 프로세스를 선행하는 경우
  2. 또는 이들이 서로 독립적일 경우 한 프로세스의 출력 장소가 다른 프로세스의 입력 장소나 출력 장소가 아닌 경우
- 비간섭을 구현하지 못한 경우 → 임계 구역을 구현해 순서적으로 구현해 결정성을 챙겨야 한다.

### 비간섭과 결정성

- 한 프로세스 시스템에서, 모든 프로세스 쌍에 대해 비관섭 관계가 성립하면 그 시스템은 비간섭 관계를 만족한다고 한다.
- 시스템이 비간섭이면 시스템이 결정적이다 (필요충분 조건)

### 선행제약, 독립적, 간섭, 결정성 관계 정리

![운영체제 6강 이미지 3]({{ '/series/operating-systems/assets/os-05-003.png' | relative_url }})

### Q. 운영체제 내에서 mutex 는 어떻게 구현하는가?

