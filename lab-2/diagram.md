graph TD

    Guest["Гість (Відвідувач)"]
    Client["Авторизований Клієнт"]
    Gateway["Платіжний шлюз"]

    Guest <|-- Client

    subgraph Система бронювання кінотеатру
        UC1["UC-1: Перегляд афіші та сеансів"]
        UC2["UC-2: Оплата та підтвердження замовлення"]
        UC3["UC-3: Вибір місць у залі"]
        UC4["UC-4: Обробка запізнілого сповіщення від банку"]
    end

    Guest --> UC1
    Client --> UC3
    Client --> UC2

    UC2 .->|include| UC3
    UC4 .->|extend| UC2

    Gateway --> UC4
