# Модель прецедентів

## Загальна схема
@startuml
' === Загальні налаштування ===
skinparam backgroundColor #FDFDFD
skinparam shadowing false
skinparam RoundCorner 15
skinparam actor {
  BackgroundColor #D0E6FA
  BorderColor #3794FF
  FontColor #03396C
}
skinparam usecase {
  BackgroundColor #F8E0E6
  BorderColor #D53E53
  FontColor #731F1F
}

' === Актори ===
actor "Студент" as Student #AED6F1
actor "Викладач" as Teacher #ABEBC6
actor "Адміністратор" as Admin #F5B7B1

Teacher --u-|> Student
Admin -u-|> Teacher

' === Варіанти використання ===
usecase "<b>ПереглядМатеріалів</b>\nПерегляд навчальних матеріалів" as ViewMaterials #D6EAF8
usecase "<b>ВиконанняЗавдань</b>\nВиконання домашніх завдань" as DoAssignments #D6EAF8
usecase "<b>ПеревіркаОцінок</b>\nПеревірка оцінок" as CheckGrades #D6EAF8
usecase "<b>НаписатиПовідомлення</b>\nЗв'язок з викладачем" as MessageTeacher #F9E79F

usecase "<b>СтворитиКурс</b>\nСтворення курсу" as CreateCourse #A9DFBF
usecase "<b>ДодатиЗавдання</b>\nДодавання завдань" as AddAssignment #A9DFBF
usecase "<b>Оцінювання</b>\nОцінювання студентів" as GradeStudents #A9DFBF
usecase "<b>КеруванняГрупами</b>\nКерування академічними групами" as ManageGroups #A9DFBF

usecase "<b>КеруванняКористувачами</b>\nДодавання/видалення користувачів" as ManageUsers #F5CBA7
usecase "<b>НалаштуванняСистеми</b>\nКонфігурація системи" as ConfigureSystem #F5CBA7

' === Зв’язки ===
Student -u-> ViewMaterials
Student -u-> DoAssignments
Student -l-> CheckGrades
Student -r-> MessageTeacher

Teacher -r-> CreateCourse
Teacher -l-> AddAssignment
Teacher -u-> GradeStudents
Teacher -d-> ManageGroups

Admin -r-> ManageUsers
Admin -l-> ConfigureSystem

@enduml

# Student

@startuml
actor "Студент" as Student

usecase "Перегляд\nнавчальних матеріалів" as ViewMaterials
usecase "Виконання\nдомашніх завдань" as DoAssignments
usecase "Перевірка\nоцінок" as CheckGrades
usecase "Зв'язок з\nвикладачем" as MessageTeacher

Student --> ViewMaterials
Student --> DoAssignments
Student --> CheckGrades
Student --> MessageTeacher
@enduml


# Teacher


@startuml
actor "Викладач" as Teacher

usecase "Створення курсу" as CreateCourse
usecase "Додавання завдань" as AddAssignment
usecase "Оцінювання студентів" as GradeStudents
usecase "Керування групами" as ManageGroups

' Успадковані від Студента
usecase "Перегляд\nнавчальних матеріалів" as ViewMaterials
usecase "Перевірка\nоцінок" as CheckGrades
usecase "Зв'язок з\nіншими викладачами" as MessageTeacher

Teacher --> CreateCourse
Teacher --> AddAssignment
Teacher --> GradeStudents
Teacher --> ManageGroups

Teacher --> ViewMaterials
Teacher --> CheckGrades
Teacher --> MessageTeacher
@enduml


# Administrator

@startuml
actor "Адміністратор" as Admin

usecase "Керування користувачами" as ManageUsers
usecase "Налаштування системи" as ConfigureSystem

' Успадковані від Викладача
usecase "Створення курсу" as CreateCourse
usecase "Додавання завдань" as AddAssignment
usecase "Оцінювання студентів" as GradeStudents
usecase "Керування групами" as ManageGroups

' Успадковані від Студента
usecase "Перегляд матеріалів" as ViewMaterials
usecase "Перевірка оцінок" as CheckGrades
usecase "Повідомлення викладачу" as MessageTeacher

Admin --> ManageUsers
Admin --> ConfigureSystem

Admin --> CreateCourse
Admin --> AddAssignment
Admin --> GradeStudents
Admin --> ManageGroups

Admin --> ViewMaterials
Admin --> CheckGrades
Admin --> MessageTeacher
@enduml
## Сценарії Використання