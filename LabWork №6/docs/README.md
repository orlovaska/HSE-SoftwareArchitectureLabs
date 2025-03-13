# Лабораторная работа № 6 - Шаблоны проектирования

# Шаблоны проектирования Gang of Four (GoF)

## Порождающие шаблоны

### Фабричный метод / Factory Method

**Назначение:**  
Определяет интерфейс для создания объекта, но оставляет подклассам решение о том, какой класс инстанцировать.  

**Пример:**  
1. Есть интерфейс объекта `Sample`, есть классы объектов, которые реализуют этот интерфейс: `CytologySample` и `HistologySample`.  
2. Есть интерфейс фабрики `SampleFactory`, в котором определён метод создания пробы. Есть классы фабрики, которые создают пробы: `CytologySampleFactory` и `HistologySampleFactory`.  
3. Благодаря тому, что `CytologySample` и `HistologySample` наследуются от `Sample`, метод фабрики `SampleFactory` не обязан реализовывать каждый конкретный тип объекта — это делают подклассы.  


**UML:**  
<img width="1255" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/FactoryMethod.png?raw=true">  

**Код:**
```C#
using System;

// Интерфейс для всех проб
public interface ISample
{
    void Analyze();
}

// Класс цитологической пробы
public class CytologySample : ISample
{
    public void Analyze()
    {
        Console.WriteLine("Analyzing cytology sample");
    }
}

// Класс гистологической пробы
public class HistologySample : ISample
{
    public void Analyze()
    {
        Console.WriteLine("Analyzing histology sample");
    }
}

// Интерфейс фабрики проб
public interface ISampleFactory
{
    ISample CreateSample();
}

// Фабрика для создания цитологических проб
public class CytologySampleFactory : ISampleFactory
{
    public ISample CreateSample()
    {
        return new CytologySample();
    }
}

// Фабрика для создания гистологических проб
public class HistologySampleFactory : ISampleFactory
{
    public ISample CreateSample()
    {
        return new HistologySample();
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        ISampleFactory cytologyFactory = new CytologySampleFactory();
        ISample cytologySample = cytologyFactory.CreateSample();
        cytologySample.Analyze();

        ISampleFactory histologyFactory = new HistologySampleFactory();
        ISample histologySample = histologyFactory.CreateSample();
        histologySample.Analyze();
    }
}

```

### Абстрактная фабрика / Abstract Factory

**Назначение:**  
Предоставляет интерфейс для создания семейств связанных или зависимых объектов без указания их конкретных классов.

**Пример:**  
1. Вводятся два протокола: `BloodTest` и `BiopsyTest`, создаются конкретные классы, реализующие эти протоколы. В данном случае, `BasicBloodTest` и `AdvancedBloodTest` — это два варианта анализа крови, а `BasicBiopsyTest` и `AdvancedBiopsyTest` — два варианта биопсии. Каждый из этих классов реализует соответствующие методы протоколов.
2. Создается протокол `LabTestFactory`, который предоставляет интерфейс для создания связанных объектов анализов крови и биопсий. Создаются две конкретные реализации абстрактной фабрики: `BasicLabTestFactory` и `AdvancedLabTestFactory`. Каждая из этих фабрик реализует методы `CreateBloodTest` и `CreateBiopsyTest`, возвращая соответствующие объекты анализов крови и биопсий.
3. Есть класс `Lab`, который не содержит конкретных объектов анализов крови и биопсий (может быть любая реализация) и объекта фабрики (может быть любая). В зависимости от использования класса клиента, будет определяться, какие именно анализы крови и биопсии будут проведены.


**UML:**  
<img width="1380" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/AbstractFactory.png?raw=true">  

```PlantUML
@startuml

interface BloodTest {
  +Run(): void
}

interface BiopsyTest {
  +Run(): void
}

class BasicBloodTest implements BloodTest {
  +Run(): void
}

class AdvancedBloodTest implements BloodTest {
  +Run(): void
}

class BasicBiopsyTest implements BiopsyTest {
  +Run(): void
}

class AdvancedBiopsyTest implements BiopsyTest {
  +Run(): void
}

interface LabTestFactory {
  +CreateBloodTest(): BloodTest
  +CreateBiopsyTest(): BiopsyTest
}

class BasicLabTestFactory implements LabTestFactory {
  +CreateBloodTest(): BasicBloodTest
  +CreateBiopsyTest(): BasicBiopsyTest
}

class AdvancedLabTestFactory implements LabTestFactory {
  +CreateBloodTest(): AdvancedBloodTest
  +CreateBiopsyTest(): AdvancedBiopsyTest
}

class Lab {
  - bloodTest: BloodTest
  - biopsyTest: BiopsyTest
  + PerformTests(): void
}

Lab --> LabTestFactory
Lab --> BloodTest
Lab --> BiopsyTest

@enduml
```

**Код:**
```С#
using System;

// Интерфейсы для тестов
public interface BloodTest
{
    void Run();
}

public interface BiopsyTest
{
    void Run();
}

// Конкретные классы тестов
public class BasicBloodTest : BloodTest
{
    public void Run()
    {
        Console.WriteLine("Running basic blood test.");
    }
}

public class AdvancedBloodTest : BloodTest
{
    public void Run()
    {
        Console.WriteLine("Running advanced blood test.");
    }
}

public class BasicBiopsyTest : BiopsyTest
{
    public void Run()
    {
        Console.WriteLine("Performing basic biopsy test.");
    }
}

public class AdvancedBiopsyTest : BiopsyTest
{
    public void Run()
    {
        Console.WriteLine("Performing advanced biopsy test.");
    }
}

// Абстрактная фабрика
public interface LabTestFactory
{
    BloodTest CreateBloodTest();
    BiopsyTest CreateBiopsyTest();
}

// Конкретные фабрики
public class BasicLabTestFactory : LabTestFactory
{
    public BloodTest CreateBloodTest()
    {
        return new BasicBloodTest();
    }

    public BiopsyTest CreateBiopsyTest()
    {
        return new BasicBiopsyTest();
    }
}

public class AdvancedLabTestFactory : LabTestFactory
{
    public BloodTest CreateBloodTest()
    {
        return new AdvancedBloodTest();
    }

    public BiopsyTest CreateBiopsyTest()
    {
        return new AdvancedBiopsyTest();
    }
}

// Клиентский класс
public class Lab
{
    private BloodTest _bloodTest;
    private BiopsyTest _biopsyTest;

    public Lab(LabTestFactory factory)
    {
        _bloodTest = factory.CreateBloodTest();
        _biopsyTest = factory.CreateBiopsyTest();
    }

    public void PerformTests()
    {
        _bloodTest.Run();
        _biopsyTest.Run();
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        LabTestFactory basicFactory = new BasicLabTestFactory();
        LabTestFactory advancedFactory = new AdvancedLabTestFactory();

        Lab basicLab = new Lab(basicFactory);
        basicLab.PerformTests();

        Lab advancedLab = new Lab(advancedFactory);
        advancedLab.PerformTests();
    }
}
```

