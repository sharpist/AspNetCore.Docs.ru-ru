---
title: Примеры проверки подлинности для ASP.NET Core
author: rick-anderson
description: Содержит ссылки на примеры проверки подлинности в репозитории ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776556"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="62f34-103">Примеры проверки подлинности для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="62f34-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="62f34-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="62f34-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62f34-105">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="62f34-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="62f34-106">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="62f34-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="62f34-107">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="62f34-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="62f34-108">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="62f34-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="62f34-109">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="62f34-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="62f34-110">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="62f34-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="62f34-111">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="62f34-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="62f34-112">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="62f34-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="62f34-113">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="62f34-113">Run the samples</span></span>

* <span data-ttu-id="62f34-114">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="62f34-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="62f34-115">Например `release/3.1`.</span><span class="sxs-lookup"><span data-stu-id="62f34-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="62f34-116">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="62f34-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="62f34-117">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62f34-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="62f34-118">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="62f34-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62f34-119">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие примеры проверки подлинности в папке *AspNetCore/src/Security/Samples* :</span><span class="sxs-lookup"><span data-stu-id="62f34-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="62f34-120">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="62f34-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="62f34-121">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="62f34-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="62f34-122">Поставщик настраиваемой политики — Иаусоризатионполиципровидер</span><span class="sxs-lookup"><span data-stu-id="62f34-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="62f34-123">Схемы и параметры динамической проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="62f34-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="62f34-124">[Внешние утверждения](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="62f34-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="62f34-125">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="62f34-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="62f34-126">Разрешает доступ к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="62f34-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="62f34-127">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="62f34-127">Run the samples</span></span>

* <span data-ttu-id="62f34-128">Выберите [ветвь](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="62f34-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="62f34-129">Например `release/2.2`.</span><span class="sxs-lookup"><span data-stu-id="62f34-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="62f34-130">Клонировать или скачать [репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="62f34-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="62f34-131">Убедитесь, что установлена версия [пакет SDK для .NET Core](https://dotnet.microsoft.com/download/dotnet-core) , соответствующая клону репозитория ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="62f34-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="62f34-132">Перейдите к примеру в *AspNetCore/src/Security/Samples* и запустите пример с помощью `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="62f34-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
