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