### Одиночка / Singleton

**Назначение:**  
Гарантирует, что у класса есть только один экземпляр и предоставляет к нему глобальную точку доступа.

**Пример:**  
1. Есть класс `FixtureSingleton<TFixture>`, в котором используется статическая переменная экземпляра этого класса (`_Instance`), а приватный конструктор предотвращает создание других экземпляров.
2. Метод `Instance` используется для получения единственного экземпляра класса. Если экземпляр еще не создан, он инициализируется, и затем можно вызвать его методы.
3. Обеспечивает единственный экземпляр класса TFixture, который реализует интерфейс IFileStorageFixture. Гарантирует создание и доступ к этому экземпляру в многопоточном окружении с помощью блокировки и очистки состояния при инициализации.


**UML:**  
<img width="1183" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Singleton.png?raw=true">  
```PlantUML
@startuml
class FixtureSingleton<TFixture> {
  - _Instance: TFixture
  - theLock: object
  - constructor(): void
  + Instance: TFixture
}

FixtureSingleton --> FixtureSingleton : _Instance
FixtureSingleton --> FixtureSingleton : theLock
@enduml

```

**Код:**
```С#
internal class FixtureSingleton<TFixture>
    where TFixture : IFileStorageFixture, new()
{
    private static TFixture _Instance;

    private static object theLock = new object();

    public static TFixture Instance
    {
        get
        {
            lock (theLock)
            {
                if (_Instance == null)
                {
                    _Instance = new TFixture();
                    _Instance.Clear();
                }
                return _Instance;
            }
        }
    }

}
```


## Структурные шаблоны

### Адаптер / Adapter

**Назначение:**  
Преобразует интерфейс одного класса в интерфейс другого, который ожидают клиенты. Адаптер делает возможной совместную работу классов с несовместимыми интерфейсами.

**Пример:**  
Создаём класс адаптера (тут их два), где он реализует нужный метод через обработку несовместимых интерфейсов.

**UML:**  
<img width="642" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Adapter.png?raw=true">
```PlantUML
@startuml

interface IDoctorNotificationSender {
    + SendNotification(doctorId: string, message: string)
}

class EmailDoctorNotificationService {
    + SendEmail(doctorId: string, message: string)
}

class VisionAccountAdapter {
    - emailService: EmailDoctorNotificationService
    + VisionAccountAdapter(emailService: EmailDoctorNotificationService)
    + SendNotification(doctorId: string, message: string)
}

class EmailDoctorNotificationAdapter {
    - emailService: EmailDoctorNotificationService
    + EmailDoctorNotificationAdapter(emailService: EmailDoctorNotificationService)
    + SendNotification(doctorId: string, message: string)
}

IDoctorNotificationSender <|-- VisionAccountAdapter
IDoctorNotificationSender <|-- EmailDoctorNotificationAdapter
EmailDoctorNotificationService <-- EmailDoctorNotificationAdapter

```

**Код:**
```С#
// Интерфейс для отправки уведомлений врачам
public interface IDoctorNotificationSender
{
    void SendNotification(string doctorId, string message);
}

// Система для отправки email врачам
public class EmailDoctorNotificationService
{
    public void SendEmail(string doctorId, string message)
    {
        Console.WriteLine($"Sending Email to Doctor {doctorId}: {message}");
    }
}

// Адаптер для отправки уведомлений внутри системы Vision
public class VisionAccountAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public VisionAccountAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message); // Уведомление через систему Vision
    }
}

// Адаптер для отправки email врачам
public class EmailDoctorNotificationAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public EmailDoctorNotificationAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message);
    }
}

// Использование
var emailService = new EmailDoctorNotificationService();

IDoctorNotificationSender visionAccountAdapter = new VisionAccountAdapter(emailService); // Уведомления внутри Vision
IDoctorNotificationSender emailAdapter = new EmailDoctorNotificationAdapter(emailService);

visionAccountAdapter.SendNotification("D123", "A new test has been assigned to you.");
emailAdapter.SendNotification("D123", "A new test has been assigned to you.");
```

### Мост / Bridge

**Назначение:**  
Разделяет абстракцию от ее реализации, чтобы они могли изменяться независимо.

**Пример:**  
Класс содержит объект интерфейса, но его можно подменить при использовании объекта класса.

**UML:**  
<img width="642" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Bridge.png?raw=true">
```PlantUML
@startuml
interface INotificationSender {
  + sendNotification(doctorId: string, message: string): void
}

class EmailSender implements INotificationSender {
  + sendNotification(doctorId: string, message: string): void
}

class VisionSender implements INotificationSender {
  + sendNotification(doctorId: string, message: string): void
}

class DoctorNotification {
  - sender: INotificationSender
  + constructor(sender: INotificationSender)
  + sendNotification(doctorId: string, message: string): void
}

DoctorNotification *-- INotificationSender

@enduml
```

