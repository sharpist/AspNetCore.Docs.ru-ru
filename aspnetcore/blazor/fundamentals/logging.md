---
title: 'Ведение журналов ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Сведения о ведении журналов в :::no-loc(Blazor)::: приложениях, включая настройку уровня ведения журнала и запись сообщений журнала из компонентов :::no-loc(Razor):::.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055988"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="f736a-103">Ведение журналов ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="f736a-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="f736a-104">Настройте ведение журналов в приложениях :::no-loc(Blazor WebAssembly)::: с помощью свойства `WebAssemblyHostBuilder.Logging` в `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f736a-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="f736a-105">Свойство `Logging` имеет тип <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, поэтому все методы расширения, доступные в <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, также доступны в `Logging`.</span><span class="sxs-lookup"><span data-stu-id="f736a-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="f736a-106">Конфигурацию ведения журнала можно загрузить из файлов параметров приложения.</span><span class="sxs-lookup"><span data-stu-id="f736a-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="f736a-107">Для получения дополнительной информации см. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f736a-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="f736a-108">Общие рекомендации по ведению журнала ASP.NET Core см. в разделе <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f736a-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="f736a-109">Ведение журнала клиента :::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET</span><span class="sxs-lookup"><span data-stu-id="f736a-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="f736a-110">Вставьте <xref:Microsoft.Extensions.Logging.ILoggerProvider>, чтобы добавить `WebAssemblyConsoleLogger` к регистраторам, передаваемым в <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f736a-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="f736a-111">В отличие от традиционных <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` является оболочкой для API-интерфейсов ведения журнала, характерных для браузера (например, `console.log`).</span><span class="sxs-lookup"><span data-stu-id="f736a-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="f736a-112">Использование `WebAssemblyConsoleLogger` делает возможным ведение журнала в Mono внутри контекста браузера.</span><span class="sxs-lookup"><span data-stu-id="f736a-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="f736a-113">Ведение журнала в компонентах :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="f736a-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="f736a-114">Средства ведения журнала учитывают конфигурацию запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="f736a-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="f736a-115">Директива `using` для <xref:Microsoft.Extensions.Logging> обеспечивает поддержку завершения IntelliSense для таких интерфейсов API, как <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> и <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="f736a-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="f736a-116">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILogger> в компонентах :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="f736a-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

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

<span data-ttu-id="f736a-117">Следующий пример демонстрирует ведение журнала с помощью <xref:Microsoft.Extensions.Logging.ILoggerFactory> в компонентах :::no-loc(Razor)::::</span><span class="sxs-lookup"><span data-stu-id="f736a-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f736a-118">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f736a-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
