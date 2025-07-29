
# 📦 PR0ST0.Storage — Простое кроссплатформенное хранилище объектов

`PR0ST0.Storage` — это легковесная инфраструктурная библиотека для сериализации и хранения любых .NET-объектов в формате JSON на диске.

---

## 📌 Возможности

- Сохранение и загрузка объектов с автоматической сериализацией/десериализацией.
- Работа с произвольными типами (`T`).
- Проверка существования ключа.
- Удаление записей.
- Использует [Newtonsoft.Json] для совместимости и контроля сериализации.

---

## 🚀 Быстрый старт

### 1. Инициализация

```csharp
using PR0ST0.Storage;

var storage = new FileStorage("data"); // папка будет создана автоматически
```

### 2. Сохранение объекта

```csharp
await storage.SaveAsync("user", new UserProfile
{
    Username = "Alice",
    Age = 30
});
```

### 3. Загрузка объекта

```csharp
var user = await storage.LoadAsync<UserProfile>("user");
if (user != null)
    Console.WriteLine($"Welcome back, {user.Username}!");
```

### 4. Проверка существования

```csharp
bool exists = await storage.ExistsAsync("user");
```

### 5. Удаление объекта

```csharp
await storage.DeleteAsync("user");
```

---

## 🧪 Пример модели

```csharp
public class UserProfile
{
    public string Username { get; set; }
    public int Age { get; set; }
}
```

---

## 🛠 Настройки сериализации

```csharp
// Внутри FileStorage настроен JsonSerializerSettings:
new JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.Auto,
    Formatting = Formatting.Indented
};
```

Это позволяет сериализовать даже наследуемые типы.

---

## 🗂 Где хранятся данные?

По умолчанию все файлы сохраняются в указанную папку:

```plaintext
data/user.json
data/settings.json
```

---

## ✅ Когда использовать?

- Для оффлайн-хранилищ.
- Для кэширования данных между сессиями.
- Для сохранения профилей, настроек, истории.

---

## ❌ Когда не стоит использовать?

- При необходимости шифрования, шардирования или транзакций.
- При работе с большими объемами данных.
- В много-поточном или много-процессном доступе (используйте базу данных).

---

## 📂 Структура хранения

Каждому ключу соответствует файл:

```plaintext
SaveAsync("config") → "config.json"
```

---

## 💬 FAQ

### ❓ Поддерживает ли бинарный формат?
Нет, только JSON с `Newtonsoft.Json`.

### ❓ Работает ли на Android и Linux?
Да, кроссплатформенно — использует `System.IO` и `Newtonsoft.Json`.

### ❓ Можно ли сохранить сложный объект с наследованием?
Да, используется `TypeNameHandling = Auto`.
