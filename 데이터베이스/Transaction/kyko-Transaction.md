# 트랜잭션

트랜잭션에 대해 공부하면서 궁금한 흐름으로 정리했습니다.

**트랜잭션(Transaction)** 이란, 데이터베이스와의 논리적 상호작용의 단위를 의미

데이터베이스의 상태를 변화시키기 위해서 수행하는 하나의 논리적인 작업 단위를 뜻함.

**초당 트랜잭션(TPS):** 데이터베이스의 작업 성능

**⚠️주의!!**

트랜잭션이 하나의 쿼리만을 포함하는 것은 아니다!

ex) 고경남이 토스 앱을 사용해 스터디 모임 통장에 30000원을 입금하는 경우

- 고경남의 계좌에서 잔액을 30000원 감소시키기
- 모임 통장 계좌에서 잔액을 30000원 증가시키기

<br>

## 트랜잭션은 왜 ACID라는 특징을 가지게 되었는가?

데이터베이스는 여러 사용자와 여러 프로그램이 사용하는 경우가 대부분이다.
여러 사용자/프로그램이 동시다발적으로 실행될 때 안전한 트랜잭션을 보장하기 위해 다음 ACID라는 성질을 지켜야 한다.
| 특성 | 설명 |
| --- | --- |
| Atomicity(원자성) | 트랜잭션을 구성하는 연산 전체가 모두 정상적으로 성공하거나 모두 실패해야 한다. |
| Consistency(일관성, 정합성) | 트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스 상태로 유지한다. |
| Isolation(고립성, 독립성, 격리성) | 두 개 이상의 트랜잭션이 동시에 발생할 때, 서로의 연산에 영향을 주면 안 된다. |
| Durability(영구성, 지속성) | 커밋된 트랜잭션의 내용은 영구히 반영된다. |

- **원자성 ex)** 송금하는 과정에서 고경남의 계좌에서 30000원이 감소되자마자 DB가 다운되면,  30000원 감소시켰던 작업이 취소되어야 한다.
- **격리성 ex)** 쿠팡에 재고가 1인 책을 두 사람이 동시에 구매하는 경우, 두 트랜잭션이 동시에 재고를 확인하고, 각자 재고가 있다고 판단하여 결제를 진행하면 두 사람 중 한명은 책을 못 받는다.
- **지속성 ex)** 고경남이 모임 통장에 정상적으로 30000원을 입금 완료하면, 시스템 장애가 발생해도 입금 내역은 디스크에 기록되어 사라지지 않음. 다시 30000원 안낸다.

<br>

