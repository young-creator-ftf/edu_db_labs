## Структура таблиці `user`

```sql
CREATE TABLE IF NOT EXISTS user (
    id BINARY(16) PRIMARY KEY,
    email VARCHAR(100) NOT NULL,
    username VARCHAR(45) NOT NULL,
    password VARCHAR(255) NOT NULL,
    role_name VARCHAR(50)
);
