🤔 트랜잭션이란?

데이터베이스의 상태를 변화시키기 위해서 수행하는 작업의 단위를 뜻한다.


데이터베이스는 질의어(SQL)를 이용하여 상태가 변경된다.

다만, 작업의 단위는 한번에 많은 질의어 명령문을 포함하게 된다.

명령어의 작업 순서에 따라 다른 전혀 다른 결과가 나오기 때문에 트랜잭션 설계를 잘 해야 한다.

<br>

📌 트랜잭션의 특징 (ACID)

### 원자성 (Atomicity)

연산은 모두 반영되거나, 모두 반영되지 않아야 한다. (All or Nothing)

### 일관성 (Consistency)

작업 처리 결과는 항상 일관적인 상태를 유지해야 한다.

### 독립성 (Isolation)

다른 트랜잭션의 작업에 끼어들거나 서로 영향을 줄 수 없다. 👉 트랜잭션 격리수준으로 관리

### 지속성 (Durability)

트랜잭션이 완료된다면, 결과는 영구적이어야 한다.

<br> 

🙏 트랜잭션 격리 수준 (Isolation level)

<낮다>

- <U>**READ UNCOMMITTED (커밋되지 않은 읽기)**</U>

  - COMMIT, ROLLBACK 여부에 상관없이 트랜잭션에서 값을 읽을 수 있다.

  - 🚨 DIRTY READ 발생

- <U>**READ COMMITTED (커밋된 읽기)**</U>

    - COMMIT이 된 데이터만 읽는다. = RDB 기본

  - 🚨 NON-REPEATABLE READ 발생

- <U>**REPEATABLE READ (반복 가능한 읽기)**</U>

    - 트랜잭션이 생성되기 이전의 트랜잭션에서 COMMIT 된 데이터만 읽는다. = MySQL/MariaDB 기본

  - 🚨 PHANTOM READ 발생

- <U>**SERIALIZABLE (직렬화 가능)**</U>

    - 데이터를 접근할 때, 항상 Lock을 걸고 데이터를 조회한다. = 성능 문제로 잘 안 씀

<높다>