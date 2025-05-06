# Проєктування бази даних

## Модель бізнес об'єктів

<center style="
    border-radius:4px;
    border: 1px solid #cfd7e6;
    box-shadow: 0 1px 3px 0 rgba(89,105,129,.05), 0 1px 1px 0 rgba(0,0,0,.025);
    padding: 1em;"
>

@startuml
' Щоб лінії були «прямими»
skinparam linetype ortho

' -------------------
' Об’єкти операцій
object Create
object Read
object Update
object Delete

' -------------------
' Сутності
entity Access

entity Grant

entity OperationType {
  name
}

entity RequestType {
  name
}

entity Role {
  name
}

entity User {
  username
  password
  email
  avatar
}

entity Workspace {
  name
  description
}

entity Project {
  name
  description
}

entity Board {
  name
  description
}

entity Task {
  title
  description
  deadline
  photos
}

entity AccessMediator

entity Status {
  name
}

' -------------------
' Статуси як об’єкти–екземпляри
object BackLog
object ToDo
object InProgress
object InReview
object BugFound
object Done

' -------------------
' Відношення «instanceOf»
Create    ..> OperationType : instanceOf
Read      ..> OperationType : instanceOf
Update    ..> OperationType : instanceOf
Delete    ..> OperationType : instanceOf

ProjectUser ..> Role : instanceOf
ProjectManager ..> Role : instanceOf
WorkspaceManager ..> Role : instanceOf
SystemAdministrator ..> Role : instanceOf

BackLog    ..> Status : instanceOf
ToDo       ..> Status : instanceOf
InProgress ..> Status : instanceOf
InReview   ..> Status : instanceOf
BugFound   ..> Status : instanceOf
Done       ..> Status : instanceOf

' -------------------
' Кардинальності й асоціації
RequestType  "0..*" -- "0..1" OperationType : uses
Grant        "0..*" -- "1..1" RequestType : grants
Role         "1..1" -- "0..*" Grant       : hasGrant
Role         "1..1" -- "0..*" Access      : hasAccess
User         "1..1" -- "0..*" Access      : ownsAccess
Workspace    "1..1" -- "0..*" Project     : contains
Project      "1..1" -- "0..*" Board       : contains
Board        "1..1" -- "0..*" Task        : contains
Task         "1..1" -- "0..*" Status      : history

' Посередник доступу (зв’язує Access з усіма бізнес-об’єктами)
AccessMediator "1..*" -- "1..1" Access    : mediates
AccessMediator "0..1" -- "0..*" Task      : forTask
AccessMediator "0..1" -- "0..*" Board     : forBoard
AccessMediator "0..1" -- "0..*" Project   : forProject
AccessMediator "0..1" -- "0..*" Workspace : forWorkspace

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

namespace AccountManagement {

    entity User <<ENTITY>> {
        email
        password
        name
        avatar
    }

}

namespace SpaceHierarchy {

    entity Workspace <<ENTITY>>{
        name
        description
        manager
    }
    
    entity Project <<ENTITY>> {
        name
        description
        manager
    }
    
    entity Board <<ENTITY>> {
        name    
        description
    }
    
    entity Task <<ENTITY>> {
        title
        description
        photos
        deadline
    }

}

Workspace "1.1" --- "0.*" Project

Project "1.1" --- "0.*" Board

Board "1.1" --- "0.*" Task

namespace TaskManagement {

    entity Status <<ENTITY>> #ffff33{
        name
    }
    
    object BackLog #ffffff
    object ToDo #ffffff
    object inProgress #ffffff
    object InReview #ffffff
    object BugFound #ffffff
    object Done #ffffff

}

BackLog ..> Status : instanceOf
ToDo ..> Status : instanceOf
inProgress ..> Status : instanceOf
InReview ..> Status : instanceOf
BugFound ..> Status : instanceOf
Done ..> Status : instanceOf
namespace AccessPolicy {

