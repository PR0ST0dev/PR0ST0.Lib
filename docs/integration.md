
# PR0ST0.Integration

Инфраструктурная библиотека для интеграции всех библиотек PR0ST0.

## Поддерживаемые модули:
- Mediator
- Validation
- Http
- Storage
- MVVM

## Пример использования:

```csharp
var container = IntegrationBuilder
    .Create()
    .UseMediator()
    .UseValidation()
    .UseHttp()
    .UseStorage()
    .UseMVVM()
    .Build();

var mediator = container.Resolve<IMediator>();
var validator = container.Resolve<IValidator<UserModel>>();
```
