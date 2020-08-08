---
title: Внедрение зависимостей в обработчики требований в ASP.NET Core
author: rick-anderson
description: Узнайте, как внедрять обработчики требований авторизации в приложение ASP.NET Core с помощью внедрения зависимостей.
ms.author: riande
ms.date: 10/14/2016
no-loc:
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
ms.openlocfilehash: e58498cc7a28b598b919c5cab128249448bde32a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021007"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Внедрение зависимостей в обработчики требований в ASP.NET Core

<a name="security-authorization-di"></a>

[Обработчики авторизации должны быть зарегистрированы](xref:security/authorization/policies#handler-registration) в коллекции служб во время настройки (с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection)).

Предположим, у вас есть репозиторий правил, которые необходимо вычислить в обработчике авторизации, и этот репозиторий был зарегистрирован в коллекции служб. Авторизация будет разрешаться и внедряться в конструктор.

Например, если вы хотите использовать технологию ASP. СЕТЕВая Инфраструктура ведения журнала, которую необходимо внедрить `ILoggerFactory` в обработчик. Такой обработчик может выглядеть следующим образом:

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

Обработчик регистрируется `services.AddSingleton()` следующим образом:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Экземпляр обработчика будет создан при запуске приложения, а DI будет внедрять зарегистрированные `ILoggerFactory` в конструктор.

> [!NOTE]
> Обработчики, использующие Entity Framework, не должны регистрироваться как singleton.
