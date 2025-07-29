
# 📘 PR0ST0.Http — FAQ и Документация

Простая, расширяемая и мощная обёртка над `HttpClient` с поддержкой:
- builder-DSL
- query параметров
- логирования
- моков
- сериализации
- retry
- timeout
- DI

---

## 🚀 Быстрый старт

```csharp
var builder = new HttpRequestBuilder(new HttpClient())
    .WithMethod(HttpMethod.Get)
    .WithUrl("https://jsonplaceholder.typicode.com/posts/1");

var response = await builder.SendAsync();
var content = await response.Content.ReadAsStringAsync();
```

---

## 📦 Сериализация (по умолчанию используется `NewtonsoftJsonSerializer`)

```csharp
var builder = new HttpRequestBuilder(new HttpClient())
    .WithMethod(HttpMethod.Get)
    .WithUrl("https://jsonplaceholder.typicode.com/posts/1");

var post = await builder.SendAsync<Post>(); // Автодесериализация
```

---

## 💬 Отправка тела (POST с JSON)

```csharp
var post = new Post { Title = "Hello", Body = "World", UserId = 1 };

var builder = new HttpRequestBuilder(new HttpClient())
    .WithMethod(HttpMethod.Post)
    .WithUrl("https://jsonplaceholder.typicode.com/posts")
    .WithBody(post);

var response = await builder.SendAsync();
```

---

## 📎 Добавление заголовков

```csharp
.WithHeader("Authorization", "Bearer xyz")
.WithHeader("Custom-Header", "Value")
```

---

## ❓ Добавление query параметров

```csharp
.WithQueryParameter("search", "test")
.WithQueryParameter("limit", 10)
```

---

## ⏱ Timeout и 🔁 Retry

```csharp
.WithTimeout(TimeSpan.FromSeconds(5))
.WithRetry(3, TimeSpan.FromSeconds(1))
```

---

## 🧪 Моки (замена ответа без запроса)

```csharp
public class FakeMockHandler : IMockHttpHandler
{
    public bool TryMock(HttpRequestMessage request, out HttpResponseMessage response)
    {
        if (request.RequestUri.AbsoluteUri.Contains("test"))
        {
            response = new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent("{\"id\": 1, \"title\": \"mocked\"}", Encoding.UTF8, "application/json")
            };
            return true;
        }

        response = null;
        return false;
    }
}
```

---

## 📋 Интерфейсы расширения

| Интерфейс           | Назначение                           |
|---------------------|--------------------------------------|
| `IHttpLogger`       | Логирование запросов и ответов       |
| `IHttpSerializer`   | Кастомная сериализация (например, XML) |
| `IMockHttpHandler`  | Подмена ответов без сети             |

---

## ❓ Часто задаваемые вопросы

**Q: Как использовать без DI-контейнера?**  
A: Просто создавайте `HttpRequestBuilder` вручную с `new HttpClient()` и `new NewtonsoftJsonSerializer()`.

**Q: Как подменить сериализацию?**  
A: Передайте свою реализацию `IHttpSerializer` в конструктор.

**Q: Поддерживает ли библиотека `FormUrlEncodedContent`?**  
A: Пока нет — но можно добавить через `.WithContent(HttpContent)` вручную.

---

## 📦 Пример полной инициализации

```csharp
var builder = new HttpRequestBuilder(
    new HttpClient(),
    new ConsoleHttpLogger(),
    new NewtonsoftJsonSerializer(),
    new FakeMockHandler()
);

var result = await builder
    .WithMethod(HttpMethod.Get)
    .WithUrl("https://jsonplaceholder.typicode.com/posts/1")
    .SendAsync<Post>();
```

---

## 🛠 Рекомендованные пространства имён

```csharp
using PR0ST0.Http;
using PR0ST0.Http.Builders;
using PR0ST0.Http.Logging;
using PR0ST0.Http.Mocks;
using PR0ST0.Http.Serialization;
```

---

## 🧾 Структура проекта

```
PR0ST0.Http/
│
├── Builders/HttpRequestBuilder.cs
├── Core/IHttpRequestBuilder.cs
├── Logging/IHttpLogger.cs, NullHttpLogger.cs
├── Mocks/IMockHttpHandler.cs, NullMockHttpHandler.cs
├── Serialization/IHttpSerializer.cs, NewtonsoftJsonSerializer.cs
└── Utils/JsonHelper.cs
```
