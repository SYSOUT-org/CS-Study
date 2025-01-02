# 트랜잭션

난 외우는 걸 못하니 이해해보자
대부분의 컴퓨터과학 기술들은 문제 → 해결의 과정에서 탄생한다 먼저 문제를 살펴보자

### RDB 와 RDBMS 가 생긴 뒤, 이런 문제가 생겼다

- RDB 에 유저들의 계좌 데이터를 account 테이블을 만들어 관리하고 있다
- user1 이 user2 에게 1000원을 송금하려고 ATM 기 앞에 섰다
- 송금을 하려면 우선 user1 의 잔액과 계좌 상태, 그리고 user2 의 계좌 상태를 확인해야한다 문제가 없다면 user1 의 잔액에서 1000원을 빼고 user2 의 잔액에서 1000원을 더해야한다
- 위 과정을 구현하기 위해 ATM 기에선 RDBMS 에게 내릴 명령어들을 단계별로 정의했다

```sql
select balance, status from user where id = 1;
select status from user where id = 2;
update user set balance = balance - 1000 where id = 1;
update user set balance = balance + 1000 where id = 2;
```

- validation 이 끝나고 user1 의 잔액에서 1000원을 차감했다 이제 user2 의 잔액에서 1000원을 더할려고 할 때, ATM 기가 맛이 가버렸다

```sql
select balance, status from user where id = 1;
select status from user where id = 2;
update user set balance = balance - 1000 where id = 1;
# 여기서 ATM 기 다운
```

- user2 의 잔액은 그대론데 user1 의 잔액은 1000원이 빠졌다
- 일련의 명령어로 하나의 행위를 완성하는 과정에서 데이터의 정합성이 깨지는 문제가 발생한 것이다
- 그렇다고 명령어 하나마다 If else 를 하면서 이전 명령의 성공 여부를 계속 체크할 순 없는 노릇이다

---

### 문제를 정의하기

- 어떤 행위를 위한 일련의 RDBMS 명령어들은 all or nothing 으로 데이터의 정합성이 지켜져야한다
- 즉 어떤 행위를 위한 일련의 RDBMS 명령어들은 하나의 집합으로 묶여 마치 하나의 명령처럼 처리될 수 있어야한다는 것이다
- 컴퓨터과학에선 이걸 어떻게 해결했을까
- 우선 일련의 명령어들을 하나의 집합으로 묶을 “단위” 를 정의해야했다

---

### Transaction?

- trans: 변화, 이전
- action: 행동
- 무언가 변화, 이전하는 행동
- Transaction 은 처리, 거래, 집행 등의 의미로 사용됨
- 처리, 거래, 집행 등의 의미로 쓰이는 Transaction 이란 단어를 논리적 “단위” 로 사용하기로 함
- 1 Transaction, 말 그대로 하나의 처리, 거래, 집행을 의미 두개의 처리면 2 Transaction
- 컴퓨터과학에선 하나의 행위 (송금, 예금, 등등) 를 하기 위한 일련의 명령어들을 묶어서 1 Transaction 으로 책정
- 그리고 Transaction 에 다음과 같은 속성을 부여함
- ACID (Atomic all or nothing, Consistency, Isolation, Durability)

---

### MySQL InnoDB 스토리지 엔진이 Transaction 과 A,C  를 보장하는 방법

- 먼저 MySQL 에선 Transaction 을 위한 Transaction Control Language 을 몇 가지 만들었다
- BEGIN 으로 트랜잭션 시작을 선언, 그럼 MySQL 은 id 를 부여해서 트랜잭션을 식별
- 시작될 때의 데이터 스냅샷을 찍어서 별도로 백업한다 이렇게 백업된 데이터를 Undo Log 라고 한다
- 변경 사항들은 실제 디스크에 쓰여지다가, COMMIT 을 하면서 트랜잭션 끝을 선언, 적당한 타이밍에 Undo Log 를 없앰
- 만약에 도중에 문제가 생겼을 시 ROLLEBACK, 언두로그가 디스크에 다시 쓰여짐 이렇게 Atomic 과 Consistency 를 보장

---

### MySQL InnoDB 스토리지 엔진이 Transaction 과 I 를 보장하는 방법

- A 와 C 는 커밋, 롤백과 언두로그로 보장한다 그렇다면 I 는?
- 특정 커넥션에서 데이터를 변경하는 도중에 다른 커넥션에서 데이터를 조회하면 트랜잭션 격리 수준에 맞게 변경 중인 레코드를 읽지 않고 언두 로그에 백업해둔 데이터를 읽어서 반환하기도 한다