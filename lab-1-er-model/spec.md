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
