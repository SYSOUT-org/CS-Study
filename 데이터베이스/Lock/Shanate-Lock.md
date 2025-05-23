# Lock을 사용하는 이유

- 여러 사용자가 동시에 같은 데이터에 접근했을 때 문제가 생길 수 있는 상황을 예방하기 위함.
- 트랜잭션 내부에서의 순차성을 보장하도록 하여, 원하는 결과가 나올 수 있게 만드는 것이 Lock.
- 즉, 동시성을 제어하기 위해 사용하는 기법

## 어떨 때 문제가 생겼다고 할 수 있는가.

![image.png](attachment:fff10a65-d442-4bcd-9d08-2b3909a51fa7:image.png)

- 수강 신청 가능 인원이 1명이 남았다고 가정한다. 이 때, A와 B라는 학생이 동시에 수강 신청을 할 경우 단 한 명이 해당 과목을 수강할 수 있어야 한다. 하지만, 동시성 문제를 고려하지 않았을 경우, 두 사람 모두 해당 과목을 수강할 수 있을 가능성이 생긴다.

# Lock의 종류

## Optimistic Lock(낙관적 락)

- 실질적으로 DB에 락을 걸지 않는다. 커밋 시점에 데이터가 다른 트랜잭션에 의해 수정되었는지 버전을 비교해 충돌 여부를 판단한다.
- Version을 읽고, 한 트랜잭션의 과정을 진행한다. 데이터를 업데이트하기 전에 다시 Version을 읽어온다. 이 때 이전 Version과 지금 받은 Version이 같다면 업데이트가 되나, Version이 같지 않다면, 충돌이 발생했음을 파악한다.
- 충돌이 발생했을 때는, 이전 시점으로 롤백합니다.
- 레이스 컨디션이 많이 발생하지 않을 상황을 예상해 낙관적 락을 사용합니다. 일반적으로, 데이터의 업데이트보다는 단순한 조회 접근이 많을 때 사용합니다.

## Pessimistic Lock(비관적 락)

- 데이터 충돌이 발생할 가능성이 높다고 우선 가정한다. 그렇기 때문에 읽을 시점에 Lock을 미리 걸어 충돌을 방지한다.
- 읽을 시점에 Lock이 걸리기 때문에, 다른 트랜잭션은 전부 대기 상태가 된다. 따라서 성능 저하가 생길 수 있다.

# Pessimistic Lock 연산의 종류

## Shared Lock(공유 잠금)

- 데이터를 변경하지 않는 읽기 작업을 할 때 잠그는 것으로 읽기 잠금이라고 한다.
- 여러 트랜잭션이 동시에 자원을 읽는 것을 허용한다.
- **공유 락**이 **걸려있을 때** 다른 트랜잭션이 **해당 자원에 대해 배타 락을 거는 것을 허용하지 않는다.**
- 즉, 읽기는 가능하지만 수정이 불가능한 상태를 유지한다. 그렇기 때문에, 데이터의 일관성을 보장할 수 있다.
- 주로 데이터를 조회(Select)할 경우가 많을 때 사용한다.

## Exclusive Lock(배타 잠금)

- 데이터를 변경하는 작업을 위해 잠그는 것으로, 쓰기 잠금이라고 한다.
- 데이터를 변경하고자 할 때 사용하게 되는데, **다른 세션이 해당 자원에 접근하는 것을 방지**합니다. 락이 해제될 때까지 다른 트랜잭션은 해당 리소스에 접근할 수 없으며, **기존 트랜잭션이 끝났을 경우 비로소, 해당 리소스에 접근할 수 있습니다.**

| 구분 | 공유 잠금 | 배타 잠금 |
| --- | --- | --- |
| 목적 | 데이터 읽기 중 무결성 보장 | 데이터 읽기/쓰기 간의 완전한 독점 보장 |
| 잠금 여부 | 여러 트랜잭션이 동시에 읽기 가능 | 하나의 트랜잭션만이 독점 |
| 성능 | 읽기 성능 좋음 | 쓰기 성능 낮음 |
| 적합한 상황 | 읽기 작업이 많을 경우. 업데이트 발생이 빈번하지 않을 경우. | 데이터 변경이 많고 충돌 가능성이 높을 경우 |

