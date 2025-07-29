
# 📘 PR0ST0.Mediator — минималистичная и мощная Mediator-библиотека для .NET Framework 4.8

> Простой и расширяемый `Mediator` с поддержкой **Command**, **Request/Response**, **Event**, **Pipeline** и **IoC-Resolver'а**.

---

## 🚀 Быстрый старт

### 1. Установка

Добавьте ссылку на проект `PR0ST0.Mediator`. Минимальные зависимости — только `System.*`.

---

### 2. Интерфейсы

```csharp
// Команда (без возврата результата)
public interface ICommand { }

// Запрос (с возвратом результата)
public interface IRequest<TResponse> { }

// Уведомление (событие, может быть много обработчиков)
public interface INotification { }
```

---

### 3. Обработчики

```csharp
public interface ICommandHandler<TCommand> where TCommand : ICommand
{
    Task HandleAsync(TCommand command);
}

public interface IRequestHandler<TRequest, TResponse> where TRequest : IRequest<TResponse>
{
    Task<TResponse> HandleAsync(TRequest request);
}

public interface INotificationHandler<TNotification> where TNotification : INotification
{
    Task HandleAsync(TNotification notification);
}
```

---

## 📦 Пример использования

### 📌 1. Создаём запрос

```csharp
public class PingRequest : IRequest<string>
{
    public string Message { get; set; }
}
```

### 📌 2. Обработчик

```csharp
public class PingRequestHandler : IRequestHandler<PingRequest, string>
{
    public Task<string> HandleAsync(PingRequest request)
    {
        return Task.FromResult($"Ответ на: {request.Message}");
    }
}
```

---

## ⚙️ Настройка резолвера

```csharp
var resolver = new SimpleResolver();

resolver.Register<IRequestHandler<PingRequest, string>>(() => new PingRequestHandler());

var mediator = new Mediator(resolver);
```

---

## 🧠 Вызов запроса

```csharp
var result = await mediator.SendAsync(new PingRequest { Message = "Ping" });
// result == "Ответ на: Ping"
```

---

## 📣 Отправка команды

```csharp
public class SaveCommand : ICommand { /* данные */ }

public class SaveHandler : ICommandHandler<SaveCommand>
{
    public Task HandleAsync(SaveCommand cmd) { /* сохранить */ return Task.CompletedTask; }
}

// Регистрация:
resolver.Register<ICommandHandler<SaveCommand>>(() => new SaveHandler());

// Использование:
await mediator.SendAsync(new SaveCommand());
```

---

## 📢 Публикация события (уведомления)

```csharp
public class UserCreatedEvent : INotification { public string Name { get; set; } }

public class WelcomeEmailHandler : INotificationHandler<UserCreatedEvent>
{
    public Task HandleAsync(UserCreatedEvent evt) => SendEmail(evt.Name);
}

public class LogHandler : INotificationHandler<UserCreatedEvent>
{
    public Task HandleAsync(UserCreatedEvent evt) => Log(evt.Name);
}

// Регистрация:
resolver.RegisterMany<INotificationHandler<UserCreatedEvent>>(() => new WelcomeEmailHandler());
resolver.RegisterMany<INotificationHandler<UserCreatedEvent>>(() => new LogHandler());

// Использование:
await mediator.PublishAsync(new UserCreatedEvent { Name = "Oleg" });
```

---

## 🛠️ Поведения (Pipeline)

```csharp
public class LoggingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
{
    public async Task<TResponse> HandleAsync(TRequest request, RequestHandlerDelegate<TResponse> next)
    {
        Console.WriteLine("Запрос: " + typeof(TRequest).Name);
        var response = await next();
        Console.WriteLine("Ответ: " + typeof(TResponse).Name);
        return response;
    }
}
```

```csharp
var behaviors = new object[]
{
    new LoggingBehavior<IRequest<string>, string>()
};

var mediator = new Mediator(resolver, behaviors);
```

---

## 🧱 Архитектура

```
UI ➜ Mediator ➜ RequestHandler / CommandHandler / NotificationHandler
                ⤷ PipelineBehavior (опционально)
                ⤷ DI через IServiceResolver
```

---

## ✅ Почему стоит использовать

- ✅ Простая реализация — идеально для .NET Framework
- ✅ Без лишних зависимостей
- ✅ Поддержка расширения через `IPipelineBehavior`
- ✅ Встроенный простой `DI`-резолвер (`SimpleResolver`)

---

## 📁 Пример проекта

- `DemoRequest.cs`, `DemoCommand.cs`, `DemoNotification.cs` — сущности
- `DemoRequestHandler`, `DemoCommandHandler`, `DemoNotificationHandler` — обработчики
- `MainWindow.xaml.cs` — взаимодействие с `Mediator`
- `Bootstrapper.cs` — регистрация
