---
title: Поддерживаемые платформы ASP.NET Core SignalR
author: bradygaster
description: Сведения о поддерживаемых платформах для ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9d7dd946ae5cf83b76a83ba1faeed0bb3fc31cef
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405826"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core SignalR

## <a name="server-system-requirements"></a>Системные требования к  Server

SignalRдля ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.

## <a name="javascript-client"></a>Клиент JavaScript

[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:

| Браузер                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Текущие&dagger; |
| Mozilla Firefox                 | Текущие&dagger; |
| Google Chrome; включает Android | Текущие&dagger; |
| Обозревателе включает iOS            | Текущие&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*Current* ссылается на последнюю версию браузера.

## <a name="net-client"></a>Клиент .NET

[Клиент .NET](xref:signalr/dotnet-client) выполняется на любой платформе, поддерживаемой ASP.NET Core. Например, [разработчики Xamarin могут использовать SignalR ](https://github.com/aspnet/Announcements/issues/305) для создания приложений Android с помощью Xamarin. Android 8.4.0.1 и более поздних версий и приложений iOS с помощью Xamarin. iOS 11.14.0.4 и более поздних версий.

Если сервер использует службы IIS, для транспорта WebSocket требуется IIS 8,0 или более поздняя версия на Windows Server 2012 или более поздней версии. Другие транспорты поддерживаются на всех платформах.

## <a name="java-client"></a>Клиент Java

[Клиент Java](xref:signalr/java-client) поддерживает Java 8 и более поздних версий.

## <a name="unsupported-clients"></a>Неподдерживаемые клиенты

Следующие клиенты доступны, но являются экспериментальными или неофициальными. В настоящее время они не поддерживаются и никогда не могут быть.

* [Клиент C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Клиент SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
