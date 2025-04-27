
## 상황 정의
단건 결제 로직을 구현해야 하는 상황이라고 생각해보겠습니다.<br/>
간략하게 보자면 다음과 같은 흐름을 가질 수 있겠네요.<br/>
> → 주문시작<br/> → 결제 진행<br/> → PSP(Payment Service Provider) API 호출<br/> → 결제 성공<br/> → 주문 완료.

결제는 전부 완료되거나 전부 실패해야 합니다 그럼 전체 로직을 트랜잭션으로 묶으면 될까요?

- 주문, 결제 요청 데이터는 저장이 잘 됐는데 pg결제 api 호출이 실패하면?
- 사용자 돈은 결제됐는데, 주문은 실패한 상태가 된다면?

여기서 넓은 의미로 분산 트랜잭션 문제가 발생합니다.

> 분산 트랜잭션?<br/>
하나의 논리적 작업이 둘 이상의 독립된 시스템(리소스 관리자) 에 걸쳐 처리..

트랜잭션 정합성이 요구되는 분산 환경(distributed consistency problem)이라고 볼 수 있습니다.

## 대안

### 1. 2PC (Two-Phase Commit)
 - 여러 시스템이 동시에 Commit/Abort를 맞추는 방법
 - 문제: 외부 시스템(PSP)은 2PC를 지원하지 않음. 네트워크, 락 문제. 비현실적.

### 2. SAGA 패턴
 - 각 서비스 트랜잭션 완료 후 실패 시 보상 트랜잭션 실행
 - 문제: PSP 결제는 “내가” 롤백할 수 없음. 결제 취소 실패 가능성 존재.

### 3. Direct Call + Retry → 성공 시 DB 저장 트랜잭션 수행
 ```java
 public void userPaymentRequest() {
     PaymentRequest request = createPaymentRequest();
 
     try {
         PaymentResponse response = callPgApiWithRetry(request); // 외부 PG 결제 승인 API 호출 + 재시도
 
         if (response.isSuccess()) {
             saveOrderSuccessInDb(response); // 성공이면 결제 성공 저장 (트랜잭션 시작)
         } else {
             saveOrderFailureInDb(response); // 실패면 결제 실패 저장 (트랜잭션 시작)
         }
     } catch (Exception e) {
         saveOrderFailureInDbWithError(e); // 예외 발생시 결제 실패 저장
     }
 }
 ```
 - 서버 부하/지연: PG 서버가 느리면 사용자도 몇 초~몇십 초 대기
 - 사용자 경험(UX) 나쁨: 결제 화면에서 오래 기다리다가 실패하면 매우 불쾌
 - 아키텍처 확장성 한계: 이벤트 기반 확장이 어려움 예를 들어 결제 완료 후 포인트 적립, 쿠폰 발급 등
   
### 4. Outbox 패턴

 - 주문 저장 트랜잭션 안에 “결제 요청 이벤트”를 함께 저장
 - 커밋 이후 별도 프로세스가 외부 PG API 호출
 - 장애 복구 가능, 정합성 보장

## 결제 처리에서 outbox 패턴

- 주문 저장과 결제 요청을 하나의 트랜잭션으로 묶는다.
- 외부 결제 호출은 트랜잭션 외부에서 안전하게 비동기 처리한다.
- 네트워크 장애나 시스템 장애에도 이벤트가 DB에 안전하게 저장되어 복구할 수 있다.
- PG 승인 실패 시 재시도, 복구 가능. (확장성)


## Outbox 패턴 개념

>DB에 데이터 저장할 때 메시지도 함께 저장하고,<br/>
나중에 안전하게 메시지를 꺼내서 보내는 방식이 Outbox 패턴<br/>
실패해도 메시지가 남아있기 때문에 다시 시도할 수 있어 안전

1. 주문 + 결제 요청 이벤트 저장 (같은 트랜잭션)
2. 트랜잭션 커밋
3. 큐(RabbitMQ 등)로 이벤트 발행
4. Consumer가 외부 PG 결제 API 호출
5. 성공 시 결제 완료 처리
6. 실패 시 재시도 또는 실패 상태 업데이트

---

“메시지 유실 방지”와 “재시도 가능성” 및 "확장성"이 핵심 강점

## 실전

### 주문 저장 및 Outbox 저장

```java
@Transactional
public void placeOrderAndSaveOutbox(OrderRequest request) {
    Order order = orderRepository.save(request.toOrder());

    PaymentOutbox outbox = PaymentOutbox.builder()
         .eventType("PAYMENT_REQUEST")
         .payload(toJson(order))
         .status(PENDING)
         .build();

    paymentOutboxRepository.save(outbox);
}
```

---

### Outbox Publisher

```java
@Scheduled(fixedDelay = 5000)
public void publishOutboxEvents() {
    List<PaymentOutbox> pendingEvents = outboxRepository.findByStatus(PENDING);

    for (PaymentOutbox event : pendingEvents) {
        rabbitTemplate.convertAndSend("paymaent.exchange", "payment.request", event.getPayload());
        event.markAsSent();
        outboxRepository.save(event);
    }
}
```

---

### Consumer (PG API 호출)

```java
@RabbitListener(queues = "payment.request.queue")
public void handlePaymentRequest(String payload) {
   PaymentRequest event = parseJson(payload);
   try {
      pgPaymentService.approvePayment(event);
      paymentService.markPaymentSuccess(event.getOrderId());
   } catch (Exception e) {
      paymentService.markPaymentFailed(event.getOrderId());
      throw new AmqpRejectAndDontRequeueException("PG 결제 실패", e); // DLQ로 이동
   }
}
```