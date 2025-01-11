# 정규화란?

- 관계형 데이터베이스의 설계에서 중복을 최소화하게 데이터를 구조화하는 프로세스


# 종류

### **1. 1NF(1정규화)**

- 각 컬럼이 원자값을 가져야한다.
    - 특정 도메인의 값이 “이순신, 홍길동” 이러면 안된다.
- 발생할 수 있는 이상
    - 갱신 이상
    - 삭제 이상

### **2. 2NF**

- 제 1 정규화를 완료한 테이블에서 부분 함수 종속성을 제거하는 과정
    - 회원 ID 101에서 데이터 중복이 발생

  ![image.png](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2F5cb9671a-c6f1-4913-a478-9bfc6f7ced38%2FUntitled.png?table=block&id=eaa8cb6d-7a50-4c27-b9cc-dbb6105f83e3&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1240&userId=&cache=v2)
    - 이를 회원 테이블, 상품 테이블, 구매 테이블로 분리하는 과정

      ![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2F736f71d6-8a82-42dd-9a9d-6b8bc3c31d9a%2FUntitled.png?table=block&id=ad10affb-d4d9-4eb1-a991-353446ddcaa8&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=480&userId=&cache=v2)

- 발생할 수 있는 이상
    - 삽입 이상
    - 수정 이상
    - 삭제 이상

### **3. 3NF**

- 제 2 정규화를 완료한 테이블에서, 이행적 함수 종속성을 제거하는 과정
- 이행적 함수 종속성이란?
    - A → B 종속, B → C 종속, A → C 종속인 관계를 말한다.

      ![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Fc5e8fc49-4318-4b01-9ae4-7e7756dddae7%2FUntitled.png?table=block&id=d4746a0f-7a94-4b46-8899-bd3426f144fc&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1180&userId=&cache=v2)

    - ID → 등급, 등급 → 할인율, ID → 할인율

      ![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Fbcbe7dc3-e5f6-4ae2-bb37-427d191580fb%2FUntitled.png?table=block&id=7b3b6216-6616-4503-a27e-c5d0460d3e66&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1180&userId=&cache=v2)


### **4. BCNF(보이스-코드 정규화)**

- 제 3 정규형을 만족하고, 모든 결정자가 후보키 집합에 속하는 경우
- 결정자가 후보기가 아닌 함수 종속을 제거하는 과정

![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Fb0084bc6-5630-4c0c-9309-bbff28d15607%2FUntitled.png?table=block&id=6519cb5e-7e27-4327-bd63-b2035d9ea977&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1300&userId=&cache=v2)

- (학생번호, 과목) 이 PK
- BUT! “지도 교수”로 “과목”을 알 수 있음
    - 후보키 집합(학생번호, 과목)에 속하지 않은 결정자가 존재하므로 BCNF를 만족하지 않는다.
- 이렇게 수정

![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2F5a1317d6-8f91-4ae1-a159-b740365bd579%2FUntitled.png?table=block&id=8fdf3096-320b-48d3-898b-66dc3453e6ad&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1300&userId=&cache=v2)

### **5. 4NF**

- 다치 종속을 제거하는 과정
    - 다치 종속이란(Multi-valued Dependency)?
        - 같은 테이블 내의 독립적인 두 개 이상의 컬럼이 또 다른 컬럼에 종속되는 것
        - A → B 에서 단일값 A와 다중 값 B가 존재하는 경우를 의미

![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Fd11b3bbe-b7eb-4182-8343-c2fc2e091bb8%2FUntitled.png?table=block&id=587ad482-99f6-45b7-9aa6-139bc1a98145&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=670&userId=&cache=v2)

- 개발자 -> 자격증, 언어인 상황
- 제 4 정규화를 통해 밑과 같이 수정

![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Ffa4009ba-f0fd-442f-b216-1ac5852433b4%2FUntitled.png?table=block&id=0a2f3708-b35f-4d4f-9ac3-2139c11b4232&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1300&userId=&cache=v2)

### **6. 5NF**

- 제 4 정규화를 만족해야하고 더 이상 비손실 분해를 할 수 없어야하는 경우
- 조인 종속을 제거하는 과정
    - 조인 종속(Joint Dependency)이란?
        - 하나의 릴레이션을 어려개의 릴레이션으로 분해했다가, 다시 조인했을 때 손실은 없지만 필요없는 데이터가 생기는 경우
- 예시
    - 4NF의 예시를 JOIN한 결과

      ![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2Fefa3b4d7-f486-4d60-8ec9-99402657c847%2FUntitled.png?table=block&id=7c397b6c-5dc1-4d35-a28d-6f369af61262&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=670&userId=&cache=v2)

    - 제 5정규화 거친 결과

  ![Untitled](https://lovely-salto-c54.notion.site/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2Fbf6d1577-a3ac-4ba7-b7a1-d57edd096aff%2F3a78fb19-c895-44b3-abcf-ec65ab63be67%2FUntitled.png?table=block&id=12527e26-7464-4b22-8244-ba140721dea5&spaceId=bf6d1577-a3ac-4ba7-b7a1-d57edd096aff&width=1240&userId=&cache=v2)


# 왜 정규화가 필요한가? (수정 중)
### 데이터 이상(Anomaly)를 방지하기 위해
- 작성 예정 

# 반정규화 및 비정규화

### **비정규화(Unnormalized Form)**

- RDB 정규화 중 어느 것도 충족시키지 못한 상태

### **반정규화(Denormalization)(=역정규화)**

- 하나 이상의 테이블에 데이터를 중복해 배치하는 최적화 기법
- 의도적으로 정규화 원칙을 위배하는 행위

👍 **장점**

- 데이터 조회 속도 증가 ⇒ 조인 비용이 덜 들어간다.
- 쿼리 단순화

👎 **단점**

- 데이터 갱신, 삽입 비용, 난이도 증가
- 데이터 일관성 깨진다.
- 중복 저장으로 인해 저장 공간 증가