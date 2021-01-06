---
title: Выбор между ASP.NET 4.x и ASP.NET Core
author: rick-anderson
description: Что такое ASP.NET Core и ASP.NET 4.x и как выбрать между ними.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- appsettings.json
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 95ac4784634d38add5e28644d42b0182e15c6de9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060031"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="7b883-103">Выбор между ASP.NET 4.x и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="7b883-104">ASP.NET Core является переработанной версией ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="7b883-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="7b883-105">В этой статье перечислены различия между ними.</span><span class="sxs-lookup"><span data-stu-id="7b883-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="7b883-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-106">ASP.NET Core</span></span>

<span data-ttu-id="7b883-107">ASP.NET Core — это кроссплатформенная среда с открытым кодом для создания современных облачных веб-приложений в Windows, macOS или Linux.</span><span class="sxs-lookup"><span data-stu-id="7b883-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="7b883-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="7b883-108">ASP.NET 4.x</span></span>

<span data-ttu-id="7b883-109">ASP.NET 4.x — это развитая платформа, предоставляющая необходимые службы для создания серверных веб-приложений корпоративного класса в Windows.</span><span class="sxs-lookup"><span data-stu-id="7b883-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="7b883-110">Выбор платформы</span><span class="sxs-lookup"><span data-stu-id="7b883-110">Framework selection</span></span>

<span data-ttu-id="7b883-111">В следующей таблице сравниваются ASP.NET Core и ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="7b883-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="7b883-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-112">ASP.NET Core</span></span> | <span data-ttu-id="7b883-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="7b883-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="7b883-114">Предназначена для Windows, macOS или Linux</span><span class="sxs-lookup"><span data-stu-id="7b883-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="7b883-115">Предназначена для Windows</span><span class="sxs-lookup"><span data-stu-id="7b883-115">Build for Windows</span></span>|
|<span data-ttu-id="7b883-116">[Razor Pages](xref:razor-pages/index) — рекомендуемый метод создания пользовательского веб-интерфейса в ASP.NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="7b883-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="7b883-117">См. также сведения об [MVC](xref:mvc/overview), [веб-API](xref:tutorials/first-web-api) и [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="7b883-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="7b883-118">Использование [веб-форм](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [веб-API](/aspnet/web-api/), [веб-перехватчиков](/aspnet/webhooks/) или [веб-страниц](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="7b883-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="7b883-119">Несколько версий для одного компьютера</span><span class="sxs-lookup"><span data-stu-id="7b883-119">Multiple versions per machine</span></span>|<span data-ttu-id="7b883-120">Одна версия для одного компьютера</span><span class="sxs-lookup"><span data-stu-id="7b883-120">One version per machine</span></span>|
|<span data-ttu-id="7b883-121">Разработка в [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) или [Visual Studio Code](https://code.visualstudio.com/) с использованием C# или F#</span><span class="sxs-lookup"><span data-stu-id="7b883-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="7b883-122">Разработка с [Visual Studio](https://visualstudio.microsoft.com/vs/) с использованием C#, VB или F#</span><span class="sxs-lookup"><span data-stu-id="7b883-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="7b883-123">Более высокая производительность, чем в ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="7b883-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="7b883-124">Хорошая производительность</span><span class="sxs-lookup"><span data-stu-id="7b883-124">Good performance</span></span>|
|[<span data-ttu-id="7b883-125">Использование среды выполнения .NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="7b883-126">Использование среды выполнения .NET Framework</span><span class="sxs-lookup"><span data-stu-id="7b883-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="7b883-127">Дополнительные сведения о поддержке ASP.NET Core 2.x на платформе .NET Framework см. в разделе [ASP.NET Core для платформы .NET Framework](xref:index#target-framework).</span><span class="sxs-lookup"><span data-stu-id="7b883-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="7b883-128">Сценарии ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="7b883-129">Веб-сайты</span><span class="sxs-lookup"><span data-stu-id="7b883-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="7b883-130">API-интерфейсы</span><span class="sxs-lookup"><span data-stu-id="7b883-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="7b883-131">Режим реального времени</span><span class="sxs-lookup"><span data-stu-id="7b883-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="7b883-132">Развертывание приложения ASP.NET Core в Azure</span><span class="sxs-lookup"><span data-stu-id="7b883-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="7b883-133">Сценарии ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="7b883-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="7b883-134">Веб-сайты</span><span class="sxs-lookup"><span data-stu-id="7b883-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="7b883-135">API-интерфейсы</span><span class="sxs-lookup"><span data-stu-id="7b883-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="7b883-136">Режим реального времени</span><span class="sxs-lookup"><span data-stu-id="7b883-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="7b883-137">Создание веб-приложение ASP.NET 4.x в Azure</span><span class="sxs-lookup"><span data-stu-id="7b883-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="7b883-138">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7b883-138">Additional resources</span></span>

* [<span data-ttu-id="7b883-139">Введение в ASP.NET</span><span class="sxs-lookup"><span data-stu-id="7b883-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="7b883-140">Введение в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b883-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
