# Лабораторная работа №4 Проектирование REST API

## *Проектные решения по API сервиса ResponsibleService*
#### Необходимо учесть, что все методы, разработанные в данной лабораторной работе выполняются авторизованным пользователем. За аутентификацию и авторизацию отвечаечает отдельный сервис, через который будут проходить запросы к API, соответственно, с помощью него могут быть также возвращены ошибки авторизации или аутентификации (401, 403)

### *Контроллер назначений ответственных*
#### *GET/responsibility/{sample_id:int}* - запрос необходим для получения всех назначений ответственных определенной пробы по ID
#### *GET/responsibility/assignable-responsibles* - запрос необходим для получения списка возможных ответственных лиц 
#### *DELETE/responsibility/{id:int}* - запрос необходим для удаления назначения ответственного по ID
#### *POST/responsibility* - запрос необходим для добавления нового назначения ответственного
#### *PUT/responsibility/{id:int}* - запрос необходим для обновления статуса текущего назначения ответственного по ID

### *Контроллер пользователей*
#### *GET /users/repo/{repoId:int}* - запрос для получения всех пользователей, привязанных к указанному RepoId
#### *GET /users/{id:int}* - запрос необходим для получения информации о пользователе по его ID
#### *DELETE/users/{id:int}* - запрос необходим для удаления пользователя по ID
#### *POST /users* - запрос необходим для добавления нового пользователя
#### *PUT /users/{id:int}* - запрос необходим для обновления информации о пользователе по его ID

### *Контроллер проб*
#### *GET/samples/{currentResponsibleId:int}* - запрос необходим для получения всех проб, где текущим ответственным является пользователь с указанным ID


## *Документация по API*

### *Контроллер назначений ответственных*

#### *GET/responsibility/{sample_id:int}*

**Описание:**  
Получает список всех назначений ответственных для конкретной пробы по её ID.

**Входные параметры:**  
Query: 
- `sample_id` (int) - ID пробы, для которой нужно получить назначения.

**Пример входных параметров:**  
URL: `/responsibility?sampleId=1`

**Коды статусов ответа:**
- `200 OK` - Успешно возвращен список назначений.
- `400 Unauthorized` - Неверные параметры запроса.

**Пример ответа:**
```json
[
  {
    "id": 1,
    "sample_id": 123,
    "responsible_id": 456,
    "assigned_at": "2025-01-25T12:00:00Z",
    "status": "assigned"
  },
  {
    "id": 2,
    "sample_id": 123,
    "responsible_id": 789,
    "assigned_at": "2025-01-20T10:00:00Z",
    "status": "completed"
  }
]
```

#### *GET/responsibility/assignable-responsibles*

**Описание:**  
Получает список возможных ответственных лиц для конкретного репозитория по его ID.

**Входные параметры:**  
Query:  
- `repositoryId` (int) - ID репозитория, для которого нужно получить возможных ответственных.

**Пример входных параметров:**  
URL: `/responsibility/assignable-responsibles?repositoryId=1`

**Коды статусов ответа:**
- `200 OK` - Успешно возвращен список возможных ответственных.
- `400 BadRequest` - Неверные параметры запроса.

**Пример ответа:**
```json
[
    {
        "id": 1,
        "repoId": 1,
        "username": "admin",
        "name": "John",
        "lastName": "Doe",
        "passwordHash": "hashed_password_admin",
        "roles": [
            {
                "id": 1,
                "name": "Admin",
                "privileges": [
                    {
                        "id": 4,
                        "name": "CanWriteSamples"
                    },
                    {
                        "id": 5,
                        "name": "CanViewSampleReports"
                    },
                ]
            }
        ]
    },
    {
        "id": 2,
        "repoId": 1,
        "username": "editor1",
        "name": "Alice",
        "lastName": "Smith",
        "passwordHash": "hashed_password_editor1",
        "roles": [
            {
                "id": 2,
                "name": "Editor",
                "privileges": [
                    {
                        "id": 1,
                        "name": "CanReadResponsible"
                    },
                    {
                        "id": 2,
                        "name": "CanWriteResponsible"
                    },
                    {
                        "id": 4,
                        "name": "CanWriteSamples"
                    },
                ]
            }
        ]
    },
]
```


#### * DELETE/responsibility/1*

**Описание:** Удаление записи о назначении ответственного по ID.

**Входные параметры:**  
Параметр маршрута:  
- `resopnsible_id` (целочисленное число) - Идентификатор назначения, информацию о котором необходимо получить.

**Пример входных параметров:**
```
/responsibility/13
```

**Код статусы ответа:**
- `204 OK` - Успешное удаление назначения, ответ пустой
- `404 Not Found` - Назначение с указанным resopnsible_id не найдено

**Пример ответа (ответ пуст всегда):**
```json
{}
```


#### *POST /responsibility*

**Описание:** Добавление нового назначения ответственного. Возращает созданного ответственного с присвоенным ID.

