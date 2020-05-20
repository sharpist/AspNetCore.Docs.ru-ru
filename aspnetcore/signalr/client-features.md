---
title: SignalRКлиенты ASP.NET Core
author: bradygaster
description: Узнайте, какие функции поддерживаются различными клиентами ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: a759e473ff7ffaebd0eb9309f37a959d0e06a466
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438969"
---
# <a name="aspnet-core-signalr-clients"></a>Клиенты SignalR ASP.NET Core

## <a name="versioning-support-and-compatibility"></a>Управление версиями, поддержка и совместимость

Клиенты SignalR поставляются вместе с серверными компонентами и имеют соответствующие версии. Любой поддерживаемый клиент может безопасно подключаться к любому поддерживаемому серверу, и все проблемы совместимости будут считаться исправленными ошибками. Клиенты SignalR поддерживаются в том же жизненном цикле поддержки, что и остальная часть .NET Core. Дополнительные сведения см. [в разделе Политика поддержки .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Для многих функций требуется совместимый клиент **и** сервер. Ниже приведена таблица, в которой показаны минимальные версии для различных функций.

Версии 1. x SignalR сопоставляются с выпусками 2,1 и 2,2 .NET Core и имеют одинаковое время существования. Для версии 3. x и более поздней версия SignalR точно соответствует остальной части .NET и имеет тот же жизненный цикл поддержки.

| Версия SignalR | Версия .NET Core | Уровень поддержки | Дата окончания поддержки |
| - | - | - | - |
| 1.0. x | 2.1.x | Долгосрочная поддержка | 21 августа 2021 г. |
| 1.1. x | 2.2. x | Завершение срока службы | 23 декабря 2019 г. |
| 3. x или более поздней версии | *то же, что и версия SignalR* | См. [политику поддержки .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . |

**Примечание.** В ASP.NET Core 3,0 клиент JavaScript *перемещается* в `@microsoft/signalr` пакет NPM.

## <a name="feature-distribution"></a>Распространение компонентов

В таблице ниже приведены функции и поддержка для клиентов, которые предлагают поддержку в режиме реального времени. Для каждого компонента отображается *Минимальная* версия, поддерживающая эту функцию. Если версия не указана, эта функция не поддерживается.

| Признак | Сервер | Клиент .NET | Клиент JavaScript | Клиент Java |
| ---- | :-: | :-: | :-: | :-: |
| SignalRПоддержка службы Azure |2.1.0|1.0.0|1.0.0|1.0.0|
| [Потоковая передача из сервера в клиент](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Потоковая передача клиента в сервер](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Автоматическое повторное подключение ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Транспорт WebSockets |2.1.0|1.0.0|1.0.0|1.0.0|
| Транспорт событий, отправленных сервером |2.1.0|1.0.0|1.0.0|❌|
| Длинный опрашивающий транспорт |2.1.0|1.0.0|1.0.0|3.0.0|
| Протокол концентратора JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Протокол MessagePack для концентратора |2.1.0|1.0.0|1.0.0|❌|

Поддержка включения дополнительных клиентских функций будет прослеживаться в средстве [записи проблем](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Начало работы с SignalR для ASP.NET Core](xref:tutorials/signalr)
* [Поддерживаемые платформы](xref:signalr/supported-platforms)
* [Концентраторы](xref:signalr/hubs)
* [Клиент JavaScript](xref:signalr/javascript-client)