| 구분 | 비관적 락 | 낙관적 락 |
| --- | --- | --- |
| 목적 | 데이터 충돌을 사전에 방지 | 데이터 충돌을 사후에 감지 |
| 잠금 여부 | 데이터 읽을 때 잠금을 걸어 충돌 예방 | 데이터 읽을 때 잠금이 없기 때문에 커밋 시점에서 충돌 여부를 검사 |
| 성능 | 쓰기 성능 낮음 | 읽기 성능 높음 |
| 적합한 상황 | 데이터 변경이 많고 충돌 가능성이 높을 경우 | 읽기 작업이 많으며 쓰기 작업이 적을 경우 |

## 블로킹

- **한 트랜잭션이** 자원을 사용하고 있는 동안에 **다른 트랜잭션이 해당 자원에 접근하려고 할 때 대기**하는 상황을 **블로킹**이라고 한다. 이를 **해결하기 위해선** 결국, 이전 트랜잭션이 **커밋이 되거나 롤백이 되어 완료 상태가 되어야만 한다**. 즉, 블로킹이 발생한 후에 락이 걸린 트랜잭션이 끝나야만 하는데, 이런 현상이 반복되면 성능에 좋지 않은 영향을 미친다.

➡️ **해결 방법**: 

1. 트랜잭션의 단위를 짧게하면 블로킹 시간을 줄일 수 있다.

## DeadLock(교착상태)

- 두 트랜잭션이 있다고 가정하고, 해당 트랜잭션들은 락이 설정되어 있다. 이 때, 서로의 락에 접근하려고 할 때, 모두 락 설정이 되어 있기 때문에 처리가 될 수 없는 상태를 ‘DeadLock’이라고 한다.
- 이 때 서로 락이 해제되기까지를 기다리기 때문에 영원히 락이 풀리지 않게 된다.
- 그렇기 때문에 락을 사용할 때에는 이 데드락이 걸리지 않는지를 확인해야 한다.

**예시**: 두 개의 스레드가 서로 다른 Lock을 잡고 상대방이 가진 Lock을 기다리는 상황

- 트랜잭션 A는 자원 X를 락하고 자원 Y를 기다리고, 트랜잭션 B는 자원 Y를 락하고 자원 X를 기다릴 때 데드락 발생

➡️ **결과**: 교착 상태 발생 

➡️ **해결 방법**:

1. 트랜잭션 A : 자원 X에 락을 건 후, 자원 Y에 락을 요청
2. 트랜잭션 B : 자원 X에 락을 건 후, 자원 Y에 락을 요청

즉, 모든 트랜잭션이 같은 순서로 락을 걸면 데드락이 발생하지 않는다.

---

참고

[https://kimjh0727.tistory.com/entry/MySQL에서의-잠금-메커니즘과-격리-수준-공유-잠금S-LOCK과-배타-잠금EXCLUSIVE-LOCK의-동작-및-문제-발생-시나리오](https://kimjh0727.tistory.com/entry/MySQL%EC%97%90%EC%84%9C%EC%9D%98-%EC%9E%A0%EA%B8%88-%EB%A9%94%EC%BB%A4%EB%8B%88%EC%A6%98%EA%B3%BC-%EA%B2%A9%EB%A6%AC-%EC%88%98%EC%A4%80-%EA%B3%B5%EC%9C%A0-%EC%9E%A0%EA%B8%88S-LOCK%EA%B3%BC-%EB%B0%B0%ED%83%80-%EC%9E%A0%EA%B8%88EXCLUSIVE-LOCK%EC%9D%98-%EB%8F%99%EC%9E%91-%EB%B0%8F-%EB%AC%B8%EC%A0%9C-%EB%B0%9C%EC%83%9D-%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4)

[https://hyunsb.tistory.com/entry/동시성-문제-해결-Lock-Shared-Exclusive-Pessimistic-Optimistic](https://hyunsb.tistory.com/entry/%EB%8F%99%EC%8B%9C%EC%84%B1-%EB%AC%B8%EC%A0%9C-%ED%95%B4%EA%B2%B0-Lock-Shared-Exclusive-Pessimistic-Optimistic)

https://www.youtube.com/watch?v=ZXV6ZqMyJLg