**Заголовки:**
- `Content-Type: application/json`
- `Authorization: Bearer {ваш_токен}`

**Входные параметры:**  
Body (тип Sample_Responsible без ID):  
```json
{
  "SampleId": 0,
  "UserId": 0,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": false
}
```


**Пример входных параметров:**
```json
{
  "SampleId": 101,
  "UserId": 1001,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": true
}
```

**Код статусы ответа:**
- `201 OK` - Успешное добавление данных назначения
- `415 Unsupported Media Type` - Запрашиваемый тип контента не поддерживается
- `400 BadRequest` - Неверные параметры запроса

**Пример ответа:**
```json
{
  "Id": 26,
  "SampleId": 101,
  "UserId": 1001,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": true
}
```

#### *PUT /responsibility*

**Описание:** Обновление данных назначения. Возвращает обновленную запись о назначении.


**Входные параметры:**  
Query:  
- `repositoryId` (int) - ID репозитория, для которого нужно получить возможных ответственных.

Body (тип Sample_Responsible):  
```json
{
  "Id": 0,
  "SampleId": 0,
  "UserId": 0,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": false
}
```


**Пример входных параметров:**
```
/responsibility/2?repositoryId=1
```
```json
{
  "Id": 2,
  "SampleId": 101,
  "UserId": 1001,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": true
}
```

**Код статусы ответа:**
- `200 OK` - Успешное обновление данных назначения
- `415 Unsupported Media Type` - Запрашиваемый тип контента не поддерживается
- `400 BadRequest` - Неверные параметры запроса

**Пример ответа:**
```json
{
  "Id": 2,
  "SampleId": 101,
  "UserId": 1001,
  "AssignedAt": "2025-01-26T12:00:00",
  "IsCurrentResponsible": true
}
```


### *Контроллер пользователей*

#### *GET /users/by-repository*

**Описание:** Получение данных о пользователях определенного репозитория. Возвращает данные пользователей, включая их роли.

**Входные параметры:**  
Query:  
- `repositoryId` (int) - ID репозитория, для которого нужно получить возможных ответственных.

**Пример входных параметров:**
```
/users/by-repository?repositoryId=1
```

**Код статусы ответа:**
- `200 OK` - Успешный запрос, возвращается информация о пользователе
- `400 BadRequest` - Неверные параметры запроса


**Пример ответа:**
```json
[
    {
        "id": 1,
        "repoId": 1,
        "username": "admin",
        "name": "John",
        "lastName": "Doe",
        "roles": [
            {
                "id": 1,
                "name": "Admin",
                "privileges": [
 
                    {
                        "id": 4,
                        "name": "CanWriteSamples"
                    },
                    {
                        "id": 7,
                        "name": "CanDeleteUsers"
                    }
                ]
            }
        ]
    },
    {
        "id": 5,
        "repoId": 1,
        "username": "editor1",
        "name": "Alice",
        "lastName": "Smith",
        "roles": [
            {
                "id": 2,
                "name": "Editor",
                "privileges": [
                    {
                        "id": 1,
                        "name": "CanReadResponsible"
                    },
                ]
            }
        ]
    },
]
```

#### *GET /user/{user_id}*

**Описание:** Получение данных о пользователе по ID. Возвращает данные пользователя, включая его роли.

**Входные параметры:**  
Параметр маршрута:  
- `user_id` (целочисленное число) - Идентификатор пользователя, информацию о котором необходимо получить.

**Пример входных параметров:**
```
/user/6
```

**Код статусы ответа:**
- `200 OK` - Успешный запрос, возвращается информация о пользователе
- `404 Not Found` - Пользователь с указанным user_id не найден

**Пример ответа:**
```json
{
    "id": 6,
    "repoId": 1,
    "username": "admin",
    "name": "John",
    "lastName": "Doe",
    "roles": [
        {
            "id": 1,
            "name": "Admin",
            "privileges": [
                {
                    "id": 1,
                    "name": "CanReadResponsible"
                },
                {
                    "id": 2,
                    "name": "CanWriteResponsible"
                },
                {
                    "id": 3,
                    "name": "CanDeleteResponsible"
                }
            ]
        }
    ]
}
```

#### *DELETE /user/{user_id}*

**Описание:** Удаление пользователя. Ответ пустой, необходимо ориентироваться на код статусы.

**Входные параметры:**  
Параметр маршрута:  
- `user_id` (целочисленное число) - Идентификатор пользователя, информацию о котором необходимо удалить.

**Пример входных параметров:**
```
/user/123
```

**Код статусы ответа:**
- `204 No Content` - Успешное удаление пользователя
- `404 Not Found` - Пользователь с указанным user_id не найден

**Пример ответа (ответ пуст всегда):**
```json
{}
```

#### *POST /user/{user_id}*

**Описание:** Добавление данных пользователя. Возвращает пользователя с присвоенным ID.