**Код:**
```С#
// Интерфейс для отправки уведомлений врачам
public interface IDoctorNotificationSender
{
    void SendNotification(string doctorId, string message);
}

// Система для отправки email врачам
public class EmailDoctorNotificationService
{
    public void SendEmail(string doctorId, string message)
    {
        Console.WriteLine($"Sending Email to Doctor {doctorId}: {message}");
    }
}

// Адаптер для отправки уведомлений внутри системы Vision
public class VisionAccountAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public VisionAccountAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message); // Уведомление через систему Vision
    }
}

// Адаптер для отправки email врачам
public class EmailDoctorNotificationAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public EmailDoctorNotificationAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message);
    }
}

// Использование
var emailService = new EmailDoctorNotificationService();

IDoctorNotificationSender visionAccountAdapter = new VisionAccountAdapter(emailService); // Уведомления внутри Vision
IDoctorNotificationSender emailAdapter = new EmailDoctorNotificationAdapter(emailService);

visionAccountAdapter.SendNotification("D123", "A new test has been assigned to you.");
emailAdapter.SendNotification("D123", "A new test has been assigned to you.");
```

### Декоратор / Decorator

**Назначение:**  
Динамически добавляет объекту новые возможности. Является гибкой альтернативой наследованию для расширения функциональности.

**Пример:**  
Создаём набор классов-декораторов, которые оборачивают базовый объект, добавляя ему новые возможности. Каждый декоратор реализует тот же интерфейс, что и базовый объект, что позволяет им быть взаимозаменяемыми.

**UML:**  
<img width="876" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Decorator.png?raw=true">
```PlantUML
@startuml

interface IDoctorNotificationSender {
  + sendNotification(doctorId: string, message: string): void
}

class VisionSender {
  + sendNotification(doctorId: string, message: string): void
}

class NotificationDecorator {
  - decoratedSender: IDoctorNotificationSender
  + sendNotification(doctorId: string, message: string): void
}

class LoggingDecorator {
  + sendNotification(doctorId: string, message: string): void
}

class PriorityDecorator {
  + sendNotification(doctorId: string, message: string): void
}

IDoctorNotificationSender <|-- VisionSender
NotificationDecorator <|-- LoggingDecorator
NotificationDecorator <|-- PriorityDecorator
VisionSender --|> NotificationDecorator

@enduml

```

**Код:**
```С#
using System;

// Интерфейс для отправки уведомлений врачам
public interface IDoctorNotificationSender
{
    void SendNotification(string doctorId, string message);
}

// Система для отправки email врачам
public class VisionSender : IDoctorNotificationSender
{
    public void SendNotification(string doctorId, string message)
    {
        Console.WriteLine($"Sending notification to Doctor {doctorId}: {message}");
    }
}

// Базовый декоратор для уведомлений
public class NotificationDecorator : IDoctorNotificationSender
{
    private readonly IDoctorNotificationSender _decoratedSender;

    public NotificationDecorator(IDoctorNotificationSender decoratedSender)
    {
        _decoratedSender = decoratedSender;
    }

    public virtual void SendNotification(string doctorId, string message)
    {
        _decoratedSender.SendNotification(doctorId, message);
    }
}

// Декоратор для логирования уведомлений
public class LoggingDecorator : NotificationDecorator
{
    public LoggingDecorator(IDoctorNotificationSender decoratedSender) 
        : base(decoratedSender) { }

    public override void SendNotification(string doctorId, string message)
    {
        Console.WriteLine($"Logging: Sending notification to Doctor {doctorId} with message: {message}");
        base.SendNotification(doctorId, message);
    }
}

// Декоратор для добавления приоритетности уведомления
public class PriorityDecorator : NotificationDecorator
{
    public PriorityDecorator(IDoctorNotificationSender decoratedSender) 
        : base(decoratedSender) { }

    public override void SendNotification(string doctorId, string message)
    {
        message = "[Priority] " + message;
        base.SendNotification(doctorId, message);
    }
}

// Пример использования
var visionSender = new VisionSender();

// Декорируем с логированием
IDoctorNotificationSender notificationWithLogging = new LoggingDecorator(visionSender);
notificationWithLogging.SendNotification("D123", "A new test has been assigned to you.");

// Декорируем с приоритетом и логированием
IDoctorNotificationSender notificationWithPriorityAndLogging = new PriorityDecorator(notificationWithLogging);
notificationWithPriorityAndLogging.SendNotification("D123", "A new test has been assigned to you.");

```

### Компоновщик / Composite

**Назначение:**  
Компонует объекты в древовидные структуры для представления иерархий «часть — целое». Позволяет клиентам единообразно трактовать индивидуальные и составные объекты.

**Пример:** 
1. Создаём абстрактный класс, который будет служить базой как для отдельных объектов, так и для их составных компонентов. В нашем примере это класс `User`, который определяет общие методы, такие как `AssignTask`.
2. Создаём классы, представляющие листовые компоненты, такие как `Responsible`. Эти классы реализуют все абстрактные методы из базового класса и представляют собой самостоятельные объекты.
3. Создаём классы, которые могут содержать как отдельные объекты `User`, так и их композиции, например, класс `UserGroup`. Этот класс может содержать несколько объектов `User` или их композиции.
4. Все компоненты, будь то отдельные объекты или их композиции, могут быть обработаны единообразно через общий интерфейс или базовый класс. Это позволяет манипулировать как простыми, так и составными объектами без необходимости менять логику обработки.



**UML:**  
<img width="539" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Composite.png?raw=true">
```PlantUML
@startuml

abstract class User {
  + assignTask(task: string): void
}

class Responsible {
  + assignTask(task: string): void
}

class UserGroup {
  - users: List<User>
  + addUser(user: User): void
  + assignTask(task: string): void
}

User <|-- Responsible
User <|-- UserGroup

@enduml

```

**Код:**
```C#
using System;
using System.Collections.Generic;

// Абстрактный класс для пользователей
public abstract class User
{
    public abstract void AssignTask(string task);
}

// Конкретный класс для ответственного пользователя
public class Responsible : User
{
    public override void AssignTask(string task)
    {
        Console.WriteLine($"Ответственный пользователь назначает задачу: {task}");
    }
}

// Класс для группы пользователей
public class UserGroup : User
{
    private List<User> users = new List<User>();

    // Добавление пользователя в группу
    public void AddUser(User user)
    {
        users.Add(user);
    }

    // Назначение задачи всем пользователям в группе
    public override void AssignTask(string task)
    {
        foreach (var user in users)
        {
            user.AssignTask(task);
        }
    }
}

// Пример использования
public class Program
{
    public static void Main()
    {
        // Создание ответственного пользователя
        var responsibleUser = new Responsible();

        // Создание группы пользователей
        var userGroup = new UserGroup();

        // Добавление ответственного пользователя в группу
        userGroup.AddUser(responsibleUser);

        // Назначение задачи группе пользователей
        userGroup.AssignTask("Задача для группы");

        // Назначение задачи отдельному ответственному пользователю
        responsibleUser.AssignTask("Задача для одного пользователя");
    }
}

```


