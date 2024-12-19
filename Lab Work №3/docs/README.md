#  Диаграммы контейнеров и компонентов
## Диаграмма контейнеров

![image](https://raw.githubusercontent.com/orlovaska/HSE-SoftwareArchitectureLabs/671cd71e63e0a38e73d6ae87e9159994d7667c70/Lab%20Work%20%E2%84%962/docs/C4%20Diagrams/Containers%20Diagram.svg)

```PlantUML
@startuml
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
' uncomment the following line and comment the first to use locally
' !include C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="micro service\neight sided")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

SHOW_PERSON_OUTLINE()

Person(administrator, Администратор, "Управляет правилами назначения и распределением проб")
Person(doctor, Врач, "Получает назначения на пробы и валидирует их данные")
System_Boundary(c1, "Система анализа микроскопических исследований") {
    Container(clientAdministrator, "Клиент администратора", "TypeScript, React", "Позволяет управлять правилами назначения проб и назначать ответственных")
    Container(clientDoctor, "Клиент врача", "TypeScript, React", "Позволяет получать назначения на пробы и валидировать результаты")
    Container(server, "Сервер", "C#, .NET", "Ответственен за обработку и передачу данных")
    ContainerDb(db, "База данных", "PostgreSQL", "Хранит данные по пробам и результаты анализов", $sprite="msql_server")
}
Container_Ext(link, "Сторонняя система сканирования образцов", "System", "Сторонняя система для сканирования и передачи данных образцов")
Rel_D(administrator, clientAdministrator, "Настраивает правила назначения проб, назначает ответственных за анализ")
Rel_D(clientAdministrator, server, "Отправляет созданные правила назначения и распределение проб")
Rel_D(server, db, "Сохраняет информацию о правилах назначения и данные по пробам")
Rel_U(db, server, "Извлекает данные по пробам и правилам назначения")
Rel_D(server, link, "Создает ссылку на результаты проб")
Rel_D(link, server, "Передает результаты отсканированных образцов")
Rel_D(server, clientDoctor, "Отправляет назначения и ссылку на пробу")
Rel_D(doctor, clientDoctor, "Получает назначение на пробы и проверяет результаты")
Rel_D(clientDoctor, server, "Отправляет запрос на результаты проб")
Rel_D(server, clientDoctor, "Отправляет результаты проб для проверки")

SHOW_LEGEND()
@enduml
```

# Диаграмма компонентов

## Веб-клиент Администратора

![image](https://raw.githubusercontent.com/orlovaska/HSE-SoftwareArchitectureLabs/671cd71e63e0a38e73d6ae87e9159994d7667c70/Lab%20Work%20%E2%84%962/docs/C4%20Diagrams/Components%20Diagram%20Admin%20Client.svg)

```PlantUML
@startuml "messagebus"
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
' uncomment the following line and comment the first to use locally
' !include C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="micro service\neight sided")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

SHOW_PERSON_OUTLINE()

Person(administrator, Администратор, "Управляет правилами назначения и распределением проб")
System(s, "Сервер", "Ответственен за обработку и передачу данных", $type="Container / C#, .NET")
Boundary(c, "Клиент администратора", "Container / TypeScript, React, Redux") {
Container(v, "Назначение проб", "Component: UI View", "Экраны назначения проб по правилам или вручную.")
Container(vm, "ViewModel", "Component: ViewModel", "Логика, управляющая данными и взаимодействием с View.")
Container(m, "Model", "Component: Model - ReduxStore", "Хранит данные о пробах и правилах назначения. Обрабатывает запросы от ViewModel.")
}
Rel_D(administrator, v, "Назначение проб вручную или по правилам (пользовательское взаимодействие)")
Rel_D(v, vm, "Отправка пользовательского взаимодействия")
Rel_D(vm, v, "Уведомление об обновлении данных")
BiRel(vm, m, "Получение и обновление данных о пробах и правилах назначения")
BiRel(m, s, "Получение данных о правилах назначения проб")

SHOW_LEGEND()
@enduml
```

## Веб-клиент Врача

![image](https://raw.githubusercontent.com/orlovaska/HSE-SoftwareArchitectureLabs/671cd71e63e0a38e73d6ae87e9159994d7667c70/Lab%20Work%20%E2%84%962/docs/C4%20Diagrams/Components%20Diagram%20Physician%20Client.svg)

```PlantUML
@startuml "messagebus"
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
' uncomment the following line and comment the first to use locally
' !include C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="micro service\neight sided")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

SHOW_PERSON_OUTLINE()

Person(doctor, Врач, "Получает назначения на пробы, просматривает результаты и валидирует данные.")
System(s, "Сервер", "Ответственен за обработку и передачу данных", $type="Container / C#, .NET")
Boundary(c, "Клиент врача", "Container / TypeScript, React, Redux") {
Container(v, "Назначения и результаты проб", "Component: UI View", "Экраны для просмотра назначений проб и их результатов.")
Container(vm, "ViewModel", "Component: ViewModel", "Логика, управляющая данными и взаимодействием с View.")
Container(m, "Model", "Component: Model - ReduxStore", "Хранит данные о пробах, результатах и назначениях. Обрабатывает запросы от ViewModel.")
}
Rel_D(doctor, v, "Просмотр назначений и результатов проб (пользовательское взаимодействие)")
Rel_D(v, vm, "Отправка пользовательского взаимодействия")
Rel_D(vm, v, "Уведомление об обновлении данных")
BiRel(vm, m, "Получение и обновление данных о пробах, назначениях и результатах")
BiRel(m, s, "Получение данных о результатах проб и назначениях")

SHOW_LEGEND()
@enduml
```

## Сервер

![image](https://raw.githubusercontent.com/orlovaska/HSE-SoftwareArchitectureLabs/671cd71e63e0a38e73d6ae87e9159994d7667c70/Lab%20Work%20%E2%84%962/docs/C4%20Diagrams/Components%20Diagram%20Server.svg)

```PlantUML
@startuml "messagebus"
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml
' uncomment the following line and comment the first to use locally
' !include C4_Container.puml

AddElementTag("microService", $shape=EightSidedShape(), $bgColor="CornflowerBlue", $fontColor="white", $legendText="micro service\neight sided")
AddElementTag("storage", $shape=RoundedBoxShape(), $bgColor="lightSkyBlue", $fontColor="white")

SHOW_PERSON_OUTLINE()

System(mta, "Клиент администратора", "Позволяет управлять правилами назначения проб и назначать ответственных за анализ", $type="Container / TypeScript, React")
System(mt, "Клиент врача", "Позволяет получать назначения на пробы и валидировать их данные", $type="Container / TypeScript, React")
Container_Ext(link, "Сторонняя система сканирования образцов", "System", "Сторонняя система для сканирования и передачи данных образцов")
ContainerDb(db, "База данных", "PostgreSQL", "Хранит информацию о пробах, результатах и назначениях", $sprite="postgresql")
Boundary(s, "Сервер", "Container / C#, ASP.NET") {
    Container(cP, "Контроллер проб", "Component: Controller", "Обрабатывает запросы на создание, обновление и удаление информации о пробах. Взаимодействует с бизнес-логикой и базой данных для получения и сохранения данных. Также взаимодействует с сторонней системой сканирования образцов.")
    Container(cR, "Контроллер правил назначения", "Component: Controller", "Обрабатывает запросы на создание, обновление и удаление правил назначения проб. Взаимодействует с бизнес-логикой и базой данных для получения и сохранения данных.")
    Container(cPa, "Контроллер пациентов", "Component: Controller", "Обрабатывает запросы на создание, обновление и удаление информации о пациентах. Взаимодействует с базой данных для получения и сохранения данных пациентов.")
    ' Организация контроллеров в уголок
    cP -[hidden]-> cR
    cP -[hidden]-> cPa
}
BiRel(mta, cR, "HTTP-запросы и ответы для создания/обновления правил назначения")
BiRel(mt, cP, "HTTP-запросы и ответы для создания/обновления проб")
BiRel(mt, cPa, "HTTP-запросы и ответы для создания/обновления данных пациентов")
BiRel(cP, db, "Сохранение и извлечение данных по пробам")
BiRel(cR, db, "Получение и сохранение данных о правилах назначения проб")
BiRel(cPa, db, "Получение и сохранение данных пациентов")
BiRel(cR, cP, "Взаимодействие с контроллером проб для назначения")
BiRel(cP, cPa, "Передача данных о пробах пациентам для назначения")
BiRel(mta, cP, "HTTP-запросы и ответы для создания/обновления проб (администратор)")
BiRel(cP, link, "Взаимодействует с сторонней системой для получения данных сканированных образцов")
Rel_D(link, cP, "Отправка данных сканированных образцов на сервер")

' Перемещаем стороннюю систему слева от сервера
link -[hidden]-> s

SHOW_LEGEND()
@enduml
```
# Диаграммы последовательности
## Диаграмма последовательности Общая
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork3/Lab%20Work%20%E2%84%963/docs/Diagrams/Sequence%20diagrams/Sequence%20diagram%20General.png?raw=true)
## Диаграмма последовательности Декомпозиция Выбор ответственных по пробе
![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork3/Lab%20Work%20%E2%84%963/docs/Diagrams/Sequence%20diagrams/Sequence%20diagram%20Dec%20Admin%20Assigment.png?raw=true)

# Модель базы данных

![image](https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork3/Lab%20Work%20%E2%84%963/docs/Diagrams/Class%20diagrams/Class%20diagrams%20General.png?raw=true)


```PlantUML
@startuml

class Repository {
  +Id: int
  -Name: string
  -DefaultUserDiskSpace: int
  -Label: string
}

class User {
  +Id: int
  -Login: string
  -Name: string
  -DiskSpace: int
  -DiskSpaceExpitation: int
  -CanChangedPassword: boolean
}

class Patient {
  +Id: int
  -FirstName: string
  -Middlename: string
  -PolicyNumber: string
  -Email: string
  -Birthdate: string
  -Phone: string
  -Gender: boolean
}

class Sample_Folder {
  +Id: int
  -OwnerId: int
  -Size: int
  -UploadTime: int
}

class Sample {
  +Id: int
  -SampleType: string
  -PrewiewUrl: string
  -BarcodeUrl: string
  -Workstation: string
  -PathologyFlag: boolean
  -Haslmages: boolean
}

class Folder {
  +Id: int
  -Name: string
  -IsArchiveFolder: boolean
  -CanBeArchived: boolean
}

class Sample_Responsible {
  +Id: int
  -SampleId: int
  -UserAssignedId: int
  -ResponsibleId: int
  -AssignedAt: data
  -IsCurrentResponsible: boolean
}

class Session {
  +Id: int
  -Token: string
  -StartDate: string
  -EndDate: string
}

class Role {
  +Id: int
  -RoleName: string
  -Description: string
}

class Privilege {
  +Id: int
  -PrivilegeName: string
}

class RolePrivilege {
  +Id: int
  -RoleId: int
  -PrivilegeId: int
}

class Country {
  +Id: int
  -NameEn: string
  -NameRu: string
  -IsInEU: boolean
  -Tax: int
}

class UserRole {
  +Id: int
  -UserId: int
  -RoleId: int
}

' Связи
Sample_Folder <|-- Sample: Inherits
Sample_Folder <|-- Folder: Inherits

Repository "1" -- "0..*" User: Has
Repository "1" -- "0..*" Patient: Has
Patient "1" -- "0..*" Sample: Has
Sample "1" -- "0..*" Sample_Responsible: Has
User "1" -- "0..*" Sample_Responsible: Has
User "1" -- "0..*" Sample_Folder: Has
User "1" -- "0..*" Session: Has
RolePrivilege "0..*" -up- "1" Role: Has
RolePrivilege "0..*" -- "1" Privilege: Has
User "1" -- "0..*" Country: LivesIn
UserRole "0..*" -up- "0..*" User: Has
UserRole "0..*" -- "1" Role: Has

@enduml
```

Краткое пояснение к модели базы данных:

1. **`Repository` (Репозиторий):**
   - Представляет репозиторий, который может содержать пользователей и пациентов (обычно создается под КДЛ).
   - Связан с пользователями и пациентами, которые имеют доступ к данным репозитория.

2. **`User` (Пользователь):**
   - Учетная запись пользователя в системе (чаще сотрудника КДЛ).
   - Может иметь несколько проб и папок.
   - Может иметь роли и привилегии, которые определяют его доступ.

3. **`Patient` (Пациент):**
   - Пациент, чьи данные могут быть связаны с пробами.
   - Имеет информацию о полисе, контактах и гендере.

4. **`Sample_Folder` (Папка с пробами):**
   - Представляет базовую сущность пробы или папки.
   - Может иметь размер и дату загрузки.

5. **`Sample` (Проба):**
   - Представляет отдельную пробу, наследуется от Sample_Folder.
   - Может несколько ответственных пользователей.

6. **`Folder` (Папка):**
   - Представляет отдельную папку, наследуется от Sample_Folder.
   - Обычная папка для хранения данных, может быть архивной и иметь статус архивируемой.

7. **`Sample_Responsible` (Ответственный за пробу):**
   - Связующая таблица для пробы и пользователя, назначаемого ответственным за конкретную пробу (только за пробу, не за папку).

8. **`Session` (Сессия):**
   - Информация о сессии пользователя с токеном и временем начала и конца.

9. **`Role` (Роль):**
   - Роль пользователя в системе с описанием и назначенными привилегиями.

10. **`Privilege` (Привилегия):**
    - Содержит сущности, представляющие пользователям определенные возможности.

11. **`RolePrivilege` (Роль-привилегия):**
    - Связывает роли с привилегиями.

12. **`Country` (Страна):**
    - Страна, где находится пользователь, с информацией о налогах и принадлежности к ЕС.

13. **`UserRole` (Роль пользователя):**
    - Связывает пользователей с их ролями в системе.


# Код с учетом принципов KISS, YAGNI, DRY и SOLID.
## Серверный код
### Код контроллера
```
[ApiController]
[Route("api/samples")]
public class SamplesController : ControllerBase
{
    private readonly SampleService _sampleService;

    public SamplesController(ISampleRepository repository)
    {
        _sampleService = new SampleService(repository);
    }

    [HttpGet]
    public IActionResult GetSamples()
    {
        var samples = _sampleService.GetAllSamples();
        return Ok(samples);
    }

    [HttpPost("{id}/assign")]
    public IActionResult AssignSample(int id, [FromBody] AssignRequest request)
    {
        var success = _sampleService.AssignResponsible(id, request.UserId);
        return success ? NoContent() : NotFound();
    }
}
```

### Код сервиса (BLL)
```
public class SampleService
{
    private readonly ISampleRepository _repository;

    public SampleService(ISampleRepository repository)
    {
        _repository = repository;
    }

    public IEnumerable<Sample> GetAllSamples() => _repository.GetAllSamples();

    public bool AssignResponsible(int sampleId, string userId)
    {
        var sample = _repository.GetSampleById(sampleId);
        if (sample == null) return false;
    
        sample.AssignedTo = userId;
        _repository.UpdateSample(sample);
        return true;
    }

}

```
### Код интерфейса репозитория (DAL)
```
public interface ISampleRepository
{
    IEnumerable<Sample> GetAllSamples();
    Sample? GetSampleById(int id);
    void UpdateSample(Sample sample);
}
```

---
#### **Принцип KISS (Keep It Simple, Stupid):**  
Код написан так, чтобы обеспечить минимальную сложность:  
- Контроллеры выполняют только задачи маршрутизации и делегируют всю логику сервису. Это уменьшает их сложность и ответственность.  
- Вся бизнес-логика сосредоточена в `SampleService`, что упрощает внесение изменений.  
- Репозиторий (`ISampleRepository`) скрывает детали работы с данными, предоставляя простой интерфейс для взаимодействия.

---

#### **Принцип YAGNI (You Aren't Gonna Need It):**  
Реализованы только необходимые функции:  
- Логика обработки проб ограничивается их получением (`GetSamples`) и назначением ответственных (`AssignResponsible`), что соответствует требованиям задачи.  
- Никакие дополнительные методы (например, удаление или сложные фильтры) не реализованы, так как они не указаны в требованиях.

---

#### **Принцип DRY (Don't Repeat Yourself):**  
Код избегает дублирования:  
- Обработка получения данных проб (в методах `GetAllSamples` и `GetSampleById`) вынесена в `ISampleRepository`.  
- Логика обновления ответственного лица сосредоточена в методе `AssignResponsible`, чтобы не повторять её в нескольких местах.

---

#### **Принцип SOLID:**  
- **S (Single Responsibility Principle):**  
  - `SamplesController` отвечает только за маршрутизацию.  
  - `SampleService` управляет бизнес-логикой, связанной с пробами.  
  - `ISampleRepository` инкапсулирует логику доступа к данным.  
- **O (Open/Closed Principle):**  
  - Добавление нового функционала (например, фильтрации проб) возможно через расширение интерфейса `ISampleRepository`, не изменяя существующий код.  
- **L (Liskov Substitution Principle):**  
  - Любая реализация `ISampleRepository` может быть заменена на другую без изменения кода, который использует этот интерфейс.  
- **I (Interface Segregation Principle):**  
  - `ISampleRepository` предоставляет только те методы, которые необходимы для работы с пробами, без избыточной функциональности.  
- **D (Dependency Inversion Principle):**  
  - `SampleService` зависит от интерфейса `ISampleRepository`, а не от конкретной реализации, что упрощает замену или тестирование.

---

# Другие принципы разработки: 

## BDUF - Big design up front (Масштабное проектирование прежде всего)
**Что такое BDUF?**  
BDUF — это подход к разработке программного обеспечения, при котором проектирование и планирование всей архитектуры системы происходят на самых ранних этапах разработки. В рамках этого подхода проектировщик должен создать детальное описание всей системы до начала реализации, определяя все ключевые аспекты, включая структуру, компоненты и взаимодействие между ними.

**Применимость:** BDUF может быть полезен в ситуациях, когда требования к проекту ясны, стабильны и вряд ли изменятся в процессе работы. Такой подход позволяет заранее предусмотреть все детали и свести к минимуму неопределенности на этапе реализации.

**Случай отказа:** BDUF может быть неэффективным в ситуациях, когда проект требует быстрого старта реализации, а требования еще не до конца сформулированы (или очень плавающие и с большой вероятностью будут изменены далее). В таких случаях излишне детализированное проектирование может затормозить процесс.

**Применимость в данном проекте:** Подход BDUF является подходящим, поскольку проект разрабатывается на основе заранее определенных и стабильных требований, что позволяет продумать аспекты разрабатываемого модуля на этапе проектирования.

## SoC - Separation оf concerns (Принцип разделения ответственности)
**Что такое SoC?**  
SoC — это принцип проектирования программного обеспечения, который предполагает разделение системы на различные модули или компоненты, каждый из которых отвечает за определенную задачу или область функциональности.

**Применимость:** SoC особенно полезен в крупных проектах, где различные компоненты системы могут развиваться в будущем. Данный принцип способствует созданию модульной архитектуры, где каждый компонент сосредоточен на выполнении своей задачи.

**Случай отказа:** Принцип SoC может быть менее эффективным в маленьких проектах, где разделение может быть излишним, лишь усложняя архитектуру.

**Применимость в данном проекте:** Принцип SoC является подходящим, поскольку разработка модуля происходит в рамках большой системы, где разделение на компоненты уже заложено в архитектуре. Кроме того, существует ограничения на использование технологий, на фронтенде обязательно должна использоваться библиотека React, в которую уже заложено разделение клиентского кода на компоненты.

## MVP - Minimum Viable Product (Минимально жизнеспособный продукт)

**Что такое MVP?**  
MVP — это концепция, согласно которой на начальных этапах разработки создается версия продукта с минимальным набором функций, достаточным для того, чтобы продукт был работоспособным и мог быть протестирован на реальных пользователях.

**Применимость:**  
MVP особенно полезен в стартапах и инновационных проектах, где важно быстро проверить жизнеспособность идеи, протестировать гипотезы и определить, будет ли продукт востребован на рынке.

**Случай отказа:**  
MVP может быть нецелесообразен в проектах с высокими требованиями к качеству и безопасности, например, в области финансовых технологий или медицинских приложений, где минимальный функционал может не удовлетворить потребности пользователей или привести к негативным последствиям.

**Применимость в данном проекте:**  
Принцип MVP не подходит для данного проекта, поскольку требования к системе уже четко определены, и проект разрабатывается с учетом детализированного плана. Проект (особенно если учитывать, что он связан с медицинской областью) требует более полного и качественного подхода с самого начала, чтобы избежать излишней доработки. Кроме того, если облегчить существующие требования до MVP, проект не будет проходить по критериям ВКР.


## PoC - Proof of concept (Доказательство концепции)

**Что такое PoC?**  
PoC — это прототип или экспериментальная модель, предназначенная для проверки жизнеспособности идеи или концепции, прежде чем приступить к полному разработанному продукту. Основная цель PoC — продемонстрировать, что предложенная концепция или технология работает в реальных условиях и может быть использована для решения поставленной задачи.

**Применимость:**  
PoC полезен в проектах, где существует высокая степень неопределенности или рисков, связанных с внедрением новой технологии или подхода. Это позволяет протестировать концепцию на практике с минимальными затратами. PoC особенно эффективен на ранних стадиях разработки.

**Случай отказа:**  
PoC может быть избыточным в проектах, где требования уже хорошо изучены и где нет неопределенности в отношении технологии или подхода. В таких случаях создание PoC может лишь замедлить процесс разработки, увеличив время на проверку идеи, которая уже подтверждена или реализована в других проектах. В таких проектах сразу можно переходить к разработке полноценного решения.

**Применимость в данном проекте:**  
В данном проекте PoC является избыточным, поскольку требования хорошо изучены и уже известны. Большиство применимых технологий прописаны в ограничениях проекта, потому нет необходимости в дополнительных доказательствах их жизнеспособности. Проект может сразу перейти к этапу полной разработки, избегая лишних этапов тестирования концепции.
