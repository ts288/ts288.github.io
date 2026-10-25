---
layout: article
title: "Redis Idempotency + Lock 설계 (Lease, Result)"
permalink: /posts/redis-idempotency-lock/
category: database
list_order: 7
home_section: database
home_section_title: "데이터베이스"
---

원문: [Notion에서 보기](https://app.notion.com/p/3083abb1a94d80d888fccf8efdfc29ef?pvs=204)

# Redis Idempotency + Lock 설계 (Lease, Result)

- 멱등성 키를 도입하면서, 요청 처리 중과 요청 처리 완료 상태를 나눠야 할 필요가 생겼음
- 각각을 Lease, Result 라는 값으로 보여주기로 결정

## Q. 왜 원자적으로 조회해야 하는가?

### If) Result 먼저 조회하는 경우

```javascript
1.	result 조회
2.	lease 조회
3.  lease 획득(setIfAbsent)
4.	트랜잭션 수행
5.	result 저장
6.	lease 해제
```

- 가정 ) 두 개의 중복된 요청 A, B 가 발행되었을 때,
  - A 가 3까지 수행한 시점에 B 가 1 을 수행하고
  - A 가 6을 마친 시점에 B 가 2 를 수행하면
  - B 에서 트랜잭션이 한 번 더 수행될 가능성이 있음

### If) Lease 먼저 조회하는 경우

```javascript
1.	lease 조회
2.	result 조회
3.  lease 획득(setIfAbsent)
4.	트랜잭션 수행
5.	result 저장
6.	lease 해제
```

- 가정 ) 두 개의 중복된 요청 A, B 가 발행되었을 때,
  - A 가 3 까지 수행한 시점에 B 가 2까지 수행하고,
  - A 가 6을 마친 시점에 B 가 3 을 수행하면
  - B 에서 트랜잭션이 한 번 더 수행될 가능성이 있음

### 그래서 lua 를 사용했음

- 애플리케이션 수준에서 방어 로직을 추가하는 것보다, 인프라(Redis + Lua) 레벨에서 상태머신과 원자성을 보장하도록 책임을 위임하는 게 타당하다고 생각했음
- 더불어, 결제 도메인에서는 특히나 이것들이 중요하다고 생각함
  1. 조회 성능 개선
  2. 중간 상태 노출 방지(lease / result / 둘 다 없음) 하기 위해 사용함
  3. 프레임워크 확장성(언어 제약 x)

```javascript
1. lease, result 를 한 번에 원자적으로 조회
	- 여기서 가능한 상태는 둘 다 없음, lease 만 있음, result 만 있음 임
	- 각 케이스에 대해 로직을 수행
		- 둘 다 없을 경우에는 트랜잭션 수행
		- lease 만 있으면 throw 후 재큐잉
		- result 가 있으면 값 내리기
2. lease 획득 (setIfAbsent)
	- 두 요청이 동시에 1 을 수행할 수 있기 때문
3. 트랜잭션 수행
4. lease 해제 및 result 저장을 원자적으로 수행
```

- 문제는 이 방식에서도 방어 안되는 예외 케이스가 있음
- 가정 ) 두 개의 중복된 요청 A, B 가 발행되었을 때,
  - A 와 B 모두 1 까지 수행한 상태에서,
  - A 가 4 까지 마치고, B 가 2 를 수행하면
  - B 에서 트랜잭션이 한 번 더 수행될 가능성이 있음

### <span color="red">결국 조회와 lock 획득이 분리되면 방어가 불가능함</span>

- 그래서 lua 스크립트로, 조회, lock 획득을 한 번에 수행
  - lease, result 를 조회한 결과를 내리면서, 둘 다 없는 경우에는 락을 획득함

```javascript
### 변경된 로직

1. **lease, result 조회 및 lock 획득을 원자적으로 수행**
   - 가능한 상태:
     - 둘 다 없음
     - lease만 있음
     - result만 있음
   - 각 상태별 반환 결과:
     - **둘 다 없음** → 락 획득 후 `status = ACQUIRED`
     - **lease만 있음** → `status = LOCKED`
     - **result만 있음** → `status = DONE`, `result = <저장된 값>`

2. **트랜잭션 수행**
   - `status = ACQUIRED`인 경우에만 비즈니스 트랜잭션 실행

3. **lease 해제 및 result 저장을 원자적으로 수행**
   - 결과 키(result key)가 없을 때만 결과를 저장하고 lease 삭제
   - Redis Lua 스크립트로 단일 원자 연산으로 처리
```

```javascript
"""
-- KEYS[1] = lease key
-- KEYS[2] = result key

local lease  = redis.call('GET', leaseKey)
if lease == false then
    lease = nil
end

local result = redis.call('GET', resultKey)
if result == false then
    result = nil
else
    result = cjson.decode(result)
end

if result ~= nil then
    return cjson.encode({ status = "DONE", result = result })
end

if lease ~= nil then
    return cjson.encode({ status = "LOCKED", result = result })
end

redis.call('SET', leaseKey, 'LOCKED', 'NX', 'PX', 10000)

return cjson.encode({ status = "ACQUIRED", result = result })
"""
```


### 테스트

- 정상적으로 동작하는지 ✅
- 이미 처리된 요청에 대해서 같은 요청을 내리는지 ✅
- 처리중인 로직에 대해서 처리 중임을 내리고, 재큐잉하는지 ✅
  - rabbitmq config 에서 <code>factory.setConcurrentConsumers(5);</code> 설정 후,
  - MockService 에 Thread.sleep 주고 테스트

### 그런데 이 방식에서는 Redisson 을 사용할 수 없음

- Redisson 을 사용해서 lock 을 걸고 heartbeat 를 보내게 하기 위해서는, Redisson 으로 lock 을 걸어야 함
- 그래서 직접 heartBeat 를 보내도록 수정
