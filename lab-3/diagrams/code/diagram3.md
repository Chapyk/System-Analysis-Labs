## Діаграма 3 — завершення часу оплати

```
sequenceDiagram
    participant Timer as Таймер оплати
    participant Orders as Сервіс замовлень
    participant Payments as Сервіс платежів
    participant Reservations as Сервіс бронювання місць

    Note over Timer: Минуло 10 хвилин від створення замовлення
    activate Timer
    Timer->>Orders: timeout(orderId)
    activate Orders
    Orders->>Orders: getOrderStatus(orderId)
    activate Orders
    Orders-->>Orders: status(Pending | Paid | Cancelled)
    deactivate Orders
    Orders->>Payments: hasPaymentConfirmation(orderId)
    activate Payments
    Payments-->>Orders: paymentConfirmed(true | false)
    deactivate Payments

    alt status == Pending and paymentConfirmed == false
        Orders->>Orders: cancelIfPendingAndUnpaid(orderId)
        activate Orders
        Orders-->>Orders: cancellationResult(cancelled, currentStatus)
        deactivate Orders
        alt cancelled == true
            Orders->>Reservations: releaseSeats(orderId)
            activate Reservations
            Reservations-->>Orders: seatsReleased
            deactivate Reservations
        else cancelled == false
            Note over Orders,Reservations: Статус не змінився; місця не звільняються
        end
        Orders-->>Timer: timeoutProcessed(cancellationResult)
    else status == Paid or status == Cancelled or paymentConfirmed == true
        Note over Orders: Не скасовувати повторно й не звільняти місця
        Orders-->>Timer: timeoutIgnored(currentStatus)
    end
    deactivate Orders
    deactivate Timer
```
