**브라우저에 www.naver.com OR www.google.com와 같이 도메인을 검색했을 경우** 무슨 일이 발생할까? 이 질문은 면접에서도 가끔 물어본다고 한다.

들어가기 앞서, 관련된 용어들을 쪼금 정리하고 나아갈 예정이다.

### 📌 용어
- DNS

> Domain Name System Server로 "URL들의 이름" , "IP 주소"를 저장하고 있는 데이터베이스로, 웹 사이트를 접속하기 위한 주소록이라 생각하면 된다.
ex) IP주소(ex. 41,412.414.414) 대신 사용자가 사용하기 편리하도록
도메인 이름(ex. www.google.com)을 매핑해주는 역할이다.

- TCP/IP

> Transmission Control Protocol / Internet Protocol로 데이터가 어떻게 웹을 건너 여행하는지 정의하는 통신 규약이다. IP주소 체계를 따르며, TCP의 특성을 활용한다는 의미이다. 즉 송신자가 수신자에게 IP주소를 사용해서 데이터를 전달하고 그 데이터가 제대로 갔는지에 대해 이야기하는 것이다.

- HTTP

>Hypertext Transfer Protocol로 클라이언트와 서버가 서로 통신할 수 있게 하기 위한 언어를 정의하는 어플리케이션 규약으로, 쉽게 말해 요청과 응답으로 이루어져있어 "어떤 데이터 주세요"라고 요청하면, "이 데이터 줄게요" 라고 응답하는 것이라고 할 수 있다. 주로 HTML문서를 주고 받는데에 사용된다.

---

## ✅ 작동방식
![](https://velog.velcdn.com/images/wngus4278/post/7c6e8e8e-a97d-4c59-9be3-7ceb1f4240bc/image.png)

일단, 간단히 작동방식을 설명하면
> 1. 사용자가 브라우저에 도메인네임(www.naver.com)을 입력을 한다.
2. 사용자가 입력한 URL주소중 도메인네임부분을 DNS서버에서 검색하고, DNS 서버에서 해당 도메인 네임에 매핑되는 IP주소를 찾아서 사용자가 입력한 URL정보와 함께 전달한다.
3. 페이지 URL정보와 IP주소는 HTTP 프로토콜을 사용해 HTTP요청메세지를 생성하여, TCP프로토콜을 사용해 인터넷을 거쳐 해당 IP주소의 컴퓨터로 전송된다.
>
4. 도착한 HTTP 요청 메시지는 HTTP 프로토콜을 사용해 웹 페이지 URL정보로 변환돼 웹페이지 URL 정보에 해당하는 데이터를 검색한다.
5. 검색된 웹 페이지 데이터는 또 다시 HTTP 프로토콜을 사용하여 HTTP 응답 메시지를 생성하고 TCP 프로토콜을 사용하여 인터넷을 거쳐 원래 컴퓨터로 전송된다.
6. 도착한 HTTP 응답 메시지는 HTTP 프로토콜을 사용하여 웹 페이지 데이터로 변환되어 웹 브라우저에 의해 출력되어 사용자가 볼 수 있게 된다.

---
간략히 말하면 이런 식으로 진행이 된다. 하지만 이걸로는 부족하기에 쫌 더 자세히 알아보자.

## ✔️ DHCP Server?

![](https://velog.velcdn.com/images/wngus4278/post/533074d8-f652-4577-95c9-d2226b2cc6fb/image.png)

대부분 가정집에선 DHCP로 인터넷 접속을 하고있다. 호스트의 IP주소 및 TCP/IP 설정을 클라이언트에 자동으로 제공하는 프로토콜이다. 사용자의 PC는 사용자 자신의 IP주소, 가장 가까운 Router IP주소, 가장 가까운 DNS서버의 IP주소를 받는다. 이후 ARP(Address Resolution Protocol)을 이용하여 가장 가까운 라우터의 MAC 주소를 알아내면 외부와 통신할 준비가 완료된다.

> - ARP(Address Resolution Protocol) ?
>
네트워크 상에서 IP 주소를 물리적 네트워크 주소로 대응시키기 위해 사용되는 프로토콜이다.
>
- IP 주소 vs MAC 주소
>
IP 주소는 통신기기(PC, 스마트폰 등)에 할당된 식별번호다.
통신기기마다 고유하게 할당되어 있는 것이 아니라,
대부분 통신사에 일정 금액을 지불하고 받아오기 때문에 경우에 따라 바뀐다.
>
MAC 주소도 통신기기의 식별번호를 나타내지만,
임시적으로 다른 주체에 의해 할당되는 IP 주소와는 다르게 통신기기 하드웨어 자체에 부여된 고유한 식별번호다.
>
제품의 시리얼 넘버라고 봐도 되며, 세상에 단 하나밖에 없는 유니크한 값을 가지고, 변경이 불가능하다.

---
## ✔️ IP 정보 수신

위의 과정을 통해서 외부와 통신할 준비를 마치고, 웹 브라이저에 원하는 도메인을 입력하면, PC는 가장 먼저 브라우저 내 존재하는 브라우저 DNS cache공간을 보고 없다면 OS에 저장된 DNS Cache -> Router DNS Server -> Root DNS Server로부터 조회한다.

>Root DNS Servcer로 DNS Query를 송신한다면, Root Name Server에 www.google.com을 질의하고, Root 네임서버는 .com 네임서버의 ip 주소를 알려준다.

> 그 후 .com 네임서버에 www.naver.com을 질의하면 naver.com 네임서버의 ip 주소를 받고 그곳에 질의를 또 송신하면 www.naver.com의 IP 주소를 수신하게 된다.

![](https://velog.velcdn.com/images/wngus4278/post/f90a2af0-bf0e-4bce-9793-0825584cbbf3/image.png)


>이와 같이 여러번 왔다갔다 하는 이유는, 도메인의 계층화 구조에 따라 DNS 서버도 계층화 되어있기 때문이다. 이렇게 계층화되어 있으므로 도메인의 가장 최상단, 즉 가장 뒷쪽(.com, .kr 등등)을 담당하는 DNS 서버는 전세계에 13개 뿐이다.


---

## ✔️ 웹 서버 접속
> 웹 서버의 IP 주소까지 알았다. Http Request를 위헤 TCP Socket을 개방하고 연결한다. 이 과정에서 3-way hand shaking 과정이 일어난다. TCP 연결에 성공하면, Http Request가 TCP Socket을 통해 보내진다. 이에 대한 응답으로 웹 페이지의 정보가 사용자의 PC로 들어온다.


출처

https://github.com/WooVictory/Ready-For-Tech-Interview/blob/master/Network/%EC%A3%BC%EC%86%8C%EC%B0%BD%EC%97%90%20naver.com%EC%9D%84%20%EC%B9%98%EB%A9%B4%20%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94%20%EC%9D%BC.md

https://siahn95.tistory.com/147