    entity Role <<ENTITY>> #ffff00 {
        name: TEXT
    }
    
    object ProjectUser #ffffff 
    object ProjectManager #ffffff 
    object WorkspaceManager #ffffff 
    object SystemAdministrator #ffffff

    entity AccessMediator
    entity Access
    entity Grant
    entity OperationType <<ENTITY>> #ffff33{
        name
    }
    entity RequestType <<ENTITY>> {
        name
    }
    
    object create #ffffff
    object read #ffffff
    object update #ffffff
    object delete #ffffff
}
 
ProjectUser ..> Role : instanceOf 
ProjectManager ..> Role : instanceOf 
WorkspaceManager ..> Role : instanceOf 
SystemAdministrator ..> Role : instanceOf

RequestType "0.*"---"0.1" OperationType

create .u.> OperationType : instanceOf
read .u.> OperationType : instanceOf
update .u.> OperationType : instanceOf
delete .u.> OperationType : instanceOf

Grant "0.*"---"1.1" RequestType

Role "0.1" --- "0.*" Grant

Role "0.1" -r- "0.*" Access

AccessMediator "0.*" -u- "1.1" Access

AccessMediator "0.*" -r- "0.1" Task

AccessMediator "0.*" -r- "0.1" Board

AccessMediator "0.*" -r- "0.1" Project

AccessMediator "0.*" -l- "0.1" Workspace

User "1.1" --- "0.*" Access

Task "1.1" --- "0.*" Status

@enduml

</center>

## Опис ER-моделі

### User (Користувач)

:::info
Представляє обліковий запис користувача в системі.
:::

- `username`: `VARCHAR` — унікальне ім’я користувача  
- `email`: `VARCHAR` — адреса електронної пошти  
- `password`: `VARCHAR` — захищений пароль  
- `avatar`: `VARCHAR` — посилання на зображення профілю

---

### Workspace (Воркспейс)

:::info
Простір для організації роботи над проєктами.
:::

- `name`: `VARCHAR` — назва воркспейсу  
- `description`: `VARCHAR` — короткий опис  
- `manager`: `BINARY` — менеджер (один, обов’язковий)

---

### Project (Проєкт)

:::info
Робоча одиниця команди в межах воркспейсу.
:::

- `name`: `VARCHAR` — назва проєкту  
- `description`: `VARCHAR` — короткий опис  
- `manager`: `BINARY` — менеджер (один, обов’язковий)

---

### Task (Завдання)

:::info
Окрема одиниця роботи, розміщена на дошці.
:::

- `title`: `VARCHAR` — назва  
- `description`: `VARCHAR` — опис завдання  
- `photos`: `VARCHAR` — фото (за потреби)  
- `deadline`: `DATETIME` — кінцевий термін

> Кожне завдання належить лише одній дошці.

---

### Board (Дошка)

:::info
Організація завдань у межах проєкту.
:::

- `name`: `VARCHAR` — назва дошки  
- `description`: `VARCHAR` — опис призначення

---

### Status (Статус)

:::info
Визначає поточний стан завдання.
:::

- `name`: `VARCHAR` — назва статусу

**Можливі статуси:**

- `BackLog` — завдання до виконання  
- `ToDo` — заплановані завдання  
- `inProgress` — у процесі виконання  
- `InReview` — на перевірці  
- `BugFound` — виявлено помилку  
- `Done` — виконано

---


### Access

:::info
Асоціація, що вказує на ролі користувача.
:::

> Один користувач може мати кілька ролей.  
> Кожна роль може бути призначена багатьом користувачам.

---

### Role (Роль) 
 
:::info
Визначає права доступу користувача.
:::

- `name`: `ENUM` — назва ролі

> Може не мати прав або мати скільки завгодно дозволів і доступів.

---

### Grant

:::info
Асоціація між роллю та дозволеними діями.
:::

> Кожна роль може мати багато прав на об'єкти системи.

---

### RequestType

:::info
Визначає об’єкт і тип доступу, який до нього надається.
:::

> Зв'язується з OperationType та використовується у Grant.

---

### OperationType

:::info
Довідник допустимих дій над сутностями системи.
:::

- `name`: `VARCHAR` — назва дії (`create`, `read`, `update`, `delete`)

## Реляційна схема

![Реляційна схема](https://i.imgur.com/9nDMdXs.png)