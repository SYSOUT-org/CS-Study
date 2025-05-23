## 📌 개념 정리

| 구분 | 설명 | 관점 |
|------|------|------|
| **동기/비동기** | **응답을 처리하는 방식** (언제 처리하느냐) | 프로그램 **로직 흐름 관점** |
| **블로킹/논블로킹** | **응답을 기다리는 동안 쓰레드를 어떻게 쓰느냐** | **쓰레드 자원 관점** |

### 🔁 동기 vs 비동기

| 구분 | 설명 | 예시 |
|------|------|------|
| **동기(Synchronous)** | 요청 → 응답 받을 때까지 **기다림**.응답 없으면 **다음 작업 못함** | 📞 친구한테 전화해서 직접 받을 때까지 기다리는 것 |
| **비동기(Asynchronous)** | 요청 → **기다리지 않고 다음 작업 진행**. 응답 오면 그때 처리 | 📩 친구에게 문자 보내고 다른 일 하다가, 답 오면 확인하는 것 |

> ✅ **즉**: 동기는 “기다리고”, 비동기는 “기다리지 않음”

### ⛔ 블로킹 vs 논블로킹

| 구분 | 설명 | 예시 |
|------|------|------|
| **블로킹(Blocking)** | 요청을 보내고, 응답이 올 때까지 **쓰레드가 아무 일도 못함** | 💻 택배 기사한테 전화 걸고, 받을 때까지 **전화기 붙잡고 기다리는 상황** |
| **논블로킹(Non-blocking)** | 요청은 했지만, **쓰레드는 다른 일도 가능** | 💻 택배 기사한테 문자 보내고, **다른 일 하다가 답 오면 확인하는 상황** |

🔎 [쓰레드 정리 참고 링크](https://velog.io/@sapientia/OS-%EC%93%B0%EB%A0%88%EB%93%9C-%EC%93%B0%EB%A0%88%EB%93%9C-%ED%92%80)
🔎 [프로세스와 쓰레드 정리 링크](https://velog.io/@sapientia/OS-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C#-%EC%8A%A4%EB%A0%88%EB%93%9C-thread)
> ✅ **즉**: 블로킹은 “내가 계속 기다려야 하고”, 논블로킹은 “내가 다른 거 해도 됨”


---
### 🔄 조합으로 이해하기

![](https://velog.velcdn.com/images/sapientia/post/4a82e48d-edd5-4eb2-bbbc-c4dd59f8706b/image.png)

| 유형 | 설명 | 예시 | 특징 | 설명 |
|------|------|------|------|------|
| 🔴 **동기 + 블로킹** | 요청하고, 응답 기다리며 쓰레드도 점유 | `RestTemplate`, `InputStream.read()` | 가장 일반적인 방식 | 전화 걸고, 친구 받을 때까지 폰 붙잡고 기다림 |
| 🟡 **동기 + 논블로킹** | 잘 안 씀 (응답 기다리되 쓰레드는 자유) | 특수한 상황 | 거의 없음 | 친구한테 전화했는데, 폰이 똑똑해서 내가 응답 기다릴 동안 딴 거 해줌 |
| 🟢 **비동기 + 블로킹** | 요청은 보내고 딴 일도 가능하지만, 응답 처리 순간에 쓰레드가 막힘 | 일부 JS/GUI 환경 |예상치 못한 케이스에서 나타남 | 문자 보내고 딴 일하다가, 답장 오면 폰 화면만 계속 응시 |
| ✅ **비동기 + 논블로킹** | 요청하고 다른 일도 하고, 응답 오면 처리. 쓰레드 낭비 없음 | `WebClient`, `Node.js`, `Reactor` | 가장 현대적인 방식 | 문자 보내고 딴 일 하다가, 문자 오면 자동으로 알려줘서 처리함 |


#### 👉 WebClient는 **비동기 + 논블로킹** 방식으로 동작

---

## 📌 WebClient란?

Spring에서 제공하는 **비동기 + 논블로킹** HTTP 클라이언트

> 내부적으로 **Reactor** 라이브러리 기반 (`Mono`, `Flux`)으로 동작

### ✅ 특징
- RestTemplate의 대체제 (Spring 5부터 등장)
- **가볍고 유연한 구성**
- **논블로킹** 방식으로 **서버 자원 효율적**
- **비동기 처리**로 **빠른 응답 제공**
- `Mono<T>` (단일 응답), `Flux<T>` (스트림 응답) 반환
- baseUrl, Header, Body 등 자유롭게 구성 가능

---

## 📌 프로젝트에서 WebClient 사용 이유

| 항목 | 이유 | 설명 |
|------|------|------|
| **외부 API 응답 지연 가능성** | OpenAI GPT API는 평균 응답 시간이 **수 초** 걸릴 수 있음 | `RestTemplate` 같이 동기 방식 사용하면 **요청마다 쓰레드가 대기 상태**로 낭비됨 |
| **서버 자원 효율성** | WebClient는 논블로킹이므로 **서버 쓰레드를 점유하지 않음** | 많은 요청이 동시에 들어와도 **적은 리소스로 처리 가능** |
| **트래픽 증가 대응** | 사용자가 많아질수록 동기 방식은 병목이 발생 | WebClient는 **쓰레드 풀 낭비 없이** 빠른 처리 가능 |
| **유연한 구성** | GPT API 호출 시 **Header 설정, Request Body 구성**이 다양함 | WebClient는 이런 설정을 **코드 한 줄로 간단하게** 처리 가능 |
| **반응형 로직 구현** | `Mono.flatMap()`, `.subscribe()` 등으로 **비동기 흐름 제어** 가능 | GPT 응답 도착 후 로직 처리 (`Map 저장`, `MBTI 분석`, `Redis 알림`) 등 순차 처리 가능 |
| **UI 응답 최적화** | 백엔드가 GPT 응답 기다리며 막히지 않으니 **사용자 UI도 즉시 응답 가능** | 사용자 경험 (UX) 향상

---

## 📌 정리 요약

- `WebClient`는 **비동기 논블로킹 기반** HTTP 클라이언트
- OpenAI API 호출처럼 **응답 시간이 긴 외부 API**에 적합
- `Mono`를 반환하여 **반응형 처리 가능**
- 서버 리소스 절약 + 빠른 사용자 응답 처리 가능

👉 `WebClient`는 GPT처럼 **응답 시간이 긴 외부 API**를 처리할 때, **서버 자원을 아끼고** 동시에 **빠른 사용자 반응성을 보장하기 위해** 최고의 선택