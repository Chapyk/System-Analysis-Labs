## Діаграма 1 - віп оплата

![Діаграма 1](diagram1.png)

## Код діаграми
```
sequenceDiagram
    actor User as Користувач
    participant UI as Інтерфейс
    participant Orders as Сервіс замовлень
    participant Gateway as Платіжний шлюз
    participant Tickets as Сервіс квитків
    participant Email as Email-сервіс

    activate User
    User->>UI: confirmPayment(orderId)
    activate UI
    UI->>Orders: pay(orderId)
    activate Orders
    Orders->>Orders: calculateTotal(orderId)
    Orders->>Orders: applyDiscount(10%)
    Orders->>Gateway: charge(orderId, сума зі знижкою)
    activate Gateway
    Gateway-->>Orders: paymentResult(Success, within 10 minutes)
    deactivate Gateway
    Orders->>Orders: markPaidIfPendingBeforeDeadline(orderId)
    Orders-->>Orders: transitionResult(updated/notUpdated)
    alt Статус змінено на Paid до завершення 10 хвилин
        Orders->>Tickets: generateTickets(orderId, QR-коди)
        activate Tickets
        Tickets-->>Orders: ticketsCreated(tickets)
        deactivate Tickets
        Orders->>Email: sendTickets(userEmail, tickets)
        activate Email
        Email-->>Orders: emailSent
        deactivate Email
    else Замовлення не Pending або минуло 10 хвилин
        Note over Orders: Статус не змінюється, квитки не створюються
    end
    Orders-->>UI: paymentResult(transitionResult)
    deactivate Orders
    UI-->>User: showPaymentResult(transitionResult)
    deactivate UI
    deactivate User
```