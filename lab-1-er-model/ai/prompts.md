Привіт! Сьогодні ти мій джун, а я твій начальник

Згенеруй ER-діаграму для системи бронювання кінотеатру у форматі Mermaid

Ось такі мають бути сутності та їхні атрибути:
1. USER (Користувач): id, email, phone, name
2. MOVIE (Фільм): id, title, duration_minutes, age_rating
3. GENRE (Жанр): id, name
4. HALL (Зал): id, name, capacity
5. SCREENING (Сеанс): id, movie_id, hall_id, start_time, price
6. TICKET (Квиток): id, screening_id, user_id, seat_row, seat_number, status

Як вони пов'язані між собою:
- Фільм і Жанр мають зв'язок багато до багатьох
- Фільм і Сеанс - один до багатьох
- Зал і Сеанс - один до багатьох
- Користувач і Квиток - один до багатьох
- Сеанс і Квиток - один до багатьох

Напиши це все правильним синтаксисом Mermaid erDiagram!
Привіт, начальнику 😎 Задача зрозуміла. Ось готова ER-діаграма саме у синтаксисі Mermaid erDiagram:

erDiagram

    USER {
        int id PK
        string email
        string phone
        string name
    }

    MOVIE {
        int id PK
        string title
        int duration_minutes
        string age_rating
    }

    GENRE {
        int id PK
        string name
    }

    HALL {
        int id PK
        string name
        int capacity
    }

    SCREENING {
        int id PK
        int movie_id FK
        int hall_id FK
        datetime start_time
        decimal price
    }

    TICKET {
        int id PK
        int screening_id FK
        int user_id FK
        int seat_row
        int seat_number
        string status
    }

    MOVIE }o--o{ GENRE : "has"
    MOVIE ||--o{ SCREENING : "has"
    HALL ||--o{ SCREENING : "hosts"
    USER ||--o{ TICKET : "buys"
    SCREENING ||--o{ TICKET : "contains"
Як читати зв'язки
MOVIE }o--o{ GENRE → багато до багатьох — фільм може мати кілька жанрів, жанр може належати багатьом фільмам.
MOVIE ||--o{ SCREENING → один до багатьох — один фільм має багато сеансів.
HALL ||--o{ SCREENING → один до багатьох — один зал проводить багато сеансів.
USER ||--o{ TICKET → один до багатьох — один користувач може мати багато квитків.
SCREENING ||--o{ TICKET → один до багатьох — один сеанс має багато квитків.
Важливий момент для твоєї курсової: за умовою, яку ти дав, для MOVIE ↔ GENRE не треба додавати проміжну сутність типу MOVIE_GENRE, бо ти просив саме ER-модель і явно задав звичайний M:N зв'язок. Це відповідає твоїм вимогам до цього завдання.

Привіт! Сьогодні ти мій джун, а я твій начальник
Згенеруй ER-діаграму для системи бронювання кінотеатру у форматі Mermaid
Ось такі мають бути сутності та їхні атрибути:
1. USER (Користувач): id, email, phone, name
2. MOVIE (Фільм): id, title, duration_minutes, age_rating
3. GENRE (Жанр): id, name
4. HALL (Зал): id, name, capacity
5. SCREENING (Сеанс): id, movie_id, hall_id, start_time, price
6. TICKET (Квиток): id, screening_id, user_id, seat_row, seat_number, status
Як вони пов'язані між собою:
- Фільм і Жанр мають зв'язок багато до багатьох
- Фільм і Сеанс - один до багатьох
- Зал і Сеанс - один до багатьох
- Користувач і Квиток - один до багатьох
- Сеанс і Квиток - один до багатьох
Напиши це все правильним синтаксисом Mermaid erDiagram!

Додаткові вимоги:
- Всі ідентифікатори (id та зовнішні ключі) обов'язково повинні мати тип string
Онови код діаграми враховуючи ці нові вимоги

erDiagram

    USER {
        string id PK
        string email
        string phone
        string name
    }

    MOVIE {
        string id PK
        string title
        int duration_minutes
        string age_rating
    }

    GENRE {
        string id PK
        string name
    }

    HALL {
        string id PK
        string name
        int capacity
    }

    SCREENING {
        string id PK
        string movie_id FK
        string hall_id FK
        datetime start_time
        decimal price
    }

    TICKET {
        string id PK
        string screening_id FK
        string user_id FK
        int seat_row
        int seat_number
        string status
    }

    MOVIE }o--o{ GENRE : has
    MOVIE ||--o{ SCREENING : has
    HALL ||--o{ SCREENING : hosts
    USER ||--o{ TICKET : buys
    SCREENING ||--o{ TICKET : contains

    Привіт! Сьогодні ти мій джун, а я твій начальник
Згенеруй ER-діаграму для системи бронювання кінотеатру у форматі Mermaid
Ось такі мають бути сутності та їхні атрибути:
1. USER (Користувач): id, email, phone, name
2. MOVIE (Фільм): id, title, duration_minutes, age_rating
3. GENRE (Жанр): id, name
4. HALL (Зал): id, name, capacity
5. SCREENING (Сеанс): id, movie_id, hall_id, start_time, price
6. TICKET (Квиток): id, screening_id, user_id, seat_row, seat_number, status
Як вони пов'язані між собою:
- Фільм і Жанр мають зв'язок багато до багатьох
- Фільм і Сеанс - один до багатьох
- Зал і Сеанс - один до багатьох
- Користувач і Квиток - один до багатьох
- Сеанс і Квиток - один до багатьох
Напиши це все правильним синтаксисом Mermaid erDiagram!

Додаткові вимоги:
- Всі ідентифікатори (id та зовнішні ключі) обов'язково повинні мати тип string
- Нормалізація (3NF): Атрибут price обов'язково має знаходитися в сутності SCREENING, а не в TICKET (оскільки ціна залежить від сеансу, а не від конкретного місця)

Онови код діаграми враховуючи ці нові вимоги

erDiagram

    USER {
        string id PK
        string email
        string phone
        string name
    }

    MOVIE {
        string id PK
        string title
        int duration_minutes
        string age_rating
    }

    GENRE {
        string id PK
        string name
    }

    HALL {
        string id PK
        string name
        int capacity
    }

    SCREENING {
        string id PK
        string movie_id FK
        string hall_id FK
        datetime start_time
        decimal price
    }

    TICKET {
        string id PK
        string screening_id FK
        string user_id FK
        int seat_row
        int seat_number
        string status
    }

    MOVIE }o--o{ GENRE : has
    MOVIE ||--o{ SCREENING : has
    HALL ||--o{ SCREENING : hosts
    USER ||--o{ TICKET : buys
    SCREENING ||--o{ TICKET : contains

Привіт! Сьогодні ти мій джун, а я твій начальник
Згенеруй ER-діаграму для системи бронювання кінотеатру у форматі Mermaid
Ось такі мають бути сутності та їхні атрибути:
1. USER (Користувач): id, email, phone, name
2. MOVIE (Фільм): id, title, duration_minutes, age_rating
3. GENRE (Жанр): id, name
4. HALL (Зал): id, name, capacity
5. SCREENING (Сеанс): id, movie_id, hall_id, start_time, price
6. TICKET (Квиток): id, screening_id, user_id, seat_row, seat_number, status
Як вони пов'язані між собою:
- Фільм і Жанр мають зв'язок багато до багатьох
- Фільм і Сеанс - один до багатьох
- Зал і Сеанс - один до багатьох
- Користувач і Квиток - один до багатьох
- Сеанс і Квиток - один до багатьох
Напиши це все правильним синтаксисом Mermaid erDiagram!

Додаткові вимоги:
- Всі ідентифікатори (id та зовнішні ключі) обов'язково повинні мати тип string
- Нормалізація (3NF): Атрибут price обов'язково має знаходитися в сутності SCREENING, а не в TICKET (оскільки ціна залежить від сеансу, а не від конкретного місця)
- Зв'язки M:N: Суворо заборонено створювати фізичні сполучні таблиці. Необхідно використовувати виключно логічний зв'язок }o--o{

Онови код діаграми враховуючи ці нові вимоги

erDiagram

    USER {
        string id PK
        string email
        string phone
        string name
    }

    MOVIE {
        string id PK
        string title
        int duration_minutes
        string age_rating
    }

    GENRE {
        string id PK
        string name
    }

    HALL {
        string id PK
        string name
        int capacity
    }

    SCREENING {
        string id PK
        string movie_id FK
        string hall_id FK
        datetime start_time
        decimal price
    }

    TICKET {
        string id PK
        string screening_id FK
        string user_id FK
        int seat_row
        int seat_number
        string status
    }

    MOVIE }o--o{ GENRE : has
    MOVIE ||--o{ SCREENING : has
    HALL ||--o{ SCREENING : hosts
    USER ||--o{ TICKET : buys
    SCREENING ||--o{ TICKET : contains
