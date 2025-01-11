# Normalization 정규형

## 1. 개념
### 1. 정의
> 정규화에 대한 정의 또한 매우 많아서 여러가지를 같이 적어봤다.
1. 정의 1
- 데이터베이스 설계 과정에서 중복 데이터를 최소화, 데이터의 무결성, 데이터의 효율성을 보장하기 위해 사용되는 기술

2. 정의 2
    - 이상 현상이 발생하는 릴레이션(테이블)을 분해하여 이상현상을 없애는 과정
    - 이상 현상이 있는 릴레이션은 이상현상을 일으키는 함수 종속성의 유형에 따라 등급을 구분 가능
    - 릴레이션은 정규형 개념으로 구분하며, 정규형이 높을 수록 이상 현상이 줄어드는 것을 의미

    3. 정의 3
    - 데이터 중복과 insertion, update, deletion anomaly를 최소화하기 위해 일련의 normal forms(NF)에 따라 relational DB를 구성하는 과정
        - Normal forms? 정규화 되기 위해 준수해야 하는 몇 가지 rule들이 있는데 이 각각의 rule을 normal form (NF)이라고 부른다.
            - Init table → 1NF → 2NF → 3NF → BCNF → 4NF → 5NF …
            - 처음부터 순차적으로 진행하며 normal form을 만족하지 못하면 만족하도록 테이블 구조를 조정한다. 앞 단계를 만족해야 다음 단계로 진행할 수 있다.

> 정리해보면, 이상 현상을 없애면서 중복 데이터를 최소화 하고 데이터의 무결성, 효율성을 보장하기 위해 사용되는 기술이라고 볼 수 있다.

### 2. 정규화(Normalization)이라는 단어의 어원

어떤 대상을 일정한 규칙이나 기준에 따르는 ‘정규적인’ 상태로 바꾸거나. 비정상적인 대상을 정상적으로 되돌리는 과정을 뜻한다.

그렇다면 정상적인 상태와 비정상적인 상태는 어떤 것을 의미할까?

데이터베이스에 정상적인 상태를 의미하는 것은 아래의 조건을 충족하는 경우를 의미한다.

    1. 중복 최소화: 동일한 데이터가 여러 위치에 중복 저장되지 않음
    2. 이상현상(삽입, 삭제, 갱신) 방지: 데이터를 삽입, 삭제, 수정할 때 논리적 오류가 발생하지 않음.
    3. 데이터 무결성 보장: 데이터 간의 관계가 일관성을 유지하며 의미가 명확함.

### 3. 정규화를 위해 필요한 개념 - 이상 현상

말 그대로, 데이터베이스에 연산을 진행함에 있어서 논리적 오류가 나타나는 부분을 이상현상이라고 의미한다. 데이터베이스 연산에는 삽입, 삭제, 수정이 있는데 연산마다 이상현상의 개념이 존재한다.

    1. 삭제 이상: 튜플 삭제 시, 같이 저장된 다른 정보까지 연쇄적으로 삭제되는 현상
    2. 삽입 이상: 튜플 삽입 시, 특정 속성에 해당하는 값이 없어 NULL을 입력해야 하는 현상
    3. 수정 이상: 튜플 수정 시, 중복된 데이터의 일부만 수정되어 일어나는 데이터 불일치 현상

예시를 살펴보자.

