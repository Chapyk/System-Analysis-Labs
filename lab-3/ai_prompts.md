## Діалог 1 — Створення діаграм

**Запит:**

Привіт, я зараз роблю третю лабу, мені потрібно створити sequence-діаграми для системи бронювання квитків у кінотеатрі
Переглянь мій файл lab-3/spec.md, а також lab-2/scenarios.md і lab-2/requirements.md, щоб зрозуміти, як працює моя система і які сценарії я вже описав у другій лабораторній
На основі цього зроби 4 окремі sequence-діаграми у Mermaid, по одній на кожен сценарій, і збережи їх у файлах lab-3/diagram1.md, diagram2.md, diagram3.md та diagram4.md
У діаграмах покажи користувача, платіжний шлюз та потрібні компоненти системи, як вони між собою взаємодіють, які методи викликають і які відповіді отримують
Також у кожній діаграмі мають бути activation bars і хоча б один alt, opt або loop. Постарайся зробити все не надто складним, але щоб були показані всі важливі кроки
Діаграми повинні відповідати сценаріям із другої лабораторної, тому нічого нового вигадувати не потрібно. Першу і другу лабораторні та файл spec.md не змінюй
Коли закінчиш, коротко поясни, що ти зробив


**Відповідь:**

Створив чотири Mermaid sequence-діаграми за сценаріями з лабораторної 2:

diagram1.md — успішна оплата з VIP-знижкою:
```
sequenceDiagram
    actor User as Користувач
    participant UI as Інтерфейс
    participant Orders as Сервіс замовлень
    participant Gateway as Платіжний шлюз
    participant Tickets as Сервіс квитків
    participant Email as Email-сервіс

    User->>UI: confirmPayment(orderId)
    activate User
    activate UI
    UI->>Orders: pay(orderId)
    activate Orders
    Orders->>Orders: calculateTotal(orderId)
    opt Користувач має VIP-статус і доступну знижку
        Orders->>Orders: applyDiscount(10%)
    end
    Orders->>Gateway: charge(orderId, сума зі знижкою)
    activate Gateway
    Gateway-->>Orders: paymentResult(Success, within 10 minutes)
    deactivate Gateway
    Orders->>Orders: setStatus(orderId, Paid)
    Orders->>Tickets: generateTickets(orderId, QR-коди)
    activate Tickets
    Tickets-->>Orders: ticketsCreated(tickets)
    deactivate Tickets
    Orders->>Email: sendTickets(userEmail, tickets)
    activate Email
    Email-->>Orders: emailSent
    deactivate Email
    Orders-->>UI: paymentConfirmed(Paid)
    deactivate Orders
    UI-->>User: showConfirmation()
    deactivate UI
    deactivate User
```
diagram2.md — відмова платіжного шлюзу:
```
sequenceDiagram
    actor User as Користувач
    participant UI as Інтерфейс
    participant Orders as Сервіс замовлень
    participant Gateway as Платіжний шлюз
    participant Timer as Таймер оплати

    User->>UI: initiatePayment(orderId)
    activate User
    activate UI
    UI->>Orders: pay(orderId)
    activate Orders
    Orders->>Gateway: charge(orderId, поточна сума)
    activate Gateway
    Gateway-->>Orders: paymentResult(Insufficient funds)
    deactivate Gateway
    alt До завершення 10-хвилинного таймера
        Orders->>Orders: keepStatus(orderId, Pending)
        Note over Orders,Timer: Таймер продовжує відлік
        Orders-->>UI: paymentFailed(Insufficient funds)
        UI-->>User: showErrorAndSuggestRetry()
    end
    deactivate Orders
    deactivate UI
    deactivate User
```
diagram3.md — скасування після таймауту:
```
sequenceDiagram
    participant Timer as Таймер оплати
    participant Orders as Сервіс замовлень
    participant Reservations as Сервіс бронювання місць

    Note over Timer: Від створення замовлення минуло 10 хвилин
    Timer->>Orders: timeout(orderId)
    activate Timer
    activate Orders
    Orders->>Orders: hasPaymentConfirmation(orderId)
    Orders-->>Orders: false
    opt Підтвердження оплати не отримано
        Orders->>Orders: setStatus(orderId, Cancelled)
        Orders->>Reservations: releaseSeats(orderId)
        activate Reservations
        Reservations-->>Orders: seatsReleased
        deactivate Reservations
    end
    deactivate Orders
    deactivate Timer
```
diagram4.md — пізній вебхук після скасування:
```
sequenceDiagram
    participant Gateway as Платіжний шлюз
    participant Webhook as Обробник вебхуків
    participant Orders as Сервіс замовлень
    participant Tickets as Сервіс квитків
    participant Support as Служба підтримки

    Gateway-->>Webhook: paymentSucceeded(orderId)
    activate Gateway
    activate Webhook
    Webhook->>Orders: handlePaymentWebhook(orderId, Success)
    activate Orders
    Orders->>Orders: getStatus(orderId)
    Orders-->>Webhook: status(Cancelled)
    alt Замовлення вже скасовано
        Orders->>Orders: recordStatusConflict(orderId)
        Note over Orders,Tickets: Квитки для скасованого замовлення не створюються
        Orders->>Support: createPaymentReviewCase(orderId)
        activate Support
        Support-->>Orders: caseCreated(caseId)
        deactivate Support
    end
    deactivate Orders
    deactivate Webhook
    deactivate Gateway
```
У діаграмах показано відповідні компоненти, виклики й відповіді, activation bars та opt/alt-фрагменти. 
spec.md виявився порожнім; його та файли лабораторної 2 не змінював. git diff --check пройшов успішно.
