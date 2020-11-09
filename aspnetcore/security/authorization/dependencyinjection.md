---
title: Внедрение зависимостей в обработчики требований в ASP.NET Core
author: rick-anderson
description: Узнайте, как внедрять обработчики требований авторизации в приложение ASP.NET Core с помощью внедрения зависимостей.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060265"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="6338c-103">Внедрение зависимостей в обработчики требований в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6338c-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="6338c-104">[Обработчики авторизации должны быть зарегистрированы](xref:security/authorization/policies#handler-registration) в коллекции служб во время настройки с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6338c-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6338c-105">Предположим, у вас есть репозиторий правил, которые необходимо вычислить в обработчике авторизации, и этот репозиторий был зарегистрирован в коллекции служб.</span><span class="sxs-lookup"><span data-stu-id="6338c-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="6338c-106">Авторизация разрешает и внедряет их в конструктор.</span><span class="sxs-lookup"><span data-stu-id="6338c-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="6338c-107">Например, для использования ASP. СЕТЕВая Инфраструктура ведения журнала, вставьте `ILoggerFactory` в обработчик.</span><span class="sxs-lookup"><span data-stu-id="6338c-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="6338c-108">Такой обработчик может выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="6338c-108">Such a handler might look like the following code:</span></span>

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

<span data-ttu-id="6338c-109">Предыдущий обработчик может быть зарегистрирован в любом [времени существования службы](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="6338c-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="6338c-110">Следующий код использует `AddSingleton` для регистрации предыдущего обработчика:</span><span class="sxs-lookup"><span data-stu-id="6338c-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="6338c-111">Экземпляр обработчика создается при запуске приложения и внедряется в конструктор с помощью ondi `ILoggerFactory` .</span><span class="sxs-lookup"><span data-stu-id="6338c-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="6338c-112">Обработчики, использующие Entity Framework, не должны регистрироваться как singleton.</span><span class="sxs-lookup"><span data-stu-id="6338c-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
