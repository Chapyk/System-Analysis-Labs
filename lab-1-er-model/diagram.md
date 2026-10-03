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