## Поведенческие шаблоны

### Стратегия / Strategy

**Назначение:**  
Определяет семейство алгоритмов, инкапсулирует каждый из них и делает их взаимозаменяемыми. Стратегия позволяет изменять алгоритмы независимо от клиентов, которые ими пользуются.

**Пример:**  
Клиент выполняет одно действие, но с разными классами одного интерфейса

**UML:**  
<img width="1206" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Strategy.png?raw=true">
```PlantUML
@startuml

interface AssignmentStrategy {
    + assign(): void
}

class ManualAssignmentStrategy {
    + assign(): void
}

class RuleBasedAssignmentStrategy {
    + assign(): void
}

class Assigner {
    - assignmentStrategy: AssignmentStrategy
    + setAssignmentStrategy(strategy: AssignmentStrategy): void
    + assign(): void
}

Assigner --|> AssignmentStrategy
Assigner *-down-> ManualAssignmentStrategy: strategy
Assigner *-down-> RuleBasedAssignmentStrategy: strategy

@enduml

```

**Код:**
```С#
using System;

// Интерфейс стратегии назначения
public interface IAssignmentStrategy
{
    void Assign();
}

// Стратегия ручного назначения
public class ManualAssignmentStrategy : IAssignmentStrategy
{
    public void Assign()
    {
        Console.WriteLine("Используется стратегия ручного назначения.");
    }
}

// Стратегия назначения по правилам
public class RuleBasedAssignmentStrategy : IAssignmentStrategy
{
    public void Assign()
    {
        Console.WriteLine("Используется стратегия назначения по правилам.");
    }
}

// Стратегия автоматического назначения
public class AutomaticAssignmentStrategy : IAssignmentStrategy
{
    public void Assign()
    {
        Console.WriteLine("Используется стратегия автоматического назначения.");
    }
}

// Класс назначающего объекта
public class Assigner
{
    private IAssignmentStrategy _assignmentStrategy;

    public Assigner(IAssignmentStrategy assignmentStrategy)
    {
        _assignmentStrategy = assignmentStrategy;
    }

    // Установка новой стратегии назначения
    public void SetAssignmentStrategy(IAssignmentStrategy strategy)
    {
        _assignmentStrategy = strategy;
    }

    // Выполнение назначения с текущей стратегией
    public void Assign()
    {
        _assignmentStrategy.Assign();
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        // Создаем стратегии
        IAssignmentStrategy manualStrategy = new ManualAssignmentStrategy();
        IAssignmentStrategy ruleBasedStrategy = new RuleBasedAssignmentStrategy();
        IAssignmentStrategy automaticStrategy = new AutomaticAssignmentStrategy();

        // Создаем объект с начальной стратегией (например, ручной)
        Assigner assigner = new Assigner(manualStrategy);

        // Выполняем назначение с текущей стратегией
        assigner.Assign();

        // Меняем стратегию на назначение по правилам
        assigner.SetAssignmentStrategy(ruleBasedStrategy);
        assigner.Assign();

        // Меняем стратегию на автоматическое назначение
        assigner.SetAssignmentStrategy(automaticStrategy);
        assigner.Assign();
    }
}
```

### Наблюдатель / Observer

**Назначение:**  
Определяет зависимость "один ко многим" между объектами так, что при изменении состояния одного объекта все зависящие от него оповещаются и обновляются автоматически.

**Пример:**  
1. Есть два класса: `AssignmentObserver` и `AssignmentSubject`.
   - Класс `AssignmentObserver` имеет метод `updateNotification`, который вызывается для получения обновлений.
   - Класс `AssignmentSubject` содержит список наблюдателей и методы для добавления, удаления и уведомления наблюдателей.
2. Когда вызывается метод уведомления, у всех наблюдателей вызывается метод `updateNotification`, чтобы оповестить их об изменении.

**UML:**  
<img width="770" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Observer.png?raw=true">
```PlantUML
@startuml
class AssignmentObserver {
    + updateNotification(task: string, newAssignee: string): void
}

class AssignmentSubject {
    - observers: List<AssignmentObserver>
    + addObserver(observer: AssignmentObserver): void
    + removeObserver(observer: AssignmentObserver): void
    + notifyObservers(task: string, newAssignee: string): void
}

AssignmentObserver --|> AssignmentSubject
@enduml
```

**Код:**
```С#
using System;
using System.Collections.Generic;

// Интерфейс наблюдателя для получения уведомлений
public interface IAssignmentObserver
{
    void UpdateNotification(string task, string newAssignee);
}

// Класс субъекта, который уведомляет наблюдателей об изменении задания
public class AssignmentSubject
{
    private List<IAssignmentObserver> observers = new List<IAssignmentObserver>();

    // Добавить наблюдателя
    public void AddObserver(IAssignmentObserver observer)
    {
        observers.Add(observer);
    }

    // Удалить наблюдателя
    public void RemoveObserver(IAssignmentObserver observer)
    {
        observers.Remove(observer);
    }

    // Уведомить всех наблюдателей об изменении задания
    public void NotifyObservers(string task, string newAssignee)
    {
        foreach (var observer in observers)
        {
            observer.UpdateNotification(task, newAssignee);
        }
    }
}

// Наблюдатель, который обрабатывает уведомления
public class NotificationSystem : IAssignmentObserver
{
    private string name;

    public NotificationSystem(string name)
    {
        this.name = name;
    }

    // Метод для обработки уведомлений о назначении
    public void UpdateNotification(string task, string newAssignee)
    {
        Console.WriteLine($"Система уведомлений: Задача '{task}' теперь назначена {newAssignee}");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        // Создаем объект субъекта
        AssignmentSubject assignmentSubject = new AssignmentSubject();

        // Создаем и добавляем наблюдателей (системы уведомлений)
        NotificationSystem notificationSystem1 = new NotificationSystem("Система 1");
        NotificationSystem notificationSystem2 = new NotificationSystem("Система 2");

        assignmentSubject.AddObserver(notificationSystem1);
        assignmentSubject.AddObserver(notificationSystem2);

        // Изменяем назначение задачи
        assignmentSubject.NotifyObservers("Задача 1", "Исполнитель 1");

        // Результат в консоли:
        // Система уведомлений: Задача 'Задача 1' теперь назначена Исполнитель 1
        // Система уведомлений: Задача 'Задача 1' теперь назначена Исполнитель 1

        // Изменяем назначение задачи
        assignmentSubject.NotifyObservers("Задача 1", "Исполнитель 2");

        // Результат в консоли:
        // Система уведомлений: Задача 'Задача 1' теперь назначена Исполнитель 2
        // Система уведомлений: Задача 'Задача 1' теперь назначена Исполнитель 2
    }
}
```

