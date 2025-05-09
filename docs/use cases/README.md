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

# студент

<table> <tr><td><b>ID</b></td><td><code>ViewCourses</code></td></tr> <tr><td><b>Назва:</b></td><td>Переглянути доступні курси</td></tr> <tr><td><b>Учасники:</b></td><td>Студент, система</td></tr> <tr><td><b>Передумови:</b></td><td>Студент авторизований</td></tr> <tr><td><b>Результат:</b></td><td>Список курсів, доступних для перегляду</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - ViewCourses_NoEnrollment_EXC – студент ще не записаний на жоден курс<br/> - ViewCourses_ServerError_EXC – помилка завантаження курсу<br/> </td> </tr> </table>

@startuml
|Студент|
start;
:Заходить на сторінку "Мої курси";

|Система|
:Завантажує список курсів;


|Студент|
:Переглядає список доступних курсів;
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>ViewMaterials</code></td></tr> <tr><td><b>Назва:</b></td><td>Перегляд навчальних матеріалів</td></tr> <tr><td><b>Учасники:</b></td><td>Студент, система</td></tr> <tr><td><b>Передумови:</b></td><td>Студент має доступ до курсу</td></tr> <tr><td><b>Результат:</b></td><td>Завантажені навчальні матеріали</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - ViewMaterials_AccessDenied_EXC – немає доступу до курсу<br/> - ViewMaterials_NoContent_EXC – матеріали не додано<br/> </td> </tr> </table>

@startuml
|Студент|
start;
:Обирає курс;

|Система|
:Перевіряє доступ;


:Завантажує матеріали;


|Студент|
:Переглядає лекції, файли, відео;
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>SubmitAssignment</code></td></tr> <tr><td><b>Назва:</b></td><td>Надіслати завдання</td></tr> <tr><td><b>Учасники:</b></td><td>Студент, система</td></tr> <tr><td><b>Передумови:</b></td><td>Завдання активне, дедлайн не завершився</td></tr> <tr><td><b>Результат:</b></td><td>Відповідь успішно збережено</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - SubmitAssignment_DeadlineMissed_EXC – дедлайн вичерпано<br/> - SubmitAssignment_EmptySubmission_EXC – не завантажено відповідь<br/> - SubmitAssignment_CancelButton_EXC – користувач натиснув “Відміна”<br/> </td> </tr> </table>

@startuml
|Студент|
start;
:Обирає завдання;
:Натискає "Надіслати відповідь";

|Система|
:Перевіряє дедлайн;

|Студент|
:Завантажує файл або вводить текст;
:Натискає "Підтвердити";

|Система|
:Зберігає відповідь;
:Повідомляє про успіх;

|Студент|
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>CheckGrades</code></td></tr> <tr><td><b>Назва:</b></td><td>Перевірити оцінки</td></tr> <tr><td><b>Учасники:</b></td><td>Студент, система</td></tr> <tr><td><b>Передумови:</b></td><td>Оцінки вже виставлені</td></tr> <tr><td><b>Результат:</b></td><td>Оцінки відображено</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - CheckGrades_NotGradedYet_EXC – оцінки ще не виставлено<br/> </td> </tr> </table>

@startuml
|Студент|
start;
:Переходить у вкладку "Оцінки";

|Система|
:Завантажує оцінки;


|Студент|
:Переглядає оцінки;
stop;
@enduml


<table> <tr><td><b>ID</b></td><td><code>MessageTeacher</code></td></tr> <tr><td><b>Назва:</b></td><td>Написати повідомлення викладачу</td></tr> <tr><td><b>Учасники:</b></td><td>Студент, система</td></tr> <tr><td><b>Передумови:</b></td><td>Студент прив'язаний до курсу викладача</td></tr> <tr><td><b>Результат:</b></td><td>Повідомлення надіслано викладачу</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - MessageTeacher_TextTooLong_EXC – повідомлення перевищує ліміт символів<br/> - MessageTeacher_CancelButton_EXC – користувач натиснув "Відміна"<br/> </td> </tr> </table>

@startuml
|Студент|
start;
:Відкриває курс і натискає "Написати викладачу";

|Система|
:Відкриває форму повідомлення;

|Студент|
:Вводить текст повідомлення;
:Натискає "Надіслати";

|Система|
:Відправляє повідомлення викладачу;

|Студент|
stop;
@enduml

# викладач

<table> <tr><td><b>ID</b></td><td><code>CreateCourse</code></td></tr> <tr><td><b>Назва:</b></td><td>Створити курс</td></tr> <tr><td><b>Учасники:</b></td><td>Викладач, система</td></tr> <tr><td><b>Передумови:</b></td><td>Викладач авторизований</td></tr> <tr><td><b>Результат:</b></td><td>Курс створено в системі</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - CreateCourse_InvalidInfo_EXC — введено невалідні дані курсу<br/> - CreateCourse_CancelButton_EXC — натиснуто “Відміна”<br/> </td> </tr> </table>

@startuml
|Викладач|
start;
:Натискає "Створити курс";

|Система|
:Відкриває форму курсу;

|Викладач|
:Вводить назву, опис, тривалість;
:Натискає "Зберегти";

|Система|
:Перевіряє дані;
:Створює курс;

