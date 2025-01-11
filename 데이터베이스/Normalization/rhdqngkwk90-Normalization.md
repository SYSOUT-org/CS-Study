🌟CS Study - Normalization(정규화)

📅 Week
Week 2 of Jan

📚 Content

--- 
### 🤔 정규화란?
직역하면 '관계 정상화'라는 뜻으로,
데이터를 일정한 기준에 따르는 '정규적인' 상태로 바꾸거나,
비정상적인 형태를 정상으로 되돌리는 과정을 말한다.

--- 
### 📌 데이터베이스 정규화
데이터베이스 설계 과정에서 이상현상(anomaly)이 있는 릴레이션을 분해하여,
데이터 구조를 조직화하고 최적화하는 과정을 말한다.

--- 
### 📌 정규화의 목적
- 데이터 중복 최소화
- 데이터 삽입, 수정, 삭제 시 발생할 수 있는 이상 현상 방지
- 데이터 무결성 유지
- 관계의 명확화
- 효율성 향상


**💡 이상 현상**
정규화를 거치지 않은 데이터에서 발생할 수 있는 현상을 말한다.
- 삽입 이상 (Insertion Anomaly) : 데이터 삽입 시 의도와 다른 값들도 삽입
- 삭제 이상 (Delete Anomaly) : 데이터 삭제 시 의도와 다른 값들도 연쇄 삭제
- 갱신 이상 (Update Anomaly) : 속성값 갱신 시 일부 튜플만 갱신되어 모순 발생


**💡 무결성**
데이터의 정확성, 일관성, 유효성이 유지되는 것
- 정확성 : 중복이나 누락이 없는 정확한 상태
- 일관성 : 원인과 결과의 의미가 연속적으로 보장되어 변하지 않는 상태
- 유효성 : 사용자로부터 값을 입력받을 때 유효한 값만 입력되는 상태

- 종류
  - 개체 무결성(Entity integrity)
  - 참조 무결성(Referential integrity)
  - 도메인 무결성(Domain integrity)
  - Null 무결성(Null integrity)
  - 고유 무결성 (Unique integrity)
  - 키 무결성 (Key integrity)
  - 관계 무결성 (Relationship integrity)

--- 
### 📌 정규화 단계
각 단계의 테이블은 "정규형(Normal Form)"이라고 불리는 규칙을 따른다.

- 정규화 : 정리정돈 과정 - 방 안에 있는 물건들을 정리한다.
- 정규형 : 정리정돈 규칙 - 박스에는 하나의 물건만 넣는다.


--- 
### 1️⃣ 제 1정규형 (1NF, First Normal Form)
속성 값이 **원자적(Atomic)** 이어야 한다.
= 하나의 셀에 여러 값을 저장할 수 없다는 뜻

<img src="https://github.com/user-attachments/assets/2da2314d-a362-4fe9-98a1-6baac7ead58f" alt="제 1정규형 적용 전" width="700"/>

###### <제 1정규형 적용 전>


위의 테이블에서는 운영체제 과목을 듣는 수강자를 한번에 저장하고 있다.
하나의 컬럼에 다중 값이 저장된 상태이다.


**💥 갱신/삭제 이상**
하나의 값만 변경하고 싶어도 전체 값이 함께 변경되어야 한다.
운영체제 과목을 수강하는 수강자 중 홍길동이 개명해서 황길동으로 바꾸게 된다면,
자료구조 과목과 운영체제 과목의 모든 행에서 홍길동을 찾아서 수정해야 한다.
만약 자료구조 수강자 홍길동만 황길동이 된다면, 자료구조 수강자 홍길동을 여전히 남아있다.
이로 인해 **데이터 일관성**이 깨진 것을 확인할 수 있다.

<img src="https://github.com/user-attachments/assets/bf049368-1b58-4d6b-a854-4582d39732c3" alt="제 1정규형 적용 후" width="700"/>

###### <제 1정규형 적용 전 후>


--- 
### 2️⃣ 제 2정규형 (2NF, Second Normal Form)
**부분 함수 종속성을 제거**해야 한다.
기본 키의 부분집합에 의존하는 컬럼들로만 구성되어야 한다.

<img src="https://github.com/user-attachments/assets/51707536-3da8-4e81-8212-da0ca2b21984" alt="제 2정규형 적용 전" width="700"/>

###### <제 2정규형 적용 전>

