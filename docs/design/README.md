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



</center>

## Опис ER-моделі



## Реляційна схема

![Реляційна схема](https://imgur.com/a/5kP4Rye)