### Состояние / State

**Назначение:**  
Позволяет объекту варьировать свое поведение в зависимости от внутреннего состояния. Извне создается впечатление, что изменился класс объекта. 

**Пример:**  
Объект делегирует выполнение своих операций текущему объекту-состоянию.

**UML:**  
<img width="770" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/State.png?raw=true">
```PlantUML
@startuml

class AssignmentState {
  {abstract} +render(task: string, assignees: List<string>): string
}

class SingleAssignmentState {
  +render(task: string, assignees: List<string>): string
}

class MultipleAssignmentState {
  +render(task: string, assignees: List<string>): string
}

class AssignmentStateContext {
  -state: AssignmentState
  +init(initialState: AssignmentState)
  +setState(newState: AssignmentState)
  +requestState(task: string, assignees: List<string>): string
}

AssignmentState <|-- SingleAssignmentState
AssignmentState <|-- MultipleAssignmentState
AssignmentStateContext *-- AssignmentState

@enduml
```

**Код:**
```React
import React, { useState } from 'react';

// Абстрактное состояние назначения
class AssignmentState {
  render(task, assignees) {
    throw new Error("Метод render должен быть реализован в дочернем классе");
  }
}

// Состояние для одного назначения
class SingleAssignmentState extends AssignmentState {
  render(task, assignees) {
    return `${task} назначено на: ${assignees[0]}`;
  }
}

// Состояние для нескольких назначений
class MultipleAssignmentState extends AssignmentState {
  render(task, assignees) {
    return `${task} назначено на: ${assignees.join(", ")}`;
  }
}

// Контекст для управления состоянием назначения
const AssignmentComponent = () => {
  const [state, setState] = useState(new SingleAssignmentState());
  const [task, setTask] = useState("");
  const [assignees, setAssignees] = useState("");
  const [message, setMessage] = useState("");

  const handleAssign = () => {
    const assigneeList = assignees.split(",").map((a) => a.trim());
    setMessage(state.render(task, assigneeList));
  };

  return (
    <div>
      <input value={task} onChange={(e) => setTask(e.target.value)} placeholder="Задача" />
      <input value={assignees} onChange={(e) => setAssignees(e.target.value)} placeholder="Исполнители" />
      <label>
        <input type="radio" onChange={() => setState(new SingleAssignmentState())} /> Одиночное назначение
      </label>
      <label>
        <input type="radio" onChange={() => setState(new MultipleAssignmentState())} /> Множественное назначение
      </label>
      <button onClick={handleAssign}>Назначить</button>
      {message && <p>{message}</p>}
    </div>
  );
};

export default AssignmentComponent;
```

### Команда / Command

**Назначение:**  
Инкапсулирует запрос как объект, позволяя тем самым задавать параметры клиентов для обработки соответствующих запросов, ставить запросы в очередь или протоколировать их, а также поддерживать отмену операций.

**Пример:**  
Создаём класс-метод, будет выполнять одну операцию - реализация интерфейса.

**UML:**  
<img width="791" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/Command.png?raw=true">
```PlantUML
@startuml

class Sample {
    + assignResponsible()
    + setPathologyFlag()
}

interface SampleCommand {
    + execute()
}

class AssignResponsibleCommand {
    - sample: Sample
    + AssignResponsibleCommand(sample: Sample)
    + execute()
}

class SetPathologyFlagCommand {
    - sample: Sample
    + SetPathologyFlagCommand(sample: Sample)
    + execute()
}

SampleCommand <|.. AssignResponsibleCommand
SampleCommand <|.. SetPathologyFlagCommand

Sample --> SampleCommand
AssignResponsibleCommand --> Sample
SetPathologyFlagCommand --> Sample

@enduml
```

**Код:**
```С#
using System;

// Класс Sample
public class Sample
{
    private string responsiblePerson;
    private bool pathologyFlag;

    public void AssignResponsible(string responsible)
    {
        responsiblePerson = responsible;
        Console.WriteLine($"Ответственный за пробу назначен: {responsiblePerson}");
    }

    public void SetPathologyFlag(bool flag)
    {
        pathologyFlag = flag;
        Console.WriteLine($"Флаг патологии установлен: {pathologyFlag}");
    }
}

// Интерфейс команды
public interface ISampleCommand
{
    void Execute();
}

// Команда для назначения ответственного
public class AssignResponsibleCommand : ISampleCommand
{
    private Sample sample;
    private string responsiblePerson;

    public AssignResponsibleCommand(Sample sample, string responsible)
    {
        this.sample = sample;
        this.responsiblePerson = responsible;
    }

    public void Execute()
    {
        sample.AssignResponsible(responsiblePerson);
    }
}

// Команда для установки флага патологии
public class SetPathologyFlagCommand : ISampleCommand
{
    private Sample sample;
    private bool flag;

    public SetPathologyFlagCommand(Sample sample, bool flag)
    {
        this.sample = sample;
        this.flag = flag;
    }

    public void Execute()
    {
        sample.SetPathologyFlag(flag);
    }
}

// Пример использования
public class Program
{
    public static void Main()
    {
        // Создаем объект Sample
        Sample sample = new Sample();

        // Создаем команды
        ISampleCommand assignResponsibleCommand = new AssignResponsibleCommand(sample, "Иванов");
        ISampleCommand setPathologyFlagCommand = new SetPathologyFlagCommand(sample, true);

        // Выполняем команды
        assignResponsibleCommand.Execute();
        setPathologyFlagCommand.Execute();
    }
}
```

