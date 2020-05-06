---
title: Миграция из ClaimsPrincipal. Current
author: mjrousos
description: Узнайте, как выполнить миграцию с ClaimsPrincipal. Current, чтобы получить удостоверение текущего пользователя, прошедшего проверку подлинности, и утверждения в ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776094"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Миграция из ClaimsPrincipal. Current

В проектах ASP.NET 4. x было распространено использование [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) для получения удостоверения и утверждений текущего пользователя, прошедшего проверку подлинности. В ASP.NET Core это свойство больше не задано. Код, который был в зависимости от него, необходимо обновить, чтобы получить удостоверение текущего пользователя, прошедшего проверку подлинности, с помощью различных средств.

## <a name="context-specific-data-instead-of-static-data"></a>Данные, зависящие от контекста, а не статические данные

При использовании ASP.NET Core значения обоих `ClaimsPrincipal.Current` параметров и `Thread.CurrentPrincipal` не устанавливаются. Эти свойства представляют статическое состояние, которое ASP.NET Core обычно избежать. Вместо этого ASP.NET Core архитектура предназначена для получения зависимостей (таких как удостоверение текущего пользователя) из коллекций служб, зависящих от контекста (с помощью модели [внедрения зависимостей](xref:fundamentals/dependency-injection) (DI)). Более того, `Thread.CurrentPrincipal` является статическим потоком, поэтому он может не сохранять изменения в некоторых асинхронных сценариях `ClaimsPrincipal.Current` (и `Thread.CurrentPrincipal` просто вызовы по умолчанию).

Чтобы понять типы проблем, которые статические члены потока могут привести к асинхронным сценариям, рассмотрим следующий фрагмент кода:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Предыдущий пример кода задает `Thread.CurrentPrincipal` и проверяет его значение до и после ожидания асинхронного вызова. `Thread.CurrentPrincipal`относится только к *потоку* , в котором он задан, и метод, скорее всего, возобновляет выполнение в другом потоке после ожидания. Следовательно, `Thread.CurrentPrincipal` имеется, когда он сначала проверяется, но имеет значение NULL после вызова `await Task.Yield()`.

Получение удостоверения текущего пользователя из коллекции служб DI для приложения является более тестируемым, так как можно легко внедрять удостоверения тестов.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Получение текущего пользователя в ASP.NET Core приложении

Существует несколько вариантов получения ASP.NET Core текущего пользователя `ClaimsPrincipal` , прошедшего проверку подлинности, вместо: `ClaimsPrincipal.Current`

* **ControllerBase. User**. Контроллеры MVC могут обращаться к текущему аутентифицированному пользователю с помощью его свойства [пользователя](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Компоненты с доступом к текущему `HttpContext` (например, по промежуточного слоя) могут получить текущего пользователя `ClaimsPrincipal` из [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Передано из вызывающей стороны**. Библиотеки без доступа к текущей `HttpContext` библиотеке часто вызываются из контроллеров или компонентов промежуточного слоя и могут иметь удостоверение текущего пользователя, переданное в качестве аргумента.
* **Ихттпконтекстакцессор**. Проект, переносимый в ASP.NET Core, может быть слишком большим для простого передачи удостоверения текущего пользователя во все необходимые расположения. В таких случаях в качестве обходного пути можно использовать [ихттпконтекстакцессор](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) . `IHttpContextAccessor`может получить доступ к текущему `HttpContext` (если он существует). Если используется DI, см. раздел <xref:fundamentals/httpcontext>. Краткосрочное решение для получения удостоверения текущего пользователя в коде, которое еще не было обновлено для работы с архитектурой на основе ASP.NET Core, будет выглядеть так:

  * Сделайте `IHttpContextAccessor` доступной в контейнере di, вызвав [аддхттпконтекстакцессор](https://github.com/aspnet/Hosting/issues/793) в `Startup.ConfigureServices`.
  * Получите экземпляр `IHttpContextAccessor` во время запуска и сохраните его в статической переменной. Экземпляр становится доступным для кода, который ранее получал текущего пользователя из статического свойства.
  * Получите текущего пользователя `ClaimsPrincipal` с помощью `HttpContextAccessor.HttpContext?.User`. Если этот код используется вне контекста HTTP-запроса, то параметр `HttpContext` имеет значение null.

Последний вариант, использующий `IHttpContextAccessor` экземпляр, хранящийся в статической переменной, противоречит ASP.NET Coreным принципам (с включением внедренных зависимостей в статические зависимости). Запланируйте в конечном `IHttpContextAccessor` итоге извлечение экземпляров из внедрения зависимостей. Статический вспомогательный метод может быть полезным мостом, но при переносе больших существующих ASP.NET приложений, которые ранее `ClaimsPrincipal.Current`использовались.
