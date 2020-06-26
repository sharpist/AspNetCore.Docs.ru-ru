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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Миграция из Microsoft. Extensions. Logging 2,1 в 2,2 или 3,0

В этой статье описаны общие действия по переносу приложения non-ASP.NET Core, использующего `Microsoft.Extensions.Logging` от 2,1 до 2,2 или 3,0.

## <a name="21-to-22"></a>С версии 2.1 на 2.2

Создайте `ServiceCollection` и вызовите вручную `AddLogging` .

2,1. Пример:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2. Пример:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>от 2,1 до 3,0

В 3,0 используйте `LoggingFactory.Create` .

2,1. Пример:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0. Пример:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Пакет NuGet Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
