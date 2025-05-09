# Проєктування бази даних

## Модель бізнес об'єктів

<center style="
    border-radius:4px;
    border: 1px solid #cfd7e6;
    box-shadow: 0 1px 3px 0 rgba(89,105,129,.05), 0 1px 1px 0 rgba(0,0,0,.025);
    padding: 1em;"
>

@startuml
skinparam linetype ortho

' -------------------
' CRUD-операції
object Create
object Read
object Update
object Delete

' -------------------
' Типи операцій та інстанси
entity OperationType {
  name
}
Create    ..> OperationType : instanceOf
Read      ..> OperationType : instanceOf
Update    ..> OperationType : instanceOf
Delete    ..> OperationType : instanceOf

entity RequestType {
  name
}
RequestType "0..*" -- "0..1" OperationType : uses

entity Grant
Grant "0..*" -- "1..1" RequestType : grants

entity Access

entity Role {
  name
}
object StudentRole
object TeacherRole
object AdminRole
StudentRole ..> Role : instanceOf
TeacherRole ..> Role : instanceOf
AdminRole   ..> Role : instanceOf

Role "1..1" -- "0..*" Grant  : hasGrant
Role "1..1" -- "0..*" Access : hasAccess

entity User {
  username
  password
  email
}
User "1..1" -- "0..*" Access : ownsAccess

' -------------------
' Бізнес-об’єкти навчальної системи
entity Course {
  title
  description
}
entity Enrollment {
  enrollDate
}
entity Module {
  title
}
entity Lesson {
  title
}
entity Assignment {
  title
  description
  dueDate
}
entity Submission {
  submitDate
  content
}
entity Grade {
  score
  feedback
}

' -------------------
' Статуси подань
entity SubmissionStatus {
  name
}
object Draft
object Submitted
object Graded
Draft     ..> SubmissionStatus : instanceOf
Submitted ..> SubmissionStatus : instanceOf
Graded    ..> SubmissionStatus : instanceOf
Submission "1..1" -- "0..*" SubmissionStatus : history

' -------------------
' Зв’язки між сутностями
User       "1..*" -- "0..*" Enrollment   : enrolls
Course     "1..1" -- "0..*" Enrollment   : hasEnrollment
Course     "1..1" -- "0..*" Module       : contains
Module     "1..1" -- "0..*" Lesson       : contains
Course     "1..1" -- "0..*" Assignment   : contains
Assignment "1..1" -- "0..*" Submission   : receives
Submission "1..1" -- "1..1" Assignment   : forAssignment
Submission "1..1" -- "1..1" User         : byUser
Submission "1..1" -- "0..1" Grade        : gradedAs

' -------------------
' Посередник доступу
entity AccessMediator
AccessMediator "1..*" -- "1..1" Access     : mediates
AccessMediator "0..*" -- "0..*" Course     : forCourse
AccessMediator "0..*" -- "0..*" Module     : forModule
AccessMediator "0..*" -- "0..*" Lesson     : forLesson
AccessMediator "0..*" -- "0..*" Assignment : forAssignment
AccessMediator "0..*" -- "0..*" Submission : forSubmission

@enduml

</center>

## Модель Сутність-Зв'язок

<center style="
    border-radius:4px;
    border: 1px solid #cfd7e6;
    box-shadow: 0 1px 3px 0 rgba(89,105,129,.05), 0 1px 1px 0 rgba(0,0,0,.025);
    padding: 1em;"
>

@startuml
skinparam linetype ortho

' -------------------
' Керування обліковими записами
namespace AccountManagement {
    entity User <<ENTITY>> {
        username
        email
        password
        fullName
    }
    
    entity StudentProfile <<ENTITY>> {
        studentId
        enrollmentDate
    }
    
    entity TeacherProfile <<ENTITY>> {
        teacherId
        hireDate
        department
    }
}

User "1" -- "0..1" StudentProfile : hasProfile
User "1" -- "0..1" TeacherProfile : hasProfile

' -------------------
' Ієрархія навчального простору
namespace LearningHierarchy {
    entity School <<ENTITY>> {
        name
        address
    }
    entity Course <<ENTITY>> {
        code
        title
        description
    }
    entity Module <<ENTITY>> {
        title
        sequence
    }
    entity Lesson <<ENTITY>> {
        title
        contentLink
    }
}

School "1" -- "0..*" Course    : offers
Course "1" -- "0..*" Module    : contains
Module "1" -- "0..*" Lesson    : contains

' -------------------
' Управління завданнями та оцінками
namespace AssignmentManagement {
    entity Assignment <<ENTITY>> {
        title
        description
        dueDate
    }
    entity Submission <<ENTITY>> {
        submitDate
        fileLink
    }
    entity Grade <<ENTITY>> {
        score
        feedback
    }
    
