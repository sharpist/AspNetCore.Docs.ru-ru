---
title: Ведение журналов ASP.NET Core Blazor
author: guardrex
description: Сведения о ведении журналов в Blazor приложениях, включая настройку уровня ведения журнала и запись сообщений журнала из компонентов Razor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 841c4021d9217312b2601b0e775542c6455cca82
ms.sourcegitcommit: dd2a1542a4a377123490034153368c135fdbd09e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85240876"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="223c4-103">Ведение журналов ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="223c4-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="223c4-104"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="223c4-104"> WebAssembly</span></span>

<span data-ttu-id="223c4-105">Настройте ведение журналов в приложениях Blazor WebAssembly с помощью свойства `WebAssemblyHostBuilder.Logging` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="223c4-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="223c4-106">Свойство `Logging` имеет тип <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, поэтому все методы расширения, доступные в <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, также доступны в `Logging`.</span><span class="sxs-lookup"><span data-stu-id="223c4-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="223c4-107">Конфигурацию ведения журнала можно загрузить из файлов параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="223c4-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="223c4-108">Для получения дополнительной информации см. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="223c4-108">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="223c4-109">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="223c4-109">Blazor Server</span></span>

<span data-ttu-id="223c4-110">Общие рекомендации по ведению журнала ASP.NET Core см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="223c4-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a><span data-ttu-id="223c4-111">Ведение журнала клиента Blazor WebAssembly SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="223c4-111">Blazor WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="223c4-112">Вставьте <xref:Microsoft.Extensions.Logging.ILoggerProvider>, чтобы добавить `WebAssemblyConsoleLogger` к регистраторам, передаваемым в <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="223c4-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="223c4-113">В отличие от традиционных <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` является оболочкой для API-интерфейсов ведения журнала, характерных для браузера (например, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="223c4-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="223c4-114">Использование `WebAssemblyConsoleLogger` делает возможным ведение журнала в Mono внутри контекста браузера.</span><span class="sxs-lookup"><span data-stu-id="223c4-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="223c4-115">Ведение журнала в компонентах Razor</span><span class="sxs-lookup"><span data-stu-id="223c4-115">Log in Razor components</span></span>

<span data-ttu-id="223c4-116">Средства ведения журнала учитывают конфигурацию запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="223c4-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="223c4-117">Директива `using` для <xref:Microsoft.Extensions.Logging> обеспечивает поддержку завершения IntelliSense для таких интерфейсов API, как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="223c4-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="223c4-118">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILogger> в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="223c4-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="223c4-119">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILoggerFactory> в компонентах Razor:</span><span class="sxs-lookup"><span data-stu-id="223c4-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="223c4-120">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="223c4-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
