---
title: Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0
author: pakrym
description: Узнайте, как перенести приложение non-ASP.NET Core, использующее Microsoft. Extensions. Logging от 2,1 до 2,2 или 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015118"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="45781-103">Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0</span><span class="sxs-lookup"><span data-stu-id="45781-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="45781-104">В этой статье описаны общие действия по переносу приложения non-ASP.NET Core, использующего `Microsoft.Extensions.Logging` от 2,1 до 2,2 или 3,0.</span><span class="sxs-lookup"><span data-stu-id="45781-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="45781-105">С версии 2.1 на 2.2</span><span class="sxs-lookup"><span data-stu-id="45781-105">2.1 to 2.2</span></span>

<span data-ttu-id="45781-106">Создайте `ServiceCollection` и вызовите вручную `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="45781-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="45781-107">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="45781-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="45781-108">2,2. Пример:</span><span class="sxs-lookup"><span data-stu-id="45781-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="45781-109">от 2,1 до 3,0</span><span class="sxs-lookup"><span data-stu-id="45781-109">2.1 to 3.0</span></span>

<span data-ttu-id="45781-110">В 3,0 используйте `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="45781-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="45781-111">2,1. Пример:</span><span class="sxs-lookup"><span data-stu-id="45781-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="45781-112">3,0. Пример:</span><span class="sxs-lookup"><span data-stu-id="45781-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="45781-113">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="45781-113">Additional resources</span></span>

* <span data-ttu-id="45781-114">[Пакет NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="45781-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
