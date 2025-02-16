## 개요
웹 브라우저 상에 'https://www.google.com' 을 입력하면 무슨일이 발생하는가?에 대한 명확한 대답을 할 수 없었습니다.
그래서 이번 기회에 간략하게 정리하고자 합니다.

## www.google.com 은 무엇인가?
네트워크에서 특정 호스트를 식별하는 기본적인 정보는 **IP 주소**입니다.
하지만 IP 주소는 숫자로 이루어져 있어 사람이 기억하기 어렵고, 변경될 가능성이 있기 때문에 **도메인 네임(Domain name)**을 사용합니다.
예를 들어, 'www.google.com', 'www.naver.com' 과 같은 도메인 네임은 사람이 쉽게 기억할 수 있는 문자열로, 실제 IP 주소와 매핑됩니다.
만약 IP 주소가 변경되더라도 도메인 네임과 새로운 IP 주소를 다시 매핑하면 사용자는 동일한 도메인 네임을 통해 접속할 수 있습니다.
이러한 매핑 정보는 **DNS(Domain Name System) 서버**에서 관리됩니다.

<br>

## TCP/IP (Transmission Control Protocol / Internet Protocol)
TCP/IP는 인터넷에서 데이터를 송수신하기 위한 표준 프로토콜입니다.
- **IP(Internet Protocol):** 데이터를 목적지까지 전달하는 역할을 합니다.
- **TCP(Transmission Control Protocol):** 데이터가 올바르게 전달되었는지 확인하고, 신뢰성을 보장하는 역할을 합니다.
즉, TCP/IP는 **IP 주소를 이용해 데이터를 목적지까지 전달하고, 데이터의 정확성과 신뢰성을 보장하는 통신 규약**입니다.

<br>

## 웹 브라우저 상에 http://www.google.com 을 입력하면?

### 1. DNS 조회 (Domain Name Resolution)
먼저, 웹 브라우저는 'www.google.com' 에 해당하는 IP 주소를 찾기 위해 **로컬 DNS 서버**에 해당 IP 주소가 캐시되어 있는지 확인합니다.
- 만약 로컬 DNS 서버에 해당 IP 주소가 저장되어 있다면, 해당 IP 주소를 응답합니다.
- 저장되어 있지 않다면, DNS 서버 계층 구조를 따라 순차적으로 IP 주소를 질의합니다.
  - **Root DNS 서버**에 요청을 보냅니다.
  - Root DNS 서버는 .com 도메인을 관리하는 **최상위 도메인(TLD) 서버**의 위치를 반환합니다.
  - TLD 서버는 google.com 도메인을 관리하는 **권한(Authoritative) DNS 서버**의 위치를 반환합니다.
  - 권한 DNS 서버가 'www.google.com' 에 대한 실제 IP 주소를 반환합니다.
![image](https://github.com/user-attachments/assets/52c94021-49da-414d-a01d-c4ef6c7a79e9)


### 2. TCP 연결 설정 (3-Way Handshake)
IP 주소를 얻은 후, 클라이언트는 해당 IP 주소로 TCP 연결을 설정합니다.
이 과정에서 3-way handshake가 수행됩니다.

![image](https://github.com/user-attachments/assets/7d8191cc-9e0b-46a0-9c7b-ced0e4812f6f)
- **3-Way Handshake 과정** 
  1. 클라이언트 → 서버: **SYN 패킷** 전송 (연결 요청)
  2. 서버 → 클라이언트: **SYN-ACK 패킷** 전송 (연결 요청 수락)
  3. 클라이언트 → 서버: **ACK 패킷** 전송 (연결 확립 완료)

TCP 연결이 설정되면, 클라이언트는 데이터를 안정적으로 송수신할 수 있습니다.


### 3. HTTP 요청 및 응답
TCP 연결이 설정되면, 클라이언트는 www.google.com 서버에 HTTP(S) 요청을 보냅니다. 
HTTPS의 경우 SSL/TLS 핸드셰이크가 추가로 수행되어 보안 연결을 설정합니다. 
이후, 클라이언트는 **GET 요청**을 통해 ‘www.google.com’의 리소스를 요청하고, 요청을 받은 서버는 필요한 데이터를 준비하여 HTTP 응답으로 보냅니다. 
이 HTTP 응답에는 요청한 웹 페이지의 HTML, CSS, JavaScript 등의 웹 페이지 데이터가 포함되어 있습니다.
브라우저는 받은 HTML 문서를 해석하고 추가 리소스(이미지 스타일 시트, 스크립트 등)를 로드하여 웹 페이지를 표시합니다.
![image](https://github.com/user-attachments/assets/7cb8b208-b308-4c5e-9c1c-fd24e4b47dcc)


### 4. TCP 연결 종료 (4-Way Handshake)
클라이언트와 서버 간의 데이터 교환이 끝나면, **4-Way Handshake**를 통해 연결을 종료합니다.
![image](https://github.com/user-attachments/assets/986e98e2-76dd-40e0-9df0-26293c0f033d)
- **4-Way Handshake 과정** 
  1. 클라이언트 → 서버: **FIN 패킷** 전송 (연결 종료 요청)
  2. 서버 → 클라이언트: **ACK 패킷** 전송 (요청 수락, 아직 데이터 전송 가능)
  3. 서버 → 클라이언트: **FIN 패킷** 전송 (서버도 연결 종료 요청)
  4. 클라이언트 → 서버: **ACK 패킷** 전송 (연결 종료 완료)

📌 **왜 3-Way가 아닌 4-Way Handshake인가?**
클라이언트가 FIN 패킷을 보냈다고 해서 서버가 즉시 연결을 종료할 수는 없습니다.
서버는 아직 보낼 데이터가 남아 있을 수 있기 때문에, FIN 패킷을 받은 후에도 데이터를 모두 전송한 뒤 FIN 패킷을 전송합니다.

📌 **TIME_WAIT** 상태란?
클라이언트는 서버의 FIN 패킷을 받은 후에도 일정 시간 동안 **TIME_WAIT 상태**를 유지합니다.
이는 네트워크 상에서 지연되거나 유실된 패킷이 있을 경우를 대비하기 위함입니다.

## 정리
1. **DNS 조회**를 통해 www.google.com의 IP 주소를 얻는다.
2. **TCP 3-Way Handshake**를 수행하여 서버와 연결을 설정한다.
3. **HTTP 요청**을 통해 웹 페이지 데이터를 요청하고, 응답을 받는다.
4. **TCP 4-Way Handshake**를 수행하여 연결을 종료한다.