## 트랜잭션 상태
![image](https://github.com/user-attachments/assets/0635c4a8-f0f0-4fc1-9405-071ba2d7fd59)
- Active: 트랜잭션이 실행 중에 있는 상태, 연산들이 정상적으로 실행 중인 상태
- Partially Committed: 트랜잭션이 마지막 연산까지 실행했지만, Commit 연산이 실행되기 직전의 상태
- Commited: 트랜잭션이 성공적으로 종료되어 Commit 연산을 실행한 후의 상태
- Failed: 트랜잭션이 실행에 오류가 발생해 중단된 상태
- Aborted: 트랜잭션이 비정상적으로 종료되어 Rollback 연산을 수행한 상태
- Commit: 트랜잭션을 성공적으로 수행하여 트랜잭션을 종료하는 것
- Rollback: 이전 트랜잭션을 취소하는 작업

<br>

## 격리성(Isolation) 때문에 두 트랜잭션은 동시에 실행 안되는가?
**동시성 문제(Cuncurrency Problem)** 를 해결하기 위해 데이터베이스는 **격리성(Isolation)** 을 통해 동시성 문제를 감추려고 했다. 한 번에 하나의 트랜잭션만 실행 가능한 **직렬성 격리(Serializable Isolation)** 을 제공함으로써 동시성 문제를 원천 차단했다.

하지만 트랜잭션이 순차적으로 실행되면 안정성을 보장하지만 동시에 **성능 손해를 동반**한다. → Real World에서는 거의 사용되지 않는다.

그렇기에 직렬성 격리보다는 완화된 격리 수준을 사용한다. 물론 이에 따라 버그가 발생할 수 있다.

<br>

## 트랜잭션의 격리 수준(Transaction Isolation Level)
여러 트랜잭션이 동시에 수행될 때, 트랜잭션끼리 서로 얼마나 고립되어 있는지를 나타내는 것을 트랜잭션의 격리 수준이라고 한다. 즉, 특정 트랜잭션이 다른 트랜잭션이 변경한 데이터를 볼 수 있도록 허용 여부를 결정하는 것이다.

격리 수준이 높은 순서대로 **SERIALIZABLE, REPEATABLE READ, READ COMMITTED, READ UNCOMMITED**가 존재한다. 아래 사진처럼 설정할 수 있다.
![image](https://github.com/user-attachments/assets/2b07ae00-53ef-4643-a6f8-376a91faf473)

<details>
<summary><b> Isolation.DEFAULT? </b></summary>
<div markdown="1">
<img src="https://github.com/user-attachments/assets/fed79dfd-c96a-4ab0-bdc5-a2b5f4c77fbb" alt="image" />
DEFAULT 설명에 따르면, 본인이 사용하는 DB의 기본 격리 수준을 따른다.
  <br>
<ul>
    <li>MySQL의 기본 격리 수준 (REPEATABLE_READ)</li>
</ul>
<img src="https://github.com/user-attachments/assets/a33090ea-e153-46a1-93c0-29f63b125e17" alt="image" />
  <br>
<ul>
    <li>PostgreSQL의 기본 격리 수준 (REPEATABLE_READ)</li>
</ul>
<img src="https://github.com/user-attachments/assets/ff4c0e43-b9ed-48c1-a23e-ff1a43c74691" alt="image" />
외에 MongoDB(READ_UNCOMMITED), Oracle(READ_COMMITED)

</div>
</details>

<br>

<ol>
    <li>
        <strong>SERIALIZABLE</strong>
        <ul>
            <li>한 트랜잭션에서 사용하는 데이터는 다른 트랜잭션이 접근 불가능</li>
        </ul>
    </li>
    <li>
        <strong>REPEATABLE READ</strong>
        <ul>
            <li>커밋이 완료된 데이터만 읽을 수 있으며, <strong>트랜잭션 범위 내에서 조회한 내용이 항상 동일함을 보장</strong></li>
            <li>일정 범위의 레코드를 두 번 이상 읽을 때, 첫 번째 쿼리에서 없던 유령 레코드가 나타나는 유령 읽기(Phantom read) 발생</li>
        </ul>
    </li>
    <li>
        <strong>READ COMMITED</strong>
        <ul>
            <li>다른 트랜잭션이 커밋한 이후의 데이터만 접근 가능
                <ul>
                    <li>데이터베이스에서 읽을 때 커밋된 데이터만 보게 된다.</li>
                    <li>데이터베이스에 쓸 때 커밋된 데이터만 덮어쓰게 된다.</li>
                </ul>
            </li>
            <li>한 트랜잭션 내에서 검색 결과가 비일관적인 현상 발생 (Non-Repeatable Read)
                <details>
                <summary><b> 반복 읽기 불가능(Non-Repeatable Read) </b></summary>
                  <div markdown="1">
                    <img src="https://github.com/user-attachments/assets/f4a4b70e-a6fe-4107-9d6f-2b08866216dc" alt="image" />
                  READ COMMITED에서 반복 읽기를 수행하면 다른 트랜잭션의 커밋 여부에 따라 조회 결과가 달라질 수 있다.
                  </div>
                </details>
            </li>
        </ul>
    </li>
    <li>
        <strong>READ UNCOMMITED</strong>
        <ul>
            <li>다른 트랜잭션에서 커밋되지 않은 내용에 접근 가능(Dirty Read)
                  <details>
<summary><b> 오손 읽기(Dirty Read) </b></summary>
<div markdown="1">
<img src="https://github.com/user-attachments/assets/927dc86c-59e1-43d1-9759-92eb9f037ff4" alt="image" />
  위 이미지만 보면 괜찮아 보인다. 하지만 아래 상황에서는 어떠한가?
<img src="https://github.com/user-attachments/assets/28847c44-ae21-4da1-a80d-2e589fd29d81" alt="image" />
  사용자 B는 id=51인 데이터에 대해 처리를 진행하고 있는데, 조회해보니 결과가 존재하지 않으면 시스템에 상당한 버그를 초래한다. → READ UNCOMMITED는 RDBMS 표준에서 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준임
</div>
</details>
            </li>
        </ul>
    </li>
</ol>

<table>
    <tr>
        <th></th>
        <th>Dirty Read</th>
        <th>Non-Repeatable Read</th>
        <th>Phantom Read</th>
    </tr>
    <tr>
        <td>READ UNCOMMITED</td>
        <td>발생</td>
        <td>발생</td>
        <td>발생</td>
    </tr>
    <tr>
        <td>READ COMMITED</td>
        <td>없음</td>
        <td>발생</td>
        <td>발생</td>
    </tr>
    <tr>
        <td>REPEATABLE READ</td>
        <td>없음</td>
        <td>없음</td>
        <td>발생 (MySQL은 거의 없음)</td>
    </tr>
    <tr>
        <td>SERIALIZABLE</td>
        <td>없음</td>
        <td>없음</td>
        <td>없음</td>
    </tr>
</table>
