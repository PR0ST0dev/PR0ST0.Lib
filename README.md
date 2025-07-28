# PR0ST0.Lib 📦
Универсальная .NET-библиотека, объединяющая MVVM, Mediator, Validation, Http, Storage и интеграцию в единый фреймворк.

## Возможности
- 🔄 MVVM: навигация, жизненный цикл, параметры, анимации
- 📬 Mediator: команды, запросы, события
- ✅ Validation: декларативная валидация и ошибки
- 🌐 Http: builder-Dsl, Retry, Timeout, Mock, Logging
- 💾 Storage: in-memory, file, local/app settings
- 🔗 Integration: полная совместимость компонентов

## Установка
```
nuget install PR0ST0.Lib -Source ./nupkgs
```

## Быстрый старт
```csharp
AppBootstrapper.Run();
await Navigation.NavigateToAsync<HomeViewModel>("Main");
```

## Документация
См. `docs/*.md` или [docs.html](docs.html)