### Шаблонный метод / Template Method

**Назначение:**  
Шаблонный метод определяет основу алгоритма и позволяет подклассам переопределять некоторые шаги алгоритма, не изменяя его структуры в целом.

**Пример:**  
1. Определяем родительский класс `RepositoryTemplate`, содержащий "шаблонный метод" `PerformOperations` и методы `AssignResponsible`, `ViewSamples` и `ApproveSample`, которые выполняются в "шаблонном методе".
2. Создаём конкретные классы, реализующие методы из `RepositoryTemplate` - `RootRepository` и `RegularRepository`.
3. Шаблонный метод нельзя переопределить.


**UML:**  
<img width="791" alt="image" src="https://github.com/orlovaska/HSE-SoftwareArchitectureLabs/blob/LabWork6/LabWork%20%E2%84%966/docs/Diagrams/TemplateMethod.png?raw=true">
```PlantUML
@startuml

abstract class RepositoryTemplate {
    +performOperations()
    #assignResponsible()
    #viewSamples()
    #approveSample()
}

class RootRepository {
    +assignResponsible()
    +viewSamples()
    +approveSample()
}

class RegularRepository {
    +assignResponsible()
    +viewSamples()
    +approveSample()
}

RepositoryTemplate <|-- RootRepository
RepositoryTemplate <|-- RegularRepository

RootRepository --> RepositoryTemplate
RegularRepository --> RepositoryTemplate

@enduml

```

**Код:**
```С#
using System;

// Абстрактный класс, определяющий шаблон репозитория
public abstract class RepositoryTemplate
{
    public void PerformOperations()
    {
        AssignResponsible();
        ViewSamples();
        ApproveSample();
    }

    protected abstract void AssignResponsible();
    protected abstract void ViewSamples();
    protected abstract void ApproveSample();
}

// Конкретный класс для корневого репозитория
public class RootRepository : RepositoryTemplate
{
    protected override void AssignResponsible()
    {
        Console.WriteLine("Назначение ответственного в RootRepository.");
    }

    protected override void ViewSamples()
    {
        Console.WriteLine("Просмотр проб в RootRepository.");
    }

    protected override void ApproveSample()
    {
        Console.WriteLine("Утверждение пробы в RootRepository.");
    }
}

// Конкретный класс для обычного репозитория
public class RegularRepository : RepositoryTemplate
{
    protected override void AssignResponsible()
    {
        Console.WriteLine("Назначение ответственного в RegularRepository.");
    }

    protected override void ViewSamples()
    {
        Console.WriteLine("Просмотр проб в RegularRepository.");
    }

    protected override void ApproveSample()
    {
        Console.WriteLine("Утверждение пробы в RegularRepository.");
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        RepositoryTemplate rootRepo = new RootRepository();
        rootRepo.PerformOperations();

        Console.WriteLine();

        RepositoryTemplate regularRepo = new RegularRepository();
        regularRepo.PerformOperations();
    }
}
```




# Шаблоны проектирования GRASP

## Роли

### Information Expert

**Проблема:**  
Информация в системе должна обрабатываться, аккумулироваться и рассчитываться.

**Решение:**  
Назначить соответствующие обязанности тому классу, который её содержит.

**Пример:**  
1. Есть класс `SampleManagementSystem`, который управляет всей информацией о пробах, их назначениях и ответственных.
2. В классе используется паттерн **Singleton** для обеспечения единственного экземпляра класса и централизованного управления пробами и операциями с ними.
3. Метод `Instance` используется для получения единственного экземпляра класса, если экземпляр еще не был создан. Все операции, такие как назначение ответственного или просмотр проб, выполняются через этот экземпляр.


**Код:** при проектировании синглтона
```C#
using System;

public class SampleManagementSystem
{
    private static SampleManagementSystem _instance;
    private static object theLock = new object();

    private SampleManagementSystem() 
    {
        // Инициализационная логика
    }

    public static SampleManagementSystem Instance
    {
        get
        {
            lock (theLock)
            {
                if (_instance == null)
                {
                    _instance = new SampleManagementSystem();
                }
                return _instance;
            }
        }
    }

    public void AssignResponsible(string sampleId, string responsiblePerson)
    {
        Console.WriteLine($"Assigning {responsiblePerson} to sample {sampleId}");
    }

    public void ViewSampleDetails(string sampleId)
    {
        Console.WriteLine($"Viewing details for sample {sampleId}");
    }
}
// Пример использования
var system1 = SampleManagementSystem.Instance;
var system2 = SampleManagementSystem.Instance;

Console.WriteLine(system1 == system2);  // true

system1.AssignResponsible("Sample001", "John Doe");
system2.ViewSampleDetails("Sample001")
```
**Результаты:** cоблюдается принцип инкапсуляции, уменьшается связанность классов

**Связь с другими паттернами:** Singleton


### Creator
**Проблема:**  
Экземпляры класса необходимо создавать.

**Решение:**  
Назначить обязанности инстанциирования тому классу, который будет использовать соответствующие экземпляры созданных классов.

**Пример:**  
1. Вводятся два протокола: `BloodTest` и `BiopsyTest`, создаются конкретные классы, реализующие эти протоколы. В данном случае, `BasicBloodTest` и `AdvancedBloodTest` — это два варианта анализа крови, а `BasicBiopsyTest` и `AdvancedBiopsyTest` — два варианта биопсии. Каждый из этих классов реализует соответствующие методы протоколов.
2. Создается протокол `LabTestFactory`, который предоставляет интерфейс для создания связанных объектов анализов крови и биопсий. Создаются две конкретные реализации абстрактной фабрики: `BasicLabTestFactory` и `AdvancedLabTestFactory`. Каждая из этих фабрик реализует методы `CreateBloodTest` и `CreateBiopsyTest`, возвращая соответствующие объекты анализов крови и биопсий.
3. Есть класс `Lab`, который не содержит конкретных объектов анализов крови и биопсий (может быть любая реализация) и объекта фабрики (может быть любая). В зависимости от использования класса клиента, будет определяться, какие именно анализы крови и биопсии будут проведены.


