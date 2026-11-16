## Діаграма 2 - відхилення оплати

![Діаграма 2](diagram2.png)

## Код програми:

```
sequenceDiagram
    actor User as Користувач
    participant UI as Інтерфейс
    participant Orders as Сервіс замовлень
    participant Timer as Таймер оплати
    participant Gateway as Платіжний шлюз

    activate User
    User->>UI: createOrder()
    activate UI
    UI->>Orders: createOrder()
    activate Orders
    Orders->>Orders: saveOrder(status=Pending)
    Orders->>Timer: startPaymentTimer(orderId, 10 хвилин)
    activate Timer
    Timer-->>Orders: timerStarted(deadline)
    deactivate Timer
    Orders-->>UI: orderCreated(orderId, Pending, deadline)
    deactivate Orders
    UI-->>User: showOrderPending(orderId, deadline)
    deactivate UI

    User->>UI: payOrder(orderId)
    activate UI
    UI->>Orders: pay(orderId)
    activate Orders
    Orders->>Gateway: charge(orderId, amount)
    activate Gateway
    Gateway-->>Orders: paymentRejected("Insufficient funds")
    deactivate Gateway
    Note over Orders: Замовлення залишається Pending
    Orders-->>UI: paymentFailed(Pending, "Insufficient funds")
    deactivate Orders
    UI-->>User: showPaymentError("Insufficient funds", retryUntil=deadline)
    deactivate UI

    loop Повторні спроби до дедлайну — 10 хвилин від створення замовлення
        User->>UI: retryPayment(orderId)
        activate UI
        UI->>Orders: retryPayment(orderId)
        activate Orders
        Orders->>Orders: verifyPendingBeforeDeadline(orderId, deadline)
        activate Orders
        Orders-->>Orders: Pending, дедлайн ще не настав
        deactivate Orders
        Orders->>Gateway: charge(orderId, amount)
        activate Gateway
        Gateway-->>Orders: paymentRejected("Insufficient funds")
        deactivate Gateway
        Note over Orders: Статус Pending; таймер продовжує працювати
        Orders-->>UI: paymentFailed(Pending, "Insufficient funds")
        deactivate Orders
        UI-->>User: showPaymentError("Insufficient funds", retryUntil=deadline)
        deactivate UI
    end
    Note over Timer,User: Повторна спроба не перезапускає таймер; після дедлайну повторити оплату не можна
    deactivate User
```