![image](https://github.com/user-attachments/assets/7330a04a-366b-4148-8dc0-1d77feed2004)

    1. 삭제 이상인 경우, 장미란 데이터가 포함된 튜플를 삭제한다고 하면 체육관 103이라는 데이터가 같이 삭제되어 다른 튜플들이 사용못한다.
    2. 삽입 이상인 경우, 튜플을 삽입하는 경우 해당하는 정보가 없어 NULL을 넣는 현상이다.
    3. 수정 이상인 경우, 박지성과 김연아라는 학생이 “공학관 110”이라는 데이터를 사용하고 있다. 하지만 두 강의는 독립적으로 이루어진 데이터이기 때문에 박지성 학생의 강의실을 201호로 변경하여도 김연아 학생은 110호로 그대로 유지가 되어, 같은 수업임에도 불구하고 동시에 수정이 되지 않아 강의실이 달라지는 현상이 발생한다.

> 💡여기에서 핵심은 서로 공유하는 데이터임에도 불구하고 각자의 튜플에 독립적으로 존재하기 때문에 이상현상이 생긴다. 이를 해결하기 위해서는 아래와 같이 정규화를 진행한다.



![image 1](https://github.com/user-attachments/assets/ccd66a2f-488a-4199-b46a-1ddc545b3bea)

### 4. 정규화를 위해 필용한 개념 - 함수 종속성


> 💡    1. 어떤 속성 A의 값을 알면 다른 속성 B의 값이 유일하게 정해지는 관계를 종속성이라 한다.
>       2. A → B라 표기하며 A를 B의 결정자라고 한다.
>       함수의 종속성은 DB를 설계하는 방법의 기본이라고 말한다.
아래와 같은 예시를 살펴보자. 아래의 예시를 보면 다음과 같이 종속성을 정리할 수 있다.

![image 2](https://github.com/user-attachments/assets/ab4c9e4a-18f6-4b5c-bee9-62bdc45cd6b8)

    1. 학생번호 → 학생이름
    2. 학과 → 학과 사무실
    3. 강좌 이름 → 강의실

그렇다면 “학과 → 학생이름도 종속 관계인가?” 를 봤을 때는 동명이 인 학생 이름으로 특정 학과를 결정하지 못하기 때문에 종속 관계가 아니다.


## 2. 종류

수많은 정규형들이 존재하지만 실제 현업에서는 3정규형 혹은 BCNF까지 진행한다고 한다. 그 이상으로는 연구 목적의 정규형이기 때문에 이번 정리에서는 BCNF까지 정리하려고 한다.

### 1. 제1 정규형

모든 attribute의 value는 반드시 나눠질 수 없는 단일한 값이어야 한다.

![image 3](https://github.com/user-attachments/assets/0d6ed8c8-807e-4c18-bfbb-4b661e8a1229)

![image 4](https://github.com/user-attachments/assets/d0ea20e4-8707-4c9f-8eb7-63f76e92a695)

    - 하나의 튜플을 값을 나눠서 저장하고 나머지 튜플에 대해서는 중복이 되도록 준비한다. 하지만 이러면 중복된 데이터가 생길 수 있다(PK 값 중복, ratio라는 비율 값 오류)
    - 여기에서 우리는 왜 중복된 데이터가 생긴 것인지 생각해봐야 한다.
        - key {account_id, card_id} , {bank_name, account_num, card_id}

          기존의 후보키에는 bank_name, account_num 으로 튜플을 식별할 수 있었지만, 지금은 bank_name, account_num, card_id 으로 튜플을 식별할 수 있게 된다.

        - non-prime attribute (class, ratio, empl_id, empl_name)

          과연 non-prime attribute들이 account_id, card_id 에 모두 반드시 의존해야 하는가? 라는 질문을 던졌을 때, card_id 값이 없이 account_id값만으로도 unique하게 식별할 수 있다.

          즉, 모든 non-prime attribute는 {account_id, card_id} 에 부분적 함수 종속(paritaly functionally dependent)이기 때문에 중복된 값이 생기는 것이다.

          또한, 모든 non-prime attribute 들이 {bank_name, account_num, card_id}에 partially dependent하다.

### 2. 제2 정규형

RULE: 모든 non-prime attribute는 모든 key에 fully functionally dependent 해야 한다.

문제의 원인을 찾아보면 card_id 때문이다. card_id때문에 부분적 종속이 생기기 때문에 해당 attribute를 테이블에서 분리시켜주면 된다.

![image 5](https://github.com/user-attachments/assets/e3fc12c0-f018-4743-8637-5c09dc201d69)

    - 이렇게 바꾼 테이블에 대해서는 non-prime attribute가 fully functionally dependent하게 되는가?
### 3. 제3 정규형

![image 6](https://github.com/user-attachments/assets/863e610f-a4d8-464f-a60f-c69dbde7d48b)

    - empl_id → empl_name
    - accoutn_id → empl_id
    - accoutn_id → empl_name

    - bank_name, account_num → empl_id
    - empl_id → empl_name
    - bane_name, account_num → empl_name



> 💡 이런 연결된 종속성을 이행적 종속, transitive FD라고 한다.
> 만약 X→ Y & Y → Z이면 X → Z를 transitive FD라고 하며,
> Y와 Z가 어떤 Key의 부분집합이면 안된다.
> 즉, 기존에는 empl_id를 통해서 empl_name 종속 관계를 확인할 수 있는데 account_id → empl_id라는 종속적 관계 때문에 추가적인 데이터 중복이 발생하게 되는 것이다.
Rule: 모든 non-prime attribute는 어떤 key에도 transitively dependent하면 안된다.
non-prime attribute와 non-prime attribute 사이에는 FD가 있으면 안된다.

![image 7](https://github.com/user-attachments/assets/faed9623-d06f-4e0e-a35d-53135e7dfe6e)

### 4. BCNF 정규형

![image 8](https://github.com/user-attachments/assets/e1420898-e7a5-4386-8e53-ba808732b3c5)

국민은행 계좌 등급: STAR → PRESTIGE → LOYAL

우리은행 계좌 등급: BRONZE → SILVER → GOLD

즉, class가 같으면 bank_name도 같다와 같은 함수적 종속이 일어난다. 그렇다면 중복된 은행 이름을 어떻게 해소할 수 있을까?

> Rule: 모든 유효한 non-trival FD X→ Y는 X가 super key 여야 한다. (X는 class, Y는 bank_name에 속한다)
> non-trival? Y가 X의 부분집합이 아닌 경우, non-trival functional dependency라고 한다.

![image 9](https://github.com/user-attachments/assets/7ea9ace1-6d51-4f6a-b7be-346c56b033f2)


## 3. 정규화의 장단점

1. 정규화의 장점
    - 이상 현상 제거로 인한 데이터 구조의 안정성 및 무결서 유지가 가능하다.
    - 중복된 데이터를 피할 수 있다.(저장 공간을 절약할 수 있다)
    - 데이터 구조가 논리적으로 명확해지며, 데이터 변경 시 연쇄적으로 발생할 수 있는 문제를 최소화한다(무결성 유지).
2. 정규화의 단점
    - 데이터가 많아지고 테이블이 많아지면 JOIN 연산 증가로 인해 쿼리에 대한 응답 시간 저하된다.
    - 쿼리 작성이 복잡해질 수 있다.
    - 높은 수준의 정규화를 수행하기 위해 데이터 모델링, 설계에 더 많은 시간이 소요된다.

> 💡DB 설계할 때, 과도한 조인과 중복 데이터 최소화 사이에서 적정 수준을 잘 선택할 필요가 있다.


## 4. 참고

[https://mangkyu.tistory.com/28](https://mangkyu.tistory.com/28)
[https://www.youtube.com/watch?v=EdkjkifH-m8](https://www.youtube.com/watch?v=EdkjkifH-m8)
[https://www.youtube.com/watch?v=5QhkZkrqFL4](https://www.youtube.com/watch?v=5QhkZkrqFL4)