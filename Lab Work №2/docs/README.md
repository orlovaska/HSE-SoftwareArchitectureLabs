# Диаграмма системного контекста

![image](https://raw.githubusercontent.com/orlovaska/HSE-SoftwareArchitectureLabs/671cd71e63e0a38e73d6ae87e9159994d7667c70/Lab%20Work%20%E2%84%962/docs/C4%20Diagrams/Context%20Diagram.svg)

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
Container(c1, "Система анализа микроскопических исследований", "Система")
Container_Ext(link, "Сторонняя система сканирования образцов", "Система", "Сторонняя система, позволяющая сканировать и отправлять цитологические образцы")
Rel_D(administrator, c1, "Настраивает правила назначения проб, назначает ответственных за анализ")
Rel_D(c1, link, "Создает ссылку на результаты проб")
Rel_D(link, c1, "Передает данные отсканированного образца")
Rel_D(administrator, doctor, "Передает назначение ответственного")
Rel_D(doctor, c1, "Получает назначение на пробы, открывает ссылку на результаты, валидирует данные")

SHOW_LEGEND()
@enduml
```

# Диаграмма контейнеров с пояснениями по выбору базового архитектурного стиля / архитектуры уровня приложений 

## Выбор архитектурного стиля:  
Выбранный стиль: Клиент-Серверная архитектура.  
Обоснование:  
* **Использование в существующей системе:** Клиент-серверная архитектура легко интегрируется в уже существующую систему анализа микроскопических исследований компании ООО "Медика продакт".
* **Возможность использования на разных устройствах:** Клиент-Серверная архитектура обеспечивает совместимость с широким спектром устройств, от мобильных телефонов до настольных компьютеров.
* **Обновления в реальном времени:** Клиент-Серверная архитектура обеспечивает эффективный механизм для обновления/получения данных в реальном времени, что важно для своевременного анализа цитологических образцов.
* **Отсутствие требований к высокой производительности устройств пользователей:** Основная нагрузка ложится на сервер, что позволяет клиентским устройствам с низкими вычислительными возможностями работать с системой без ухудшения производительности, что особенно актуально в условиях использования устаревшего или маломощного оборудования в больничных учреждениях.

## Выбор архитектуры уровня приложений на фронтенде:  
Выбранная архитектура: MVC (Model-View-Controller).  
Обоснование: 
* **Использование в существующем сервисе:** MVC архитектура используется в уже существующих сервисах системы анализа микроскопических исследований компании ООО "Медика продакт".
* **Разделение ответственности:** MVC разделяет данные Model, интерфейс View и логику взаимодействия Controller, что упрощает разработку и поддержку веб-приложения.  
* **Тестируемость:** Логика взаимодействий сосредоточена в Controller, что упрощает написание модульных тестов и изоляцию потенциальных ошибок.  
* **Совместимость с React:** Реализация MVC в React (необходимость разработки на котором определена Заказчиком) позволяет использовать такие подходы, как классы-контроллеры для управления состоянием и Model внешнею библиотеки, такие как Redux.  
* **Поддержка производительности:** Архитектура снижает сложность отображения данных в View за счет четкой связи с Controller, что важно для обеспечения стабильной работы на устаревших или маломощных устройствах в больничных учреждениях.  


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
