# 📌 브라우저에서 네이버 검색 시 네트워크 흐름이 어떻게 될까?

---

## 🔹 1. 물리적인 네트워크 장비 및 구조 설명
✔ 사용자의 요청이 **PC → 공유기 → ISP → 인터넷 백본 → 국제망 → 네이버 데이터센터** 로 전달됨  
✔ **네트워크 장비 역할**
- **라우터(Router):** 패킷을 목적지까지 전달
- **스위치(Switch):** 내부 네트워크에서 데이터 전송
- **방화벽(Firewall):** 보안 정책 적용
- **인터넷 백본(ISP, IX):** 대륙 간 트래픽을 최적의 경로로 전달

---

## 🔹 2. DNS 조회: 네이버의 IP 주소 찾기

✔ 사용자가 `www.naver.com`을 입력하면 **DNS 서버를 통해 IP 주소를 찾음**  
✔ **DNS 조회 과정 (계층적 동작)**

```plaintext
1️⃣ **브라우저/운영체제 캐시 확인**
  - 이전에 방문한 기록이 있다면 로컬 캐시에서 IP 주소를 반환하여 바로 요청을 수행 

2️⃣ **로컬 DNS 서버(ISP DNS) 조회**
  - 사용자의 네트워크(ISP)에서 운영하는 DNS 서버에 `www.naver.com`에 대한 IP 요청

3️⃣ **계층적 DNS 질의 수행**
  - **Root DNS 서버** → `.com`을 관리하는 **TLD(Top-Level Domain) DNS 서버** → `naver.com`의 네임서버 조회  

4️⃣ **네이버 네임서버가 실제 IP 주소 반환**
```

---

## 🔹 3. TCP 연결 (3-Way Handshake)
✔ 클라이언트가 네이버 서버와 **TCP 3-Way Handshake (SYN → SYN-ACK → ACK)로 연결 수립**  

### **3-Way Handshake (TCP 연결 수립)**

✔ 클라이언트가 네이버 서버와 **TCP 연결을 맺기 위해 3단계 과정**을 거침

```plaintext
1️⃣ SYN (Synchronize)
   • 클라이언트 → 서버: "연결 요청 (SYN 플래그 설정)"
   
2️⃣ SYN-ACK (Synchronize Acknowledgment)
   • 서버 → 클라이언트: "연결 요청을 받았고, 준비 완료"

3️⃣ ACK (Acknowledgment)
   • 클라이언트 → 서버: "연결 완료"
   • TCP 연결이 맺어지면 이후 HTTP 요청을 전송 가능
```

✔ **연결 후 데이터 전송**
- 연결이 맺어진 후, **HTTP 요청(GET, POST 등)**을 보냄

---

## 🔹 4. 웹 서버 & WAS & DB에서 요청 처리
✔ **클라이언트가 HTTP 요청(GET)을 보내면, 네이버 서버가 이를 처리**  
✔ **요청 과정**
- **웹 서버(Nginx, Apache 등):** 정적 리소스 제공 & 로드 밸런싱
- **WAS(Spring, Django, Node.js 등):** 동적 요청 처리 및 DB 연동
- **데이터베이스(MySQL, Redis 등):** 데이터 조회 및 가공

✔ **응답 과정**
- WAS가 필요한 데이터를 처리하여 HTML 응답 생성
- 웹 서버가 이를 클라이언트로 전달

---

## 🔹 5. 브라우저가 응답을 받아 렌더링
✔ **브라우저는 HTML, CSS, JS를 파싱하고 렌더링 엔진을 통해 화면을 구성**  
✔ **브라우저 렌더링 과정**

- DOM & CSSOM 생성
- Render Tree 구성
- Layout & Paint 단계  

✔ **네트워크 요청이 필요한 추가 리소스 (이미지, API 요청 등) 처리**


## 📌 **참고 자료**
-  **[inpa.tistory.com/entry/Web-DNS 개념 & 동작 ★ 알기 쉽게 정리](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-DNS-%EA%B0%9C%EB%85%90-%EB%8F%99%EC%9E%91-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4-%E2%98%85-%EC%95%8C%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%A0%95%EB%A6%AC)**