**Код:** при реализации абстрактной фабрики как раз создавали объекты
```C#
using System;

// Интерфейсы для тестов
public interface BloodTest
{
    void Run();
}

public interface BiopsyTest
{
    void Run();
}

// Конкретные классы тестов
public class BasicBloodTest : BloodTest
{
    public void Run()
    {
        Console.WriteLine("Running basic blood test.");
    }
}

public class AdvancedBloodTest : BloodTest
{
    public void Run()
    {
        Console.WriteLine("Running advanced blood test.");
    }
}

public class BasicBiopsyTest : BiopsyTest
{
    public void Run()
    {
        Console.WriteLine("Performing basic biopsy test.");
    }
}

public class AdvancedBiopsyTest : BiopsyTest
{
    public void Run()
    {
        Console.WriteLine("Performing advanced biopsy test.");
    }
}

// Абстрактная фабрика
public interface LabTestFactory
{
    BloodTest CreateBloodTest();
    BiopsyTest CreateBiopsyTest();
}

// Конкретные фабрики
public class BasicLabTestFactory : LabTestFactory
{
    public BloodTest CreateBloodTest()
    {
        return new BasicBloodTest();
    }

    public BiopsyTest CreateBiopsyTest()
    {
        return new BasicBiopsyTest();
    }
}

public class AdvancedLabTestFactory : LabTestFactory
{
    public BloodTest CreateBloodTest()
    {
        return new AdvancedBloodTest();
    }

    public BiopsyTest CreateBiopsyTest()
    {
        return new AdvancedBiopsyTest();
    }
}

// Клиентский класс
public class Lab
{
    private BloodTest _bloodTest;
    private BiopsyTest _biopsyTest;

    public Lab(LabTestFactory factory)
    {
        _bloodTest = factory.CreateBloodTest();
        _biopsyTest = factory.CreateBiopsyTest();
    }

    public void PerformTests()
    {
        _bloodTest.Run();
        _biopsyTest.Run();
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        LabTestFactory basicFactory = new BasicLabTestFactory();
        LabTestFactory advancedFactory = new AdvancedLabTestFactory();

        Lab basicLab = new Lab(basicFactory);
        basicLab.PerformTests();

        Lab advancedLab = new Lab(advancedFactory);
        advancedLab.PerformTests();
    }
}
```
**Результаты:** соблюдается принцип инкапсуляции, уменьшается связанность классов

**Связь с другими паттернами:** антипаттерн Poltergeist , Abstract Factory


### Controller

**Проблема** Необходимо реализовать метод, который будет возвращать все записи (продукты, элементы и т.д.) по указанному `id` из репозитория.

**Решение** Реализовать метод, который будет обрабатывать запрос на получение всех объектов по определённому `id` из репозитория. Метод будет обращаться к базе данных или коллекции и возвращать данные.

**Код** Был написан новый код
```C#
[HttpGet("repository/{id}")]
public IActionResult GetByRepositoryId(int id)
{
    var items = _repository.GetItemsById(id); // предположим, что _repository это ваш репозиторий
    if (items == null || !items.Any())
    {
        return NotFound($"Items with repository ID {id} not found.");
    }
    return Ok(items);
}

```

**Результаты:** обрабатываются http запросы с помощью контроллера
**Связь с другими паттернами:** Observer


### Pure Fabrication

Необходимо обеспечить Low Coupling и High Cohesion в архитектуре приложения, разделив бизнес-логику и логику взаимодействия с внешними источниками данных.

**Решение**

Создать искусственную сущность (например, сервис), которая будет обеспечивать взаимодействие с репозиториями или базами данных, обеспечивая разделение ответственности и улучшение структурности кода. Это позволяет добиться Low Coupling и High Cohesion.

**Код**

```C#
// Пример Pure Fabrication - синтезированная сущность для работы с репозиторием
public class RepositoryUtility
{
    private readonly IRepository _repository;

    public RepositoryUtility(IRepository repository)
    {
        _repository = repository;
    }

    // Метод для получения всех записей по id
    public IEnumerable<Item> GetItemsById(int id)
    {
        return _repository.GetItemsById(id);
    }

    // Метод для добавления нового элемента
    public void AddItem(Item item)
    {
        _repository.Add(item);
    }
}

// Пример использования Pure Fabrication
var repositoryUtility = new RepositoryUtility(new ItemRepository());
var items = repositoryUtility.GetItemsById(5);
repositoryUtility.AddItem(new Item { Id = 6, Name = "New Item" });
```

**Результаты:** бизнес-логика не смешивается с логикой приложения, на уровне архитектуры
лучше поддерживаются Low Coupling и High Cohesion

**Связь с другими паттернами:** Facade, Strategy



### Indirection

**Проблема:** Необходимо распределить обязанности объектов, избежав прямого связывания, чтобы упростить заменяемость и модификацию компонентов системы.

**Решение:** Использование паттерна Indirection предполагает добавление промежуточного объекта, который управляет связью между компонентами.

**Код:** В примере адаптера используется промежуточный объект для связи между системой отправки уведомлений и конкретной реализацией отправки email.

```C#
// Интерфейс для отправки уведомлений врачам
public interface IDoctorNotificationSender
{
    void SendNotification(string doctorId, string message);
}

// Система для отправки email врачам
public class EmailDoctorNotificationService
{
    public void SendEmail(string doctorId, string message)
    {
        Console.WriteLine($"Sending Email to Doctor {doctorId}: {message}");
    }
}

// Адаптер для отправки уведомлений внутри системы Vision
public class VisionAccountAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public VisionAccountAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message); // Уведомление через систему Vision
    }
}

// Адаптер для отправки email врачам
public class EmailDoctorNotificationAdapter : IDoctorNotificationSender
{
    private readonly EmailDoctorNotificationService _emailService;

    public EmailDoctorNotificationAdapter(EmailDoctorNotificationService emailService)
    {
        _emailService = emailService;
    }

    public void SendNotification(string doctorId, string message)
    {
        _emailService.SendEmail(doctorId, message);
    }
}

// Использование
var emailService = new EmailDoctorNotificationService();

IDoctorNotificationSender visionAccountAdapter = new VisionAccountAdapter(emailService); // Уведомления внутри Vision
IDoctorNotificationSender emailAdapter = new EmailDoctorNotificationAdapter(emailService);

visionAccountAdapter.SendNotification("D123", "A new test has been assigned to you.");
emailAdapter.SendNotification("D123", "A new test has been assigned to you.");
```

