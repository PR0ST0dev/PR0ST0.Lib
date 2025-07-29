# ❓ Часто задаваемые вопросы (FAQ)

## Как зарегистрировать Frame для навигации?
```csharp
Navigation.RegisterFrame("Main", myFrame);
```

## Как использовать NavigateForResult?
```csharp
var result = await Navigation.NavigateForResult<ModalViewModel>("Main");
```

## Как сделать повторный HTTP-запрос?
```csharp
builder.WithRetry(3).WithTimeout(5000);
```

## Где хранится состояние?
В реализации `IStorage` — по умолчанию `InMemoryStorage`, можно заменить.
