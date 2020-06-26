---
title: Примеры проверки подлинности для ASP.NET Core
author: rick-anderson
description: Содержит ссылки на примеры проверки подлинности в репозитории ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 95915c28b132a1a48fb772b2663794754006627f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405358"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="2a5f8-103">Примеры проверки подлинности для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a5f8-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="2a5f8-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2a5f8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a5f8-105">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="2a5f8-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2a5f8-106">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="2a5f8-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2a5f8-107">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="2a5f8-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="2a5f8-108">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="2a5f8-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2a5f8-109">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2a5f8-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="2a5f8-110">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="2a5f8-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="2a5f8-111">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="2a5f8-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2a5f8-112">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="2a5f8-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2a5f8-113">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="2a5f8-113">Run the samples</span></span>

* <span data-ttu-id="2a5f8-114">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2a5f8-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="2a5f8-115">Например `release/3.1`.</span><span class="sxs-lookup"><span data-stu-id="2a5f8-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="2a5f8-116">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2a5f8-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="2a5f8-117">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a5f8-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2a5f8-118">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2a5f8-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a5f8-119">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="2a5f8-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="2a5f8-120">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="2a5f8-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="2a5f8-121">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="2a5f8-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="2a5f8-122">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="2a5f8-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="2a5f8-123">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2a5f8-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="2a5f8-124">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="2a5f8-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="2a5f8-125">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="2a5f8-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="2a5f8-126">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="2a5f8-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="2a5f8-127">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="2a5f8-127">Run the samples</span></span>

* <span data-ttu-id="2a5f8-128">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2a5f8-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="2a5f8-129">Например `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="2a5f8-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="2a5f8-130">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2a5f8-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="2a5f8-131">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a5f8-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="2a5f8-132">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2a5f8-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
