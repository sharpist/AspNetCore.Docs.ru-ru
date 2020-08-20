---
title: Миграция из ClaimsPrincipal. Current
author: mjrousos
description: Узнайте, как выполнить миграцию с ClaimsPrincipal. Current, чтобы получить удостоверение текущего пользователя, прошедшего проверку подлинности, и утверждения в ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634076"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Миграция из ClaimsPrincipal. Current

В проектах ASP.NET 4. x было распространено использование [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) для получения удостоверения и утверждений текущего пользователя, прошедшего проверку подлинности. В ASP.NET Core это свойство больше не задано. Код, который был в зависимости от него, необходимо обновить, чтобы получить удостоверение текущего пользователя, прошедшего проверку подлинности, с помощью различных средств.

## <a name="context-specific-state-instead-of-static-state"></a>Контекстно-зависимое состояние, а не статическое состояние

При использовании ASP.NET Core значения обоих параметров `ClaimsPrincipal.Current` и `Thread.CurrentPrincipal` не устанавливаются. Эти свойства представляют статическое состояние, которое ASP.NET Core обычно избежать. Вместо этого ASP.NET Core использует [внедрение зависимостей](xref:fundamentals/dependency-injection) (DI) для предоставления зависимостей, таких как удостоверение текущего пользователя. Получение удостоверения текущего пользователя из DI является более тестируемым, так как можно легко внедрять удостоверения тестов.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Получение текущего пользователя в ASP.NET Core приложении

Существует несколько вариантов получения ASP.NET Core текущего пользователя, прошедшего проверку подлинности, вместо `ClaimsPrincipal` `ClaimsPrincipal.Current` :

* **ControllerBase. User**. Контроллеры MVC могут обращаться к текущему аутентифицированному пользователю с помощью его свойства [пользователя](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Компоненты с доступом к текущему `HttpContext` (например, по промежуточного слоя) могут получить текущего пользователя `ClaimsPrincipal` из [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Передано из вызывающей стороны**. Библиотеки без доступа к текущей библиотеке `HttpContext` часто вызываются из контроллеров или компонентов промежуточного слоя и могут иметь удостоверение текущего пользователя, переданное в качестве аргумента.
* **Ихттпконтекстакцессор**. Проект, переносимый в ASP.NET Core, может быть слишком большим для простого передачи удостоверения текущего пользователя во все необходимые расположения. В таких случаях в качестве обходного пути можно использовать [ихттпконтекстакцессор](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) . `IHttpContextAccessor` может получить доступ к текущему `HttpContext` (если он существует). Если используется DI, см. раздел <xref:fundamentals/httpcontext> . Краткосрочное решение для получения удостоверения текущего пользователя в коде, которое еще не было обновлено для работы с архитектурой на основе ASP.NET Core, будет выглядеть так:

  * Сделайте `IHttpContextAccessor` доступной в контейнере di, вызвав [аддхттпконтекстакцессор](https://github.com/aspnet/Hosting/issues/793) в `Startup.ConfigureServices` .
  * Получите экземпляр `IHttpContextAccessor` во время запуска и сохраните его в статической переменной. Экземпляр становится доступным для кода, который ранее получал текущего пользователя из статического свойства.
  * Получите текущего пользователя `ClaimsPrincipal` с помощью `HttpContextAccessor.HttpContext?.User` . Если этот код используется вне контекста HTTP-запроса, то параметр `HttpContext` имеет значение null.

Последний вариант, использующий `IHttpContextAccessor` экземпляр, хранящийся в статической переменной, противоречит ASP.NET Core принципу добавления внедренных зависимостей к статическим зависимостям. Запланируйте в конечном итоге извлечение `IHttpContextAccessor` экземпляров из di. Статический вспомогательный метод может быть полезным мостом, но при переносе больших существующих приложений ASP.NET, использующих `ClaimsPrincipal.Current` .
