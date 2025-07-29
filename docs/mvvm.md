
# PR0ST0.MVVM — Документация и FAQ

Библиотека **PR0ST0.MVVM** предназначена для удобной реализации паттерна **MVVM** в WPF с поддержкой:

- Асинхронной навигации
- INavigationResult (возврат данных из диалогов/страниц)
- Deep linking и маршрутов (`Route`)
- Множественных `Frame`
- Анимаций переходов
- Автоматической регистрации View/VM

---

## 🔧 Основы

### 1. Базовый ViewModel

```csharp
[Route("home")]
public class HomeViewModel : ViewModelBase
{
    public string WelcomeText { get; set; } = "Добро пожаловать!";
}
```

### 2. Привязанная View (HomePage.xaml)

```xml
<Page x:Class="MyApp.Views.HomePage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation">
    <TextBlock Text="{Binding WelcomeText}" />
</Page>
```

Или с атрибутом:

```csharp
[ViewFor(typeof(HomeViewModel))]
public partial class HomePage : Page { ... }
```

---

### 3. Навигация

```csharp
Navigator.NavigateTo<HomeViewModel>("Main");
await Navigator.NavigateToAsync<SettingsViewModel>("Main");
```

---

### 4. Возврат результата

```csharp
var result = await Navigator.NavigateForResult<ModalViewModel>("Main");
MessageBox.Show("Пользователь ввёл: " + result.Result);
```

Внутри `ModalViewModel`:

```csharp
public class ModalViewModel : ViewModelBase, INavigationResultHost
{
    public string Input { get; set; }
    private Action<object> _callback;

    public ICommand CloseCommand => new RelayCommand(() => _callback?.Invoke(Input));
    public void SetResultCallback(Action<object> callback) => _callback = callback;
}
```

---

### 5. Deep Linking и Query параметры

```csharp
[Route("profile/details")]
public class DetailsViewModel : ViewModelBase
{
    public int Id { get; set; }
}
```

```csharp
Navigator.NavigateToRoute("profile/details?id=5", "Main", null);
```

---

### 6. Анимации переходов

```csharp
Navigator.NavigateTo<HomeViewModel>("Main", null, TransitionType.Slide);
```

---

## 🔄 Регистрация

```csharp
container.Register<HomeViewModel, HomeViewModel>();
navigator.Register<HomeViewModel, HomePage>();

// Или авто:
navigator.AutoRegisterViewsWithAttributes(assembly);
navigator.RegisterRoutesFromViewModels(assembly);
```

---

## ❓ FAQ

### Как вернуть значение из страницы?
→ Используйте `INavigationResultHost` и `NavigateForResult<T>()`.

### Как связать View и ViewModel?
→ Через `navigator.Register<VM, View>()` или `[ViewFor(typeof(VM))]`.

### Как передать параметры?
→ Используйте query-строку: `"route?param=value"` или напрямую в `NavigateTo()`.

### Как использовать несколько Frame?
→ Зарегистрируйте их по ключу:

```csharp
navigator.RegisterFrame("Main", mainFrame);
navigator.NavigateTo<HomeViewModel>("Main");
```

### Как сбросить страницу (ViewModel)?
```csharp
navigator.Reset<HomeViewModel>("Main");
```

### Как очистить кэш страниц?
```csharp
navigator.ClearCache();
```

### Как получить ссылку для deep linking?
```csharp
var link = navigator.GetNavigationLink<ProfileViewModel>(new { id = 10 });
// Результат: "profile?id=10"
```

---
