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

## Діалог 2 — Перевірка діаграм


**Запит:**

Привіт, я додав свій spec.md до третьої лабораторної
Тепер хочу, щоб ти ще раз уважно перевірив чотири sequence-діаграми, які ти створив раніше
Переглянь lab-3/spec.md, lab-2/scenarios.md, lab-2/requirements.md та файли lab-3/diagram1.md, diagram2.md, diagram3.md і diagram4.md
Порівняй діаграми з вимогами та сценаріями другої лабораторної й знайди помилки або невідповідності
Особливо перевір, чи правильно працює 10-хвилинний таймер, чи не змінюється статус уже скасованого замовлення, чи правильно показані alt та opt, а також порядок повідомлень і activation bars.
Поки що нічого не виправляй і не змінюй файли, а просто напиши, які помилки ти знайшов, у яких діаграмах вони є та що потрібно уточнити в spec.md, щоб їх виправити


**Відповідь:**

Перевірив spec.md, scenarios.md, requirements.md і всі чотири діаграми. Файли не змінював.

Знайдені невідповідності
diagram1.md — VIP-знижка та 10-хвилинний ліміт. Успішний сценарій стосується VIP-клієнта, але знижка оформлена як необов’язкова гілка opt. Це робить її умовною, хоча в сценарії вона має застосовуватися. Також «within 10 minutes» зазначено лише в повідомленні від шлюзу: діаграма не показує перевірки, що замовлення ще Pending і дедлайн не минув, перш ніж встановити Paid.

diagram2.md — помилка оплати й повторна спроба. alt має лише гілку «до завершення таймера», без альтернативи для випадку, коли відповідь приходить після дедлайну. Сценарій описує помилку до завершення часу, тож сама гілка відповідає йому, але для alt бракує альтернативи; якщо показується лише умовна поведінка, доречніший opt. Повторна спроба згадана в повідомленні користувачу, але не показана як loop або окремий сценарій. Таймер лише згадується в нотатці — його робота не представлена повідомленнями.

diagram3.md — скасування без перевірки поточного статусу. Перевіряється лише відсутність підтвердження оплати, після чого встановлюється Cancelled. Немає умови, що замовлення досі Pending. Через повторний таймаут або гонку з оплатою діаграма не виключає повторного переходу статусу вже скасованого чи оплаченого замовлення. Умова має дозволяти перехід лише з Pending; для інших статусів зміни не повинно бути. Крім того, activation bar таймера починається після надсилання timeout і триває під час обробки замовлення та звільнення місць, хоча жодної подальшої роботи таймера не показано.

diagram4.md — activation bar і непоказана реакція на інші статуси. Activation bar платіжного шлюзу починається після надсилання вебхука; якщо її показано, вона має охоплювати відправлення повідомлення. alt охоплює лише статус Cancelled, без гілки для інших статусів. Нотатка стверджує, що квитки не створюються, але не показано явної відмови від їх створення; відсутність виклику Tickets натякає на це, але не робить рішення явним.

Порядок activation bars у діаграмах 1–3. Для ініціаторів — користувача в діаграмах 1–2 і таймера в діаграмі 3 — активація починається після першого повідомлення. Це може хибно показувати, що ініціатор активувався лише після відправлення виклику. Це відрізняється від активації отримувача після отримання повідомлення.

Що уточнити в spec.md
Точку відліку 10 хвилин: від створення замовлення чи від першої спроби оплати; також що саме рахується дедлайном — час фактичного успіху платежу чи час отримання вебхука системою.

## Діалог 3 - Виправлення віп знижки

**Запит:**

Привіт, я переглянув твої діаграми та помітив помилку в diagram1.md. У першому сценарії користувач уже має VIP-статус, тому знижка 10% повинна застосовуватися обов'язково, а ти показав її через opt, ніби це необов'язкова дія. Я вже додав це уточнення в lab-3/spec.md, тому переглянь його та виправ першу діаграму відповідно до нової вимоги. Також перевір, щоб перед зміною статусу замовлення на Paid система переконувалася, що замовлення ще Pending і 10 хвилин не минули. Не забудь, що за вимогами лабораторної в діаграмі має залишитися хоча б один фрагмент alt, opt або loop, а також правильні activation bars і відповіді між компонентами. Інші три діаграми та файли spec.md і другої лабораторної поки не змінюй. Після виправлення коротко поясни, що саме ти змінив

**Відповідь:**

Виправив лише diagram1.md:
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
- Зробив застосування знижки 10% обов’язковим кроком, без opt.
- Додав перехід markPaidIfPendingBeforeDeadline, який встановлює Paid лише якщо замовлення ще Pending і 10 хвилин не минули. У alt квитки створюються лише після успішного переходу; інакше статус не змінюється.
- Відповідь інтерфейсу тепер відображає фактичний результат переходу, а activation bar користувача починається перед його дією.
Перевірка git diff --check пройшла. Інші діаграми та файли не змінював.

## Діалог 4 - виправлення таймера оплати

**Запит:**

Привіт, тепер потрібно виправити другу sequence-діаграму, бо під час перевірки ми знайшли проблему з роботою 10-хвилинного таймера. Я вже додав уточнення в lab-3/spec.md: таймер запускається під час створення замовлення, після відхилення оплати продовжує працювати, а повторна спроба не запускає його заново. Переглянь spec.md і виправ lab-3/diagrams/code/diagram2.md відповідно до цих вимог. Покажи, що платіжний шлюз відхиляє оплату через Insufficient funds, замовлення залишається Pending, користувач бачить помилку й може повторити оплату до завершення 10 хвилин. Врахуй, що цей сценарій описує відхилення платежу, а не скасування замовлення. Зроби зрозумілий фрагмент alt, opt або loop, правильно покажи виклики, відповіді й activation bars. Не змінюй інші діаграми, spec.md та файли попередніх лабораторних. Після виправлення коротко поясни, що змінив.

**Відповідь:**

Виправив diagram2.md:
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
таймер запускається під час створення замовлення, а повторні спроби використовують той самий дедлайн. Діаграма показує відмову шлюзу з причиною Insufficient funds, статус Pending, повідомлення користувачу та цикл повторних спроб до завершення 10 хвилин. Скасування замовлення не показується.

Змінено лише цю діаграму; перевірив її вміст. Тести не запускав — це документаційна зміна.
