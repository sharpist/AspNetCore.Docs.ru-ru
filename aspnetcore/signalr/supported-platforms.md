---
title: Поддерживаемые платформы ASP.NET Core SignalR
author: bradygaster
description: Сведения о поддерживаемых платформах для ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 91fd2553803d855b338b1d1b46d55e1d1e4cc21e
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635155"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>Поддерживаемые платформы ASP.NET Core SignalR

## <a name="server-system-requirements"></a>Системные требования к  Server

SignalR для ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.

## <a name="javascript-client"></a>Клиент JavaScript

[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:

| Браузер                         | Версия         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Текущий&dagger; |
| Mozilla Firefox;                 | Текущий&dagger; |
| Google Chrome; включает Android | Данном&dagger; |
| Обозревателе включает iOS            | Текущий&dagger; |
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
