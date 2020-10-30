---
title: Внедрение зависимостей в обработчики требований в ASP.NET Core
author: rick-anderson
description: Узнайте, как внедрять обработчики требований авторизации в приложение ASP.NET Core с помощью внедрения зависимостей.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060265"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Внедрение зависимостей в обработчики требований в ASP.NET Core

<a name="security-authorization-di"></a>

[Обработчики авторизации должны быть зарегистрированы](xref:security/authorization/policies#handler-registration) в коллекции служб во время настройки с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).

Предположим, у вас есть репозиторий правил, которые необходимо вычислить в обработчике авторизации, и этот репозиторий был зарегистрирован в коллекции служб. Авторизация разрешает и внедряет их в конструктор.

Например, для использования ASP. СЕТЕВая Инфраструктура ведения журнала, вставьте `ILoggerFactory` в обработчик. Такой обработчик может выглядеть следующим образом:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Предыдущий обработчик может быть зарегистрирован в любом [времени существования службы](/dotnet/core/extensions/dependency-injection#service-lifetimes). Следующий код использует `AddSingleton` для регистрации предыдущего обработчика:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Экземпляр обработчика создается при запуске приложения и внедряется в конструктор с помощью ondi `ILoggerFactory` .

> [!NOTE]
> Обработчики, использующие Entity Framework, не должны регистрироваться как singleton.
