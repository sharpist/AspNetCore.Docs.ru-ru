---
title: 'Поддерживаемые платформы ASP.NET Core SignalR'
author: bradygaster
description: Сведения о поддерживаемых платформах для ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051022"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="1b283-103">Поддерживаемые платформы ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="1b283-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="1b283-104">Системные требования к  Server</span><span class="sxs-lookup"><span data-stu-id="1b283-104">Server system requirements</span></span>

<span data-ttu-id="1b283-105">SignalR для ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b283-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="1b283-106">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="1b283-106">JavaScript client</span></span>

<span data-ttu-id="1b283-107">[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:</span><span class="sxs-lookup"><span data-stu-id="1b283-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="1b283-108">Браузер</span><span class="sxs-lookup"><span data-stu-id="1b283-108">Browser</span></span>                          | <span data-ttu-id="1b283-109">Версия</span><span class="sxs-lookup"><span data-stu-id="1b283-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="1b283-110">Apple Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="1b283-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="1b283-111">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="1b283-111">Current&dagger;</span></span> |
| <span data-ttu-id="1b283-112">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="1b283-112">Google Chrome, including Android</span></span> | <span data-ttu-id="1b283-113">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="1b283-113">Current&dagger;</span></span> |
| <span data-ttu-id="1b283-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="1b283-114">Microsoft Edge</span></span>                   | <span data-ttu-id="1b283-115">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="1b283-115">Current&dagger;</span></span> |
| <span data-ttu-id="1b283-116">Mozilla Firefox;</span><span class="sxs-lookup"><span data-stu-id="1b283-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="1b283-117">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="1b283-117">Current&dagger;</span></span> |

<span data-ttu-id="1b283-118">&dagger;*Текущий* означает последнюю версию браузера.</span><span class="sxs-lookup"><span data-stu-id="1b283-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="1b283-119">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="1b283-119">.NET client</span></span>

<span data-ttu-id="1b283-120">[Клиент .NET](xref:signalr/dotnet-client) выполняется на любой платформе, поддерживаемой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1b283-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="1b283-121">Например, [разработчики Xamarin могут использовать SignalR ](https://github.com/aspnet/Announcements/issues/305) для создания приложений Android с помощью Xamarin. Android 8.4.0.1 и более поздних версий и приложений iOS с помощью Xamarin. iOS 11.14.0.4 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="1b283-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="1b283-122">Если сервер использует службы IIS, для транспорта WebSocket требуется IIS 8,0 или более поздняя версия на Windows Server 2012 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="1b283-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="1b283-123">Другие транспорты поддерживаются на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="1b283-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="1b283-124">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="1b283-124">Java client</span></span>

<span data-ttu-id="1b283-125">[Клиент Java](xref:signalr/java-client) поддерживает Java 8 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="1b283-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="1b283-126">Неподдерживаемые клиенты</span><span class="sxs-lookup"><span data-stu-id="1b283-126">Unsupported clients</span></span>

<span data-ttu-id="1b283-127">Следующие клиенты доступны, но являются экспериментальными или неофициальными.</span><span class="sxs-lookup"><span data-stu-id="1b283-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="1b283-128">В настоящее время они не поддерживаются и никогда не могут быть.</span><span class="sxs-lookup"><span data-stu-id="1b283-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="1b283-129">[Клиент C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="1b283-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="1b283-130">[Клиент SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="1b283-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
