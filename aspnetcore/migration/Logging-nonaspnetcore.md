---
title: Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0
author: pakrym
description: Узнайте, как перенести приложение non-ASP.NET Core, использующее Microsoft. Extensions. Logging от 2,1 до 2,2 или 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 0c85ca637c1e93bbde93c7d5d12408637476558e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399794"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="5a6fa-103">Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0</span><span class="sxs-lookup"><span data-stu-id="5a6fa-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="5a6fa-104">В этой статье описаны общие действия по переносу приложения non-ASP.NET Core, использующего `Microsoft.Extensions.Logging` от 2,1 до 2,2 или 3,0.</span><span class="sxs-lookup"><span data-stu-id="5a6fa-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="5a6fa-105">С версии 2.1 на 2.2</span><span class="sxs-lookup"><span data-stu-id="5a6fa-105">2.1 to 2.2</span></span>

<span data-ttu-id="5a6fa-106">Создайте `ServiceCollection` и вызовите вручную `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="5a6fa-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="5a6fa-107">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="5a6fa-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="5a6fa-108">2,2. Пример:</span><span class="sxs-lookup"><span data-stu-id="5a6fa-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="5a6fa-109">от 2,1 до 3,0</span><span class="sxs-lookup"><span data-stu-id="5a6fa-109">2.1 to 3.0</span></span>

<span data-ttu-id="5a6fa-110">В 3,0 используйте `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="5a6fa-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="5a6fa-111">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="5a6fa-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="5a6fa-112">3,0. Пример:</span><span class="sxs-lookup"><span data-stu-id="5a6fa-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="5a6fa-113">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5a6fa-113">Additional resources</span></span>

* <span data-ttu-id="5a6fa-114">[Пакет NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="5a6fa-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
