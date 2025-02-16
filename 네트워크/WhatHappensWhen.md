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