|Викладач|
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>AddMaterial</code></td></tr> <tr><td><b>Назва:</b></td><td>Додати матеріал до курсу</td></tr> <tr><td><b>Учасники:</b></td><td>Викладач, система</td></tr> <tr><td><b>Передумови:</b></td><td>Курс створений, викладач його автор</td></tr> <tr><td><b>Результат:</b></td><td>Матеріали додані до курсу</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - AddMaterial_InvalidFile_EXC — завантажено файл невідповідного типу<br/> - AddMaterial_CancelButton_EXC — викладач натиснув “Відміна”<br/> </td> </tr> </table>

@startuml
|Викладач|
start;
:Відкриває курс;
:Натискає "Додати матеріал";

|Система|
:Виводить форму додавання файлів;

|Викладач|
:Завантажує файл;
:Натискає "Додати";


|Система|
:Перевіряє тип файлу;

:Зберігає матеріал;

|Викладач|
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>CreateAssignment</code></td></tr> <tr><td><b>Назва:</b></td><td>Створити завдання</td></tr> <tr><td><b>Учасники:</b></td><td>Викладач, система</td></tr> <tr><td><b>Передумови:</b></td><td>Курс активний, викладач його автор</td></tr> <tr><td><b>Результат:</b></td><td>Завдання створено</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - CreateAssignment_MissingFields_EXC — не заповнені обов’язкові поля<br/> - CreateAssignment_CancelButton_EXC — натиснуто “Відміна”<br/> </td> </tr> </table>

@startuml
|Викладач|
start;
:Обирає курс;
:Натискає "Створити завдання";

|Система|
:Відкриває форму завдання;

|Викладач|
:Вводить опис, дедлайн;
:Натискає "Опублікувати";

|Система|
:Перевіряє дані;
:Створює завдання;

|Викладач|
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>ReviewSubmissions</code></td></tr> <tr><td><b>Назва:</b></td><td>Перегляд відповідей студентів</td></tr> <tr><td><b>Учасники:</b></td><td>Викладач, система</td></tr> <tr><td><b>Передумови:</b></td><td>Є опубліковане завдання з відповідями</td></tr> <tr><td><b>Результат:</b></td><td>Викладач бачить відповіді студентів</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - ReviewSubmissions_NoAnswersYet_EXC — ще немає відповідей<br/> </td> </tr> </table>

@startuml
|Викладач|
start;
:Відкриває курс;
:Обирає завдання;

|Система|
:Завантажує відповіді;


|Викладач|
:Читає відповіді студентів;
stop;
@enduml


<table> <tr><td><b>ID</b></td><td><code>GradeAssignment</code></td></tr> <tr><td><b>Назва:</b></td><td>Виставити оцінку</td></tr> <tr><td><b>Учасники:</b></td><td>Викладач, система</td></tr> <tr><td><b>Передумови:</b></td><td>Є надіслані відповіді студентів</td></tr> <tr><td><b>Результат:</b></td><td>Оцінка збережена</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - GradeAssignment_InvalidMark_EXC — некоректний формат оцінки<br/> - GradeAssignment_CancelButton_EXC — викладач натиснув “Відміна”<br/> </td> </tr> </table>


@startuml
|Викладач|
start;
:Відкриває відповідь студента;

|Система|
:Виводить форму виставлення оцінки;

|Викладач|
:Вказує оцінку;
:Натискає "Підтвердити";

|Система|
:Зберігає оцінку;

|Викладач|
stop;
@enduml

# Адміністратор

<table> <tr><td><b>ID</b></td><td><code>ManageUsers</code></td></tr> <tr><td><b>Назва:</b></td><td>Керувати користувачами</td></tr> <tr><td><b>Учасники:</b></td><td>Адміністратор, система</td></tr> <tr><td><b>Передумови:</b></td><td>Адміністратор авторизований</td></tr> <tr><td><b>Результат:</b></td><td>Користувача додано, змінено або видалено</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - ManageUsers_EmptyFields_EXC — не заповнені обов'язкові поля<br/> - ManageUsers_AlreadyExists_EXC — користувач вже існує<br/> - ManageUsers_DeleteSelf_EXC — спроба видалити себе<br/> </td> </tr> </table>

@startuml
|Адміністратор|
start;
:Заходить у розділ "Користувачі";

|Система|
:Відображає список користувачів;

|Адміністратор|
:Обирає "Додати/Редагувати/Видалити";
:Вводить дані користувача;

:Натискає "Зберегти";


|Система|
:Перевіряє унікальність;

:Оновлює базу користувачів;

|Адміністратор|
stop;
@enduml

<table> <tr><td><b>ID</b></td><td><code>EditRoles</code></td></tr> <tr><td><b>Назва:</b></td><td>Змінити роль користувача</td></tr> <tr><td><b>Учасники:</b></td><td>Адміністратор, система</td></tr> <tr><td><b>Передумови:</b></td><td>Користувач існує в системі</td></tr> <tr><td><b>Результат:</b></td><td>Оновлена роль користувача</td></tr> <tr><td><b>Виключні ситуації:</b></td> <td> - EditRoles_InvalidRole_EXC — вказано невалідну роль<br/> - EditRoles_CancelButton_EXC — натиснуто “Відміна”<br/> </td> </tr> </table>

@startuml
|Адміністратор|
start;
:Обирає користувача;
:Натискає "Змінити роль";

|Система|
:Виводить перелік доступних ролей;

|Адміністратор|
:Вибирає нову роль;
:Натискає "Підтвердити";

|Система|
:Перевіряє роль;
:Оновлює роль у базі;

|Адміністратор|
stop;
@enduml