**Результаты:** заменяемость частей системы, возможность их повторного использования

**Связь с другими паттернами:** похож на Dependency Inversion Principleв SOLID, Adapter

## Принципы разработки

### High Cohesion

**Проблема:** необходимо обеспечивать выполнение объектами разнородных функций

**Решение:** распределить обязанность так, чтобы обеспечить высокое зацепление – содержание однородной бизнес-логики

**Код:** код для паттерна Pure Fabrication, который выполняет как раз этот принцип
```C#
public class RepositoryUtility
{
    private readonly IRepository _repository;

    public RepositoryUtility(IRepository repository)
    {
        _repository = repository;
    }

    // Метод для получения всех записей по id
    public IEnumerable<Item> GetItemsById(int id)
    {
        return _repository.GetItemsById(id);
    }

    // Метод для добавления нового элемента
    public void AddItem(Item item)
    {
        _repository.Add(item);
    }
}

// Пример использования Pure Fabrication
var repositoryUtility = new RepositoryUtility(new ItemRepository());
var items = repositoryUtility.GetItemsById(5);
repositoryUtility.AddItem(new Item { Id = 6, Name = "New Item" });
```

**Результаты:** несколько контекстов смешивается в одном классе, классы более легко поддерживать

**Связь с другими паттернами:** похож на Single Responsibility Principleв SOLID, Pure Fabrication

### Polymorphism

### Проблема:
Необходимо обрабатывать различные варианты поведения на основании типа, допуская замену частей системы, например, работы с репозиториями, не влияя на общую структуру программы.

### Решение:
Использовать шаблонный метод для определения основного алгоритма работы с репозиториями, позволяя каждому типу репозитория (например, корневому и обычному) изменять шаги алгоритма, не изменяя его структуры в целом.

### Код:
В шаблонном методе мы создаём основу алгоритма работы с репозиториями и даём возможность каждому типу репозитория подменить только определённые шаги (например, назначение ответственного, просмотр проб и утверждение проб).
```C#
public abstract class RepositoryTemplate
{
    // Шаблонный метод, который задаёт структуру алгоритма
    public void PerformOperations()
    {
        AssignResponsible();  // Шаг 1: Назначение ответственного
        ViewSamples();        // Шаг 2: Просмотр проб
        ApproveSample();      // Шаг 3: Утверждение проб
    }

    // Абстрактные методы, которые должны быть реализованы в подклассах
    protected abstract void AssignResponsible();
    protected abstract void ViewSamples();
    protected abstract void ApproveSample();
}

// Конкретный класс для корневого репозитория
public class RootRepository : RepositoryTemplate
{
    protected override void AssignResponsible()
    {
        Console.WriteLine("Назначение ответственного в RootRepository.");
    }

    protected override void ViewSamples()
    {
        Console.WriteLine("Просмотр проб в RootRepository.");
    }

    protected override void ApproveSample()
    {
        Console.WriteLine("Утверждение пробы в RootRepository.");
    }
}

// Конкретный класс для обычного репозитория
public class RegularRepository : RepositoryTemplate
{
    protected override void AssignResponsible()
    {
        Console.WriteLine("Назначение ответственного в RegularRepository.");
    }

    protected override void ViewSamples()
    {
        Console.WriteLine("Просмотр проб в RegularRepository.");
    }

    protected override void ApproveSample()
    {
        Console.WriteLine("Утверждение пробы в RegularRepository.");
    }
}

// Пример использования
class Program
{
    static void Main()
    {
        RepositoryTemplate rootRepo = new RootRepository();
        rootRepo.PerformOperations();  // Выполнение операций для RootRepository

        Console.WriteLine();

        RepositoryTemplate regularRepo = new RegularRepository();
        regularRepo.PerformOperations();  // Выполнение операций для RegularRepository
    }
}
```

**Результаты:** Подключаемые компоненты системы будут заменяемыми

**Связь с другими паттернами:** Adapter, Factory, Template Method



## Свойство программы

### Protected Variations

**Проблема:** необходимо спроектировать систему так, чтобы изменение одних её элементов, таких как данные репозитория, не влияло на другие части программы.

**Решение:** использовать синглтон для централизованного управления данными репозитория, так чтобы изменение информации о репозитории в одном месте автоматически обновляло её везде, где используется синглтон.

**Код:** при изменении информации о репозитории достаточно изменить данные в синглтоне, новая информация автоматически станет доступна во всех частях программы, где используется этот экземпляр.
```C#
public class RepositorySingleton
{
    private static RepositorySingleton _Instance;
    private static object theLock = new object();

    private List<Repository> repositories;

    // Приватный конструктор
    private RepositorySingleton()
    {
        repositories = new List<Repository>
        {
            new Repository { Id = 1, Name = "Repo 1" },
            new Repository { Id = 2, Name = "Repo 2" }
        };
    }

    // Глобальная точка доступа
    public static RepositorySingleton Instance
    {
        get
        {
            lock (theLock)
            {
                if (_Instance == null)
                {
                    _Instance = new RepositorySingleton();
                }
                return _Instance;
            }
        }
    }

    // Метод для получения репозитория по ID
    public Repository GetRepositoryById(int id)
    {
        return repositories.FirstOrDefault(r => r.Id == id);
    }
}

public class Repository
{
    public int Id { get; set; }
    public string Name { get; set; }
}

// Пример использования
var repositoryService = RepositorySingleton.Instance;
var repository = repositoryService.GetRepositoryById(1);
Console.WriteLine($"Repository: {repository?.Name}");
```

**Результаты:** внося изменения в одни части программы нет необходимости изменять другие части программы

**Связь с другими паттернами:** похож на Open Closed Principleв SOLID, Singleton