    entity SubmissionStatus <<ENTITY>> {
        name
    }
    object Draft
    object Submitted
    object Graded
    
    Draft     ..> SubmissionStatus : instanceOf
    Submitted ..> SubmissionStatus : instanceOf
    Graded    ..> SubmissionStatus : instanceOf
    
    Assignment "1" -- "0..*" Submission : receives
    Submission "1" -- "0..1" Grade       : gradedAs
    Submission "1" -- "0..*" SubmissionStatus : history
}

' Взаємозв’язки із профілями
StudentProfile "1" -- "0..*" Assignment : assigned
TeacherProfile "1" -- "0..*" Assignment : creates

' -------------------
' Політика доступу (RBAC/ACL)
namespace AccessPolicy {
    entity Role <<ENTITY>> {
        name
    }
    object StudentRole
    object TeacherRole
    object AdminRole
    StudentRole      ..> Role : instanceOf
    TeacherRole      ..> Role : instanceOf
    AdminRole        ..> Role : instanceOf

    entity OperationType <<ENTITY>> {
        name
    }
    object CreateOp
    object ReadOp
    object UpdateOp
    object DeleteOp
    CreateOp ..> OperationType : instanceOf
    ReadOp   ..> OperationType : instanceOf
    UpdateOp ..> OperationType : instanceOf
    DeleteOp ..> OperationType : instanceOf

    entity RequestType <<ENTITY>> {
        name
    }
    RequestType "0..*" -- "0..1" OperationType : uses

    entity Grant <<ENTITY>>
    Grant "0..*" -- "1" RequestType : grants

    entity Access <<ENTITY>>
    Role "1" -- "0..*" Grant   : hasGrant
    Role "1" -- "0..*" Access  : hasAccess
    User "1" -- "0..*" Access  : ownsAccess

    entity AccessMediator <<ENTITY>>
    AccessMediator "1..*" -- "1" Access : mediates
    AccessMediator "0..1" -- "0..*" School      : forSchool
    AccessMediator "0..1" -- "0..*" Course      : forCourse
    AccessMediator "0..1" -- "0..*" Module      : forModule
    AccessMediator "0..1" -- "0..*" Lesson      : forLesson
    AccessMediator "0..1" -- "0..*" Assignment  : forAssignment
    AccessMediator "0..1" -- "0..*" Submission  : forSubmission
}

@enduml


</center>

## Опис ER-моделі

# Опис ER-моделі

## User (Користувач)

> Представляє обліковий запис користувача (студента, викладача або адміністратора).

- username : VARCHAR — унікальне ім’я користувача  
- email : VARCHAR — адреса електронної пошти  
- password : VARCHAR — захищений пароль  
- role_id : BINARY — зовнішній ключ до ролі користувача

---

## Role (Роль)

> Описує роль користувача в системі.

- name : VARCHAR — назва ролі (Student, Teacher, Admin)

---

## StudyGroup (Група)

> Навчальна група, до якої належать студенти.

- name : VARCHAR — назва групи  
- year : INT — рік навчання

---

## Course (Курс)

> Навчальний курс, який ведеться для певної групи.

- name : VARCHAR — назва курсу  
- description : TEXT — опис курсу  
- teacher_id : BINARY — викладач (звʼязок з користувачем)  
- group_id : BINARY — група, що вивчає курс

---

## Assignment (Завдання)

> Завдання, яке задається в межах курсу.

- title : VARCHAR — назва завдання  
- description : TEXT — умова завдання  
- deadline : DATETIME — термін виконання  
- course_id : BINARY — курс, до якого належить завдання

---

## Submission (Робота)

> Відповідь студента на конкретне завдання.

- assignment_id : BINARY — посилання на завдання  
- student_id : BINARY — студент, який подав роботу  
- file_url : VARCHAR — посилання на файл  
- submission_time : DATETIME — дата і час здачі

---

## Grade (Оцінка)

> Оцінка, що виставлена за подану роботу.

- submission_id : BINARY — до якої відповіді стосується  
- grade_value : DECIMAL — числове значення оцінки  
- graded_by : BINARY — викладач, що перевірив

---

## Schedule (Розклад)

> Розклад занять для певної групи.

- group_id : BINARY — до якої групи належить  
- day : ENUM — день тижня  
- start_time : TIME — час початку  
- end_time : TIME — час завершення

---

## Lesson (Заняття)

> Конкретна пара або лекція в розкладі.

- topic : VARCHAR — тема заняття  
- room_number : VARCHAR — номер аудиторії  
- course_id : BINARY — курс, до якого належить заняття  
- schedule_id : BINARY — розклад, у якому відображено заняття

## Реляційна схема

![Реляційна схема](https://imgur.com/a/5kP4Rye)