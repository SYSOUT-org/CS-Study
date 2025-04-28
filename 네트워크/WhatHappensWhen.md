
# 웹 페이지가 브라우저에 보이는 과정

![image](https://github.com/user-attachments/assets/b22b2986-e88a-4dd1-8e48-f745cbb2a6a8)


## 1. 사용자 입력

사용자가 브라우저의 주소창에 naver.com을 입력한다.

## 2. 웹 페이지 URL

1. **브라우저 캐시 확인**
2. **OS의 DNS 기록 확인** (OS도 DNS 기록을 가지고 있음)
3. **라우터 캐시 확인** - 자체 DNS 레코드 캐시를 유지 관리하는 라우터와 통신

위 모든 단계에 실패하면 브라우저는 Recursive DNS 서버로 이동한다.

## 3. 도메인 네임

브라우저는 DNS 서버에 해당 도메인에 대한 IP 주소를 요청한다.

## 4. IP 주소 반환

DNS 서버는 naver.com에 해당하는 IP 주소를 반환한다. 이 IP 주소는 실제 웹 서버의 위치를 나타낸다.

![image](https://github.com/user-attachments/assets/c83dd844-4002-4edc-8331-aff99377246b)

![image](https://github.com/user-attachments/assets/025b29cc-477c-46b1-aadb-57059ce13d43)


## 5/6. HTTP 요청 메시지

브라우저는 TCP 통신을 통해 해당 IP 주소(네이버 서버)로 HTTP 요청을 전송한다.

![image](https://github.com/user-attachments/assets/da71da7c-5073-4d9c-ab7c-8f639ef9d41d)


## 7. 웹 페이지 URL (웹 서버로부터)

웹 서버는 다음과 같은 HTTP 요청을 수신한다.

![image](https://github.com/user-attachments/assets/02a84811-0733-48f3-91d2-3714cb6e5b7d)


## 8. 웹 페이지 데이터

웹 서버는 요청된 웹 페이지 데이터를 응답으로 보내준다.

![image](https://github.com/user-attachments/assets/b5568178-f272-4af2-964d-efab69c19e8e)


## 9/10. HTTP 응답 메시지

웹 서버는 웹 페이지 데이터를 HTTP 응답 메시지로 변환하여 클라이언트(브라우저)로 전송한다. 응답 메시지에는 상태 코드와 웹 페이지 데이터가 포함된다.

![image](https://github.com/user-attachments/assets/6a9ca3b0-9b08-4f1c-b6bd-fcbe6daea75d)


## 11. 웹 페이지 데이터

브라우저는 웹 페이지 데이터를 해석하고, 필요한 리소스(CSS, 이미지, JavaScript 등)를 추가로 요청하여 완전한 페이지를 렌더링한다.

![image](https://github.com/user-attachments/assets/0e69c775-8941-4709-970b-82e1a8653989)


## 12. 사용자 출력

최종적으로 사용자는 브라우저에서 naver.com 웹 페이지를 확인하게 된다.

![image](https://github.com/user-attachments/assets/84e21b01-2c8e-4bb7-a850-be14a5c4eb2c)


## 참고 문헌

- [주소창에 www.google.com을 입력했을 때 일어나는 과정](https://velog.io/@zioo/%EC%A3%BC%EC%86%8C%EC%B0%BD%EC%97%90-www.google.com-%EC%9D%84-%EC%9E%85%EB%A0%A5%ED%96%88%EC%9D%84-%EB%95%8C-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EA%B3%BC%EC%A0%95)
- [주소창에 naver.com을 입력했을 때 무슨 일이 일어날까?](https://velog.io/@gusdh2/%EC%A3%BC%EC%86%8C%EC%B0%BD%EC%97%90-naver.com%EC%9D%84-%EC%B9%98%EB%A9%B4-%EC%96%B4%EB%96%A4-%EC%9D%BC%EC%9D%B4-%EC%9D%BC%EC%96%B4%EB%82%A0%EA%B9%8C)
- [관련 유튜브 영상](https://www.youtube.com/watch?v=YahjHM9UNCA)
=======
> 과정을 이해해야만, 페이지가 뜨지 않았을 때 어디서 문제가 생겼는지 역추적을 하면서 파악해야 한다.
> 

**리다이렉트 → 캐싱 → DNS → IP 라우팅 → TCP 연결 구축 → 요청, 응답이 일어나는 TTFB → 컨텐츠 다운 → 브라우저 렌더링 과정 → 네이버 화면**

### 리다이렉트
- 리다이렉트가 있으면 다른 주소로 이동하는 진행을 하고, 없다면 그대로 해당 요청을 그대로 진행한다.

### 캐싱
- 어떤 서버에 요청을 했을 때, 예전에 요청을 한 것이 있으면 캐시가 생긴다.

### DNS
- Domain Name System Servers
- URL과 IP를 저장하고 있는 데이터베이스.
- IP주소가 8.8.8.8라고 한다면, 이를 도메인 www.google.com으로 매핑해주는 역할이다.
- 분산형 DB
### NAT
- Network Address Translation
- 사설 IP를 공인 IP로 변경에 필요한 주소 변환 서비스
- 라우터를 사용해 다수의 사설 IP(Private IP)를 하나의 공인 IP(Public IP) 주소로 변환하는 기술
### TCP/IP
- Transmission Control Protocol
- Internet Protocol
- IP 주소를 이용해 데이터를 전달했을 때, 그 데이터가 제대로 갔는지를 확인하는 통신 규약
### HTTP
- Hypertext Transfer Protocol
- 클라이언트와 서버가 서로 통신할 수 있게 하기 위한 언어로, “어떤 데이터 주세요”라고 Request하면, “이 데이터 줄게요”라고 Response한다.

---
# FLOW :D
## 1. URL 입력
    1. 사용자는 웹 브라우저 주소에 URL을 입력합니다.
## 2. DNS 조회
    1. 웹 브라우저는 호스트를 가지고 DNS한테 이 호스트에 대응되는 IP 주소를 요청합니다.
    2. 이 과정에서 바로 DNS에 요청을 하는 것이 아닌데, 다음과 같은 과정을 거칩니다.
    3. 브라우저 캐시 확인 : 최근 방문한 웹사이트일 경우 DNS 정보가 남아있는지 확인하여 있을 시 DNS에 따로 요청을 하지 않음.
    4. 운영체제 캐시 확인 : OS에서 DNS 캐시를 보관하는 경우 이를 사용한다.
    5. 호스트 파일 확인(hosts) : 매핑된 IP가 있는지를 확인한다.
    6. 이렇게도 없으면 비로소 DNS에 요청을 해 해당 호스트에 대응된 IP 주소를 받습니다.
    
    
    
## 3. TCP 연결(3-Way Handshake)
    1. 이렇게 얻은 IP 주소는  해당 서버와 TCP 연결을 해야합니다. TCP란, IP 주소를 이용해 데이터를 전달했을 때, 그 데이터가 제대로 갔는지를 확인하는 통신 규약으로 이 연결이 완료되어야지만 데이터를 주고받을 수 있습니다.
    2. **클라이언트** → **서버**: SYN 패킷 전송 (연결 요청)
    3. **서버** → **클라이언트**: SYN-ACK 응답 (연결 수락)
    4. **클라이언트** → **서버**: ACK 전송 (연결 완료)
## 4. TLS 핸드셰이크(HTTPS의 경우)
    1. 클라이언트 → 서버: ClientHello 메시지를 전송해 지원하는 암호화 방식과 TLS 버전을 전달
    2. 서버 → 클라이언트: ServerHello로 응답해 사용할 암호화 방식과 인증서를 제공
    3. 클라이언트가 서버의 인증서를 검증(유효성 검사)
    4. 클라이언트 ↔ 서버: 키 교환 후, 세션 키를 생성해 암호화된 데이터 교환을 진행
    
   
    
## 5. HTTP 요청 전송
## 6. 웹 서버 처리
    1. 웹 서버 확인(Nginx, Apache 등): 정적 파일 요청이면 바로 응답을 보내며, 동적 요청일 경우에는 애플리케이션 서버로 전달
    2. 애플리케이션 서버 처리(Spring, Django 등)
    3. 데이터베이스 접근
## 7. HTTP 응답 반환
## 8. 브라우저 렌더링


---

출처

https://www.youtube.com/watch?v=GAyZ_QgYYYo

https://www.youtube.com/watch?v=YahjHM9UNCA

https://www.youtube.com/watch?v=FQHNg9gCWpg

---

