# PR0ST0.Validation

Легковесная и декларативная библиотека для валидации моделей в .NET, совместимая с WPF, WinForms и другими приложениями на .NET Framework 4.8+.

---

## 📦 Возможности

- Простое API для описания правил валидации.
- Расширяемые fluent-методы (`NotEmpty()`, `MinLength()`, `GreaterThan()` и др.).
- Отчёт об ошибках по полям.
- Поддержка кастомных валидаторов и правил.

---

## 🚀 Быстрый старт

### 1. Создай модель

```csharp
public class UserModel
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

---

### 2. Создай валидатор

```csharp
public class UserModelValidator : Validator<UserModel>
{
    public UserModelValidator()
    {
        RuleFor("Name", x => x.Name)
            .NotEmpty()
            .MinLength(3);

        RuleFor("Age", x => x.Age)
            .GreaterThan(17)
            .LessThanOrEqualTo(99);
    }
}
```

---

### 3. Проведи валидацию

```csharp
var user = new UserModel { Name = "", Age = 10 };

var validator = new UserModelValidator();
var result = validator.Validate(user);

if (!result.IsValid)
{
    foreach (var error in result.Errors)
        Console.WriteLine($"{error.Property}: {error.Message}");
}
```

---

## 📚 Доступные правила

```csharp
// string
.NotEmpty()
.MinLength(5)

// int
.GreaterThan(0)
.LessThanOrEqualTo(100)
```

Ты можешь легко добавить свои правила:

```csharp
public static RuleBuilder<T, string> StartsWithA<T>(this RuleBuilder<T, string> builder)
{
    return builder.AddRule((val, ctx) =>
    {
        if (!val.StartsWith("A"))
            ctx.AddError(builder.Name, "must start with A");
    });
}
```

---

## 💡 FAQ

### ❓ Могу ли я валидировать nullable поля?
Да. Используй `int?` или `string?` и просто добавляй правила, например:

```csharp
RuleFor("Age", x => x.Age ?? 0).GreaterThan(0);
```

---

### ❓ Что возвращает `Validate()`?
Объект `ValidationResult`:

```csharp
public class ValidationResult
{
    public bool IsValid { get; }
    public List<ValidationFailure> Errors { get; }
}
```

---

### ❓ Как добавить правила в рантайме?
Ты можешь использовать `.AddRule(...)` напрямую:

```csharp
RuleFor("Custom", x => x.Something)
    .AddRule((val, ctx) =>
    {
        if (val != "expected") ctx.AddError("Custom", "is not expected");
    });
```

---

## 🧱 Структура библиотеки

- `Validator<T>` — базовый абстрактный валидатор.
- `RuleBuilder<T, TProp>` — fluent-конфигуратор правил.
- `ValidationContext` — прокси для записи ошибок.
- `ValidationResult`, `ValidationFailure` — результаты валидации.
- `ValidationExtensions` — fluent API с готовыми правилами.

---

## 🧪 Пример использования в WPF

```xaml
<TextBox Text="{Binding Name, UpdateSourceTrigger=PropertyChanged}" />
<TextBlock Text="{Binding NameError}" Foreground="Red" />
```

```csharp
public string Name { get; set; }
public string NameError { get; set; }

private void Validate()
{
    var result = _validator.Validate(this);
    NameError = result.Errors.FirstOrDefault(e => e.Property == "Name")?.Message;
}
```

---