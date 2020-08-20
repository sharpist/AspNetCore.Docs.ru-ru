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
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="73de4-103">Поддерживаемые платформы ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="73de4-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="73de4-104">Системные требования к  Server</span><span class="sxs-lookup"><span data-stu-id="73de4-104">Server system requirements</span></span>

<span data-ttu-id="73de4-105">SignalR для ASP.NET Core поддерживает любую серверную платформу, поддерживаемую ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73de4-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="73de4-106">Клиент JavaScript</span><span class="sxs-lookup"><span data-stu-id="73de4-106">JavaScript client</span></span>

<span data-ttu-id="73de4-107">[Клиент JavaScript](xref:signalr/javascript-client) работает на NodeJS 8 и более поздних версиях и в следующих браузерах:</span><span class="sxs-lookup"><span data-stu-id="73de4-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="73de4-108">Браузер</span><span class="sxs-lookup"><span data-stu-id="73de4-108">Browser</span></span>                         | <span data-ttu-id="73de4-109">Версия</span><span class="sxs-lookup"><span data-stu-id="73de4-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="73de4-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="73de4-110">Microsoft Edge</span></span>                  | <span data-ttu-id="73de4-111">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="73de4-111">Current&dagger;</span></span> |
| <span data-ttu-id="73de4-112">Mozilla Firefox;</span><span class="sxs-lookup"><span data-stu-id="73de4-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="73de4-113">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="73de4-113">Current&dagger;</span></span> |
| <span data-ttu-id="73de4-114">Google Chrome; включает Android</span><span class="sxs-lookup"><span data-stu-id="73de4-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="73de4-115">Данном&dagger;</span><span class="sxs-lookup"><span data-stu-id="73de4-115">Current&dagger;</span></span> |
| <span data-ttu-id="73de4-116">Обозревателе включает iOS</span><span class="sxs-lookup"><span data-stu-id="73de4-116">Safari; includes iOS</span></span>            | <span data-ttu-id="73de4-117">Текущий&dagger;</span><span class="sxs-lookup"><span data-stu-id="73de4-117">Current&dagger;</span></span> |
| <span data-ttu-id="73de4-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="73de4-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="73de4-119">11</span><span class="sxs-lookup"><span data-stu-id="73de4-119">11</span></span>              |

<span data-ttu-id="73de4-120">&dagger;*Current* ссылается на последнюю версию браузера.</span><span class="sxs-lookup"><span data-stu-id="73de4-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="73de4-121">Клиент .NET</span><span class="sxs-lookup"><span data-stu-id="73de4-121">.NET client</span></span>

<span data-ttu-id="73de4-122">[Клиент .NET](xref:signalr/dotnet-client) выполняется на любой платформе, поддерживаемой ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73de4-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="73de4-123">Например, [разработчики Xamarin могут использовать SignalR ](https://github.com/aspnet/Announcements/issues/305) для создания приложений Android с помощью Xamarin. Android 8.4.0.1 и более поздних версий и приложений iOS с помощью Xamarin. iOS 11.14.0.4 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="73de4-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="73de4-124">Если сервер использует службы IIS, для транспорта WebSocket требуется IIS 8,0 или более поздняя версия на Windows Server 2012 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="73de4-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="73de4-125">Другие транспорты поддерживаются на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="73de4-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="73de4-126">Клиент Java</span><span class="sxs-lookup"><span data-stu-id="73de4-126">Java client</span></span>

<span data-ttu-id="73de4-127">[Клиент Java](xref:signalr/java-client) поддерживает Java 8 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="73de4-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="73de4-128">Неподдерживаемые клиенты</span><span class="sxs-lookup"><span data-stu-id="73de4-128">Unsupported clients</span></span>

<span data-ttu-id="73de4-129">Следующие клиенты доступны, но являются экспериментальными или неофициальными.</span><span class="sxs-lookup"><span data-stu-id="73de4-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="73de4-130">В настоящее время они не поддерживаются и никогда не могут быть.</span><span class="sxs-lookup"><span data-stu-id="73de4-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="73de4-131">[Клиент C++](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="73de4-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="73de4-132">[Клиент SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="73de4-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
