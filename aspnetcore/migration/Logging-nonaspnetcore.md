---
title: Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0
author: pakrym
description: Узнайте, как перенести приложение non-ASP.NET Core, использующее Microsoft. Extensions. Logging от 2,1 до 2,2 или 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777063"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="a8699-103">Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0</span><span class="sxs-lookup"><span data-stu-id="a8699-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="a8699-104">В этой статье описаны общие действия по переносу приложения non-ASP.NET Core, использующего `Microsoft.Extensions.Logging` от 2,1 до 2,2 или 3,0.</span><span class="sxs-lookup"><span data-stu-id="a8699-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="a8699-105">С версии 2.1 на 2.2</span><span class="sxs-lookup"><span data-stu-id="a8699-105">2.1 to 2.2</span></span>

<span data-ttu-id="a8699-106">Создайте `ServiceCollection` и вызовите `AddLogging`вручную.</span><span class="sxs-lookup"><span data-stu-id="a8699-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="a8699-107">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="a8699-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a8699-108">2,2. Пример:</span><span class="sxs-lookup"><span data-stu-id="a8699-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="a8699-109">от 2,1 до 3,0</span><span class="sxs-lookup"><span data-stu-id="a8699-109">2.1 to 3.0</span></span>

<span data-ttu-id="a8699-110">В 3,0 используйте `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="a8699-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="a8699-111">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="a8699-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="a8699-112">3,0. Пример:</span><span class="sxs-lookup"><span data-stu-id="a8699-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="a8699-113">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a8699-113">Additional resources</span></span>

<xref:fundamentals/logging/index>