# Реалізація інформаційного та програмного забезпечення

## SQL-скрипт для створення та початкового наповнення бази даних

```sql
-- Створення бази даних
CREATE DATABASE IF NOT EXISTS edu_management;
USE edu_management;

-- Таблиця ролей
CREATE TABLE role (
    id BINARY(16) PRIMARY KEY,
    name VARCHAR(50) NOT NULL
);

-- Таблиця користувачів
CREATE TABLE user (
    id BINARY(16) PRIMARY KEY,
    email VARCHAR(100) NOT NULL UNIQUE,
    username VARCHAR(45) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role_id BINARY(16),
    FOREIGN KEY (role_id) REFERENCES role(id)
);

-- Таблиця академічних груп
CREATE TABLE study_group (
    id BINARY(16) PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    year INT NOT NULL
);

-- Таблиця курсів
CREATE TABLE course (
    id BINARY(16) PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    description TEXT,
    teacher_id BINARY(16),
    group_id BINARY(16),
    FOREIGN KEY (teacher_id) REFERENCES user(id),
    FOREIGN KEY (group_id) REFERENCES study_group(id)
);

-- Таблиця розкладу
CREATE TABLE schedule (
    id BINARY(16) PRIMARY KEY,
    group_id BINARY(16),
    day ENUM('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday') NOT NULL,
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    FOREIGN KEY (group_id) REFERENCES study_group(id)
);

-- Таблиця занять
CREATE TABLE lesson (
    id BINARY(16) PRIMARY KEY,
    topic VARCHAR(100),
    room_number VARCHAR(10),
    course_id BINARY(16),
    schedule_id BINARY(16),
    FOREIGN KEY (course_id) REFERENCES course(id),
    FOREIGN KEY (schedule_id) REFERENCES schedule(id)
);

-- Таблиця завдань
CREATE TABLE assignment (
    id BINARY(16) PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    description TEXT,
    deadline DATETIME,
    course_id BINARY(16),
    FOREIGN KEY (course_id) REFERENCES course(id)
);

-- Таблиця надсилань
CREATE TABLE submission (
    id BINARY(16) PRIMARY KEY,
    assignment_id BINARY(16),
    student_id BINARY(16),
    file_url VARCHAR(255),
    submission_time DATETIME,
    FOREIGN KEY (assignment_id) REFERENCES assignment(id),
    FOREIGN KEY (student_id) REFERENCES user(id)
);

-- Таблиця оцінок
CREATE TABLE grade (
    id BINARY(16) PRIMARY KEY,
    submission_id BINARY(16),
    grade_value DECIMAL(4,2),
    graded_by BINARY(16),
    FOREIGN KEY (submission_id) REFERENCES submission(id),
    FOREIGN KEY (graded_by) REFERENCES user(id)
);

-- Початкове наповнення

-- Ролі
INSERT INTO role (id, name) VALUES
(UUID_TO_BIN(UUID()), 'student'),
(UUID_TO_BIN(UUID()), 'teacher'),
(UUID_TO_BIN(UUID()), 'admin');

-- Група
INSERT INTO study_group (id, name, year) VALUES
(UUID_TO_BIN(UUID()), 'IPZ-21', 2021);

-- Користувачі
INSERT INTO user (id, email, username, password, role_id)
VALUES 
(UUID_TO_BIN(UUID()), 'student1@example.com', 'student1', 'hashed_pass_1', 
 (SELECT id FROM role WHERE name = 'student')),
(UUID_TO_BIN(UUID()), 'teacher1@example.com', 'teacher1', 'hashed_pass_2', 
 (SELECT id FROM role WHERE name = 'teacher')),
(UUID_TO_BIN(UUID()), 'admin1@example.com', 'admin1', 'hashed_pass_3', 
 (SELECT id FROM role WHERE name = 'admin'));

-- Курси
INSERT INTO course (id, name, description, teacher_id, group_id)
VALUES
(UUID_TO_BIN(UUID()), 'Програмування', 'Основи програмування мовою Java', 
 (SELECT id FROM user WHERE username = 'teacher1'),
 (SELECT id FROM study_group WHERE name = 'IPZ-21'));

-- Розклад
INSERT INTO schedule (id, group_id, day, start_time, end_time)
VALUES
(UUID_TO_BIN(UUID()), 
 (SELECT id FROM study_group WHERE name = 'IPZ-21'),
 'Monday', '09:00:00', '10:20:00');

-- Заняття
INSERT INTO lesson (id, topic, room_number, course_id, schedule_id)
VALUES
(UUID_TO_BIN(UUID()), 'Вступ до Java', 'A101',
 (SELECT id FROM course WHERE name = 'Програмування'),
 (SELECT id FROM schedule WHERE day = 'Monday'));

-- Завдання
INSERT INTO assignment (id, title, description, deadline, course_id)
VALUES
(UUID_TO_BIN(UUID()), 'Лабораторна робота 1', 'Ознайомлення з синтаксисом Java',
 '2025-06-01 23:59:59', 
 (SELECT id FROM course WHERE name = 'Програмування'));
```

## RESTfull сервіс для управління даними