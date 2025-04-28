## 🚩 인터넷 = Web의 동작 방식
### 📌 단어 개념
- `DNS (Domain Name System)` : 도메인 이름 시스템 서버, URL들의 이름과 IP 주소를 저장하고 있는 데이터베이스   
👉🏻 숫자로 된 IP 주소 대신 사용자가 사용하기 편리하도록 도메인 이름을 매핑해주는 역할
- `TCP/IP (Transmission Control Protocol/Internet Protocol)` : 데이터가 어떻게 전달되는지 정의하는 통신 규약   
👉🏻 송신자가 수신자에게 IP 주소를 사용해서 데이터를 전달하고, 그 데이터가 어제대로 갔는지에 대해 이야기하는 것 
- `HTTP (HyperText Transfer Protocol)` : 클라이언트와 서버가 서로 통신할 수 있게 하기 위한 언어를 정의하는 어플리케이션 규약   
👉🏻 요청(Request)과 응답(Response)으로 이루어져있음, HTML 문서를 주고 받는데에 사용

### 📌 웹페이지 요청 및 응답 과정
#### 1. 사용자가 웹브라우저 검색창에 `www.google.com` 입력
- 입력 도중, 자동 완성 기능이 실행되어 브라우저가 제안을 표시
- 사용자가 Enter를 누르면 요청이 본격적으로 시작

#### 2. 웹 브라우저는 캐싱된 DNS 기록 확인
> *(브라우저 캐시 → OS 캐시 → router 캐시 → ISP 캐시 순으로 검색)*
>

1️⃣ 가장 먼저 **브라우저 캐시**를 확인  
브라우저는 일정기간 동안의 DNS 기록들을 저장함  
DNS query가 이 곳에서 가장 먼저 실행  
🟰 최근 방문한 웹사이트 정보 저장  
2️⃣ 그 다음에 브라우저는 **OS 캐시**를 확인  
브라우저 캐시에 웹사이트 이름의 IP 주소가 발견되지 않았다면,   브라우저는 systemcall을 통해서 OS가 저장하고 있는 DNS    기록들의 캐시에 접근   
3️⃣ 그 다음에는 **router 캐시**를 확인  
컴퓨터에 DNS 기록을 찾지 못하면 브라우저는 DNS 기록을 캐싱하고 있는 router와 통신을 해서 탐색  
4️⃣ 그래도 못 찾는다면 마지막으로 **ISP 캐시**를 확인  
ISP는 DNS 서버를 구축하고 있고 브라우저가 마지막으로 DNS 기록이 있기를 바라며 접근      
(ISP : 인터넷 서비스 공급자의 약자 ex. KT, SKT, ..)  
> `👉🏻 위 캐시에 IP 주소가 없다면, DNS 서버에 요청`  

####  3. 웹 브라우저가 HTTP를 사용하여 DNS 서버에게 입력된 도메인 주소 요청 
  - 캐싱된 IP 주소가 없으면 DNS 서버에서 google.com의 IP 주소 검색
  
#### 4. DNS 서버가 IP 주소 응답
- ISP(Internet Service Provider)의 DNS서버가 호스팅하고 있는 서버의 IP주소를 찾기 위해 DNS query를 보냄
  - DNS query의 목적 
    - DNS 서버들을 검색해서 해당 사이트의 IP주소를 찾는데에 있음  
    - IP주소를 찾을 때 까지 DNS서버에서 다른 DNS서버를 오가며 에러가 날때까지 반복적으로 검색 🟰 **`recursive search`**
    
![DNS 계층](https://velog.velcdn.com/images/sapientia/post/a43c1d45-c30f-4d25-a895-d3c7bce4114f/image.png)

> 1️⃣ DNS recursor가 루트 네임 서버(Root Name Server) 문의   
> 2️⃣ .com 도메인 네임 서버로 리다이렉트  
> 3️⃣ google.com 네임 서버로 리다이렉트  
> 4️⃣ 최종적으로 www.google.com에 해당하는 IP 주소 반환  
> 5️⃣ 찾은 주소를 DNS recursor로 보내기  

👉🏻 원하는 DNS기록을 가진 DNS서버에 도달할 때까지   
클라이언트 ↔️ 서버를 여러번 오가는 과정을 가짐 

#### 5. 웹 브라우저가 웹 서버에 HTTP 요청 전송
- 브라우저는 IP 주소를 이용해 웹 서버와 연결을 시도

- TCP/IP Three-way Handshake를 통해 연결 확립
  - 클라이언트가 SYN 패킷 전송 (연결 요청)
  - 서버가 SYN-ACK 패킷 응답 (요청 수락)
  - 클라이언트가 ACK 패킷 전송 (연결 확정)

#### 6. WAS(웹 애플리케이션 서버)와 Databse에서 우선 웹페이지 작업을 처리
웹 서버 혼자서 모든 로직을 수행하고 데이터를 관리할 수 있다면 간단하지만, 그럴 경우 서버 과부화 발생 가능    
👉🏻 서버의 일을 돕는 조력자 역할을 하는 것이 **웹 에플리케이션 서버(WAS)**
- WAS는 사용자의 컴퓨터나 장치에 웹 애플리케이션을 수행해주는 미들웨어

> ✔️ 웹서버 ↔️ 웹어플리케이션서버(WAS)
> - 웹서버 : 정적인 컨텐츠(HTML, CSS, IMAGE 등)를 요청받아 처리   
> - WAS : 동적인 컨텐츠(JSP, ASP, PHP 등)를 요청받아 처리     
👉🏻 보안 문제로 인해 직접 클라이언트 요청을 처리하지 않고, 웹 서버를 통해 요청을 받음

#### 7. 웹 서버가 클라이언트에 응답 반환
- 요청된 웹페이지 데이터와 함께 HTTP 응답(Response) 전송
- 응답에는 HTML 문서, 상태 코드(Status Code), 캐싱 정책(Cache-Control) 등이 포함

> 
✔️ 1xx : 정보만 담긴 메세지  
✔️ 2xx : response 성공 (예: 200 OK)  
✔️ 3xx : 클라이언트를 다른 URL로 리다이렉트 (예: 301 Moved Permanently)  
✔️ 4xx : 클라이언트 측에서 에러 발생 (예: 404 Not Found)  
✔️ 5xx : 서버 측에서 에러 발생 (예: 500 Internal Server Error)

#### 8. 웹 브라우저가 HTML 문서를 해석하고 페이지 렌더링
- 브라우저는 HTML 문서를 파싱하여 DOM(Document Object Model) 생성
- CSS 및 JavaScript 파일을 추가적으로 요청하고 적용하여 최종적으로 웹페이지를 화면에 표시
- 브라우저 캐시를 이용하여 정적 파일을 저장해 다음 방문 시 로딩 속도 향상

### 📌 결론
1️⃣ 사용자가 URL 입력 → DNS 조회 후 IP 주소 반환   
2️⃣ 웹 브라우저 → 웹 서버에 HTTP 요청 전송   
3️⃣ 웹 서버와 WAS가 요청을 처리하고 필요한 데이터 조회  
4️⃣ 서버가 응답 반환 → 웹 브라우저가 렌더링 후 페이지 표시


---

> 📜 참고 자료
> - https://velog.io/@zioo/%EC%A3%BC%EC%86%8C%EC%B0%BD%EC%97%90-www.google.com-%EC%9D%84-%EC%9E%85%EB%A0%A5%ED%96%88%EC%9D%84-%EB%95%8C-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EA%B3%BC%EC%A0%95
> - https://devjin-blog.com/what-happen-browser-search/