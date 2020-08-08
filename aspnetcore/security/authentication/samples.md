---
title: Примеры проверки подлинности для ASP.NET Core
author: rick-anderson
description: Содержит ссылки на примеры проверки подлинности в репозитории ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 71e614eb3d91e8425ec430d775b8a0163dc7258f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017796"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="5a671-103">Примеры проверки подлинности для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a671-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="5a671-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="5a671-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a671-105">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="5a671-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="5a671-106">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="5a671-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="5a671-107">[Cookieидентификаци](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="5a671-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="5a671-108">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="5a671-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="5a671-109">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5a671-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="5a671-110">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="5a671-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="5a671-111">Выбор между cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="5a671-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="5a671-112">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="5a671-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="5a671-113">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="5a671-113">Run the samples</span></span>

* <span data-ttu-id="5a671-114">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5a671-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="5a671-115">Например, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="5a671-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="5a671-116">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5a671-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="5a671-117">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a671-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="5a671-118">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="5a671-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a671-119">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="5a671-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="5a671-120">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="5a671-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="5a671-121">[Cookieидентификаци](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="5a671-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="5a671-122">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="5a671-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="5a671-123">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5a671-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="5a671-124">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="5a671-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="5a671-125">Выбор между cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="5a671-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="5a671-126">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="5a671-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="5a671-127">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="5a671-127">Run the samples</span></span>

* <span data-ttu-id="5a671-128">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5a671-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="5a671-129">Например, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="5a671-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="5a671-130">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="5a671-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="5a671-131">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a671-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="5a671-132">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="5a671-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