**Входные параметры:**  
Body (тип User):  
{
  "username": "",
  "name": "",
  "lastName": "",
  "roles": [],
  "repoId": 0
}


**Пример входных параметров:**
```
/users/1?repositoryId=1
```
```json
{
    "id": 1,
    "repoId": 1,
    "username": "admin",
    "name": "John",
    "lastName": "Doe",
    "roles": [9]
}
```

**Код статусы ответа:**
- `201 OK` - Успешное добавление данных назначения
- `415 Unsupported Media Type` - Запрашиваемый тип контента не поддерживается
- `400 BadRequest` - Неверные параметры запроса


**Пример ответа:**
```json
{
    "id": 1,
    "repoId": 1,
    "username": "admin",
    "name": "John",
    "lastName": "Doe",
    "roles": [
                {
                    "id": 9,
                    "name": "Editor",
                    "privileges": [
                        {
                            "id": 1,
                            "name": "CanReadResponsible"
                        },
                        {
                            "id": 2,
                            "name": "CanWriteResponsible"
                        },
                        {
                            "id": 4,
                            "name": "CanWriteSamples"
                        },
                    ]
                }
            ]
}
```


#### *PUT /user/{user_id}*

**Описание:** Обновление данных пользователя. Возвращает токен для авторизации, данные для аутентификации пользователя (обновлённые), созданные пользователем маршруты.

**Входные параметры:**  
Параметр маршрута:
- `user_id` (целочисленное число) - Идентификатор пользователя, информацию о котором необходимо обновить.

Body (тип User):  
{
  "id": 0,
  "username": "",
  "name": "",
  "lastName": "",
  "roles": [],
  "repoId": 0
}


**Пример входных параметров:**
```
/users/1?repositoryId=1
```
```json
{
    "id": 1,
    "repoId": 1,
    "username": "admin",
    "name": "John",
    "lastName": "Doe",
    "roles": []
}
```

**Код статусы ответа:**
- `200 OK` - Успешное обновление данных назначения
- `415 Unsupported Media Type` - Запрашиваемый тип контента не поддерживается
- `400 BadRequest` - Неверные параметры запроса параметров)

**Пример ответа:**
```json
{
    "id": 1,
    "repoId": 1,
    "username": "admin",
    "name": "John",
    "lastName": "Doe",
    "roles": []
}
```



### *Контроллер проб*

#### *GET/samples/by-responsible-user?currentResponsibleId=1*

**Описание:**  
Получает список всех пробы, имеющих текущего ответственного с переданным ID пользователя.

**Входные параметры:**  
Query: 
- `currentResponsibleId` (int) - ID пользователя, для которого нужно получить пробы назначенные на него

**Пример входных параметров:**  
URL: `/samples/by-responsible-user?currentResponsibleId=1`

**Коды статусов ответа:**
- `200 OK` - Успешно возвращен список проб
- `400 BadRequest` - Неверные параметры запроса

**Пример ответа:**
```json
[
    {
        "id": 1,
        "repoId": 1,
        "type": 0
    },
    {
        "id": 6,
        "repoId": 1,
        "type": 3
    }
]
```

## *Реализация API*

[Код реализации Rest API в отдельном репозитории](https://github.com/orlovaska/HSE-SA-Microservices-API)





### Контроллер назначений ответственных
#### *GET /responsibility/{sample_id:int}* 
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/GetResponsibilitiesForSample200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/GetResponsibilitiesForSample400.png?raw=true)

#### *GET /responsibility/assignable-responsibles* 
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/GetAssignableResponsibles200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/GetAssignableResponsibles400.png?raw=true)

#### *DELETE /responsibility/{id:int}*  
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/DeleteResponsibility204.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/DeleteResponsibility404.png?raw=true)

#### *POST /responsibility* 
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/AddResponsibility201.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/AddResponsibility415.png?raw=true)

#### *PUT /responsibility/{id:int}* 
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/UpdateResponsibility200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/UpdateResponsibility400.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/ResponsibilityController/UpdateResponsibility415.png?raw=true)



### Контроллер пользователей
#### *GET /users/repo/{repoId:int}* 
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/GetUsersByRepoId200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/GetUsersByRepoId400.png?raw=true)

#### *GET /users/{id:int}*  
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/GetUserById200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/GetUserById404.png?raw=true)

#### *DELETE /users/{id:int}*
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/DeleteUser204.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/DeleteUser404.png?raw=true)

#### *POST /users*  
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/AddUser201.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/AddUser415.png?raw=true)

#### *PUT /users/{id:int}*
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/UpdateUser200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/UpdateUser400.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/UsersController/UpdateUser415.png?raw=true)



### Контроллер проб
#### *GET /samples/{currentResponsibleId:int}*  
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/SamplesController/GetSamplesByResponsibleUser200.png?raw=true)
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork4/Lab%20Work%20%E2%84%964/docs/PosmanTests/SamplesController/GetSamplesByResponsibleUser400.png?raw=true)