위의 테이블에서는 과목 ID에 대한 종속성을 가지는 데이터와, 학생 ID에 대한 종속성을 가지는 데이터를
하나의 테이블에 가지고 있다.


**💥 삽입/수정/삭제 이상**
새로운 과목이 추가될 때 해당 과목을 수강하는 학생 정보까지 함께 입력해야 한다.
이런 방식은 **데이터 중복**이 발생한다.

<img src="https://github.com/user-attachments/assets/3ed9faef-62a2-4804-aa0a-0abdd206c95b" alt="제 2정규형 적용 후" width="700"/>

###### <제 2정규형 적용 후>
👉 키의 부분집합에 의존하는 컬럼들을 테이블로 분리하고, 그 사이의 관계를 설정하는 관계 테이블을 생성한다.


--- 
### 3️⃣ 제 3정규형 (3NF, Third Normal Form)
**이행적 함수 종속성을 제거**해야 한다.
비주요 속성들이 다른 비주요 속성에 의존할 때, 이행적 종속이 발생한다. 이럴 경우 이를 해결하기 위해 테이블을 분리해야 한다.


**🤔 이행적 함수 종속?**
A → B (종속) 이고 B → C (종속) 이라면 A → C (종속) 인 형태

<img src="https://github.com/user-attachments/assets/20c0b6f4-0885-468d-9c36-fdb6aa3f2cb9" alt="제 3정규형 적용 전" width="700"/>

###### <제 3정규형 적용 전>


공학인증 여부는 공학인증 번호에 종속되는 개념이다.
과목 ID를 통해 간접적으로 종속되어 있기 때문에 분리해준다.

<img src="https://github.com/user-attachments/assets/5c58ba9b-50ec-479d-8593-f3ee5bb2dede" alt="제 3정규형 적용 후" width="700"/>

###### <제 3정규형 적용 후>

--- 
**🤔 그 이후 단계는?**

대부분의 데이터베이스 설계에서는 1NF, 2NF, 3NF까지 가장 많이 적용된다.
- BCNF, 4NF, 5NF : 일부 고급 설계나 복잡한 데이터베이스 시스템에서 필요한 경우 적용한다.



--- 
### ➕ 추가
아래 테이블은 프로젝트를 진행하면서 구성했던 내용으로,
나름 정규화 작업을 마쳤다고 생각한 결과물이다.
각 단계를 공부하면서 테이블에 적용하여 설명을 해보려고 한다.

- 디자이너 테이블
  - 디자이너 정보
  - 주소 정보
  - 이미지 URL


- 포트폴리오 테이블
  - 디자이너 아이디 (FK)
  - 포트폴리오 내용
  - 동영상 URL


- 포트폴리오 이미지 테이블
  - 포트폴리오 아이디 (FK)
  - 이미지 URL

- 설명
  한 명의 디자이너는 3개의 포트폴리오를 가질 수 있다.
  하나의 포트폴리오에는 1개의 동영상과 3개의 이미지를 업로드할 수 있다.

<img src="https://github.com/user-attachments/assets/a6653b65-8549-454d-97b4-e7c1e90476ed" alt="디자이너 테이블" width="400"/>

###### <디자이너 테이블>

<img src="https://github.com/user-attachments/assets/aa2d8485-598a-4093-8d4a-0bffdc6d97f3" alt="포트폴리오 테이블" width="400"/>

###### <포트폴리오 테이블>

<img src="https://github.com/user-attachments/assets/0bbb9bb1-2c5a-468d-bfe3-21d61020e412" alt="포트폴리오 이미지 테이블" width="400"/>

###### <포트폴리오 이미지 테이블>

<img src="https://github.com/user-attachments/assets/cc4b902b-d8cf-426d-b81f-c44d0c982624" alt="테이블 관계" width="400"/>

###### <테이블 관계>

-> 미완


🔗 참고 링크
(무결성) https://velog.io/@zionedoha/%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EB%AC%B4%EA%B2%B0%EC%84%B1%EC%9D%B4%EB%9E%80
(정규화) https://hstory0208.tistory.com/entry/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%A0%95%EA%B7%9C%ED%99%94Normalization%EB%9E%80-%EC%98%88%EC%8B%9C%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%B4%EB%B3%B4%EC%9E%90
(블로그) https://blog.naver.com/ohhappyday030/223722036852