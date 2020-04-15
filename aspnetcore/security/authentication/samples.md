---
title: Образцы аутентификации для ASP.NET ядра
author: rick-anderson
description: Предоставляет ссылки на образцы аутентификации в репозитории ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308219"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="62b44-103">Образцы аутентификации для ASP.NET ядра</span><span class="sxs-lookup"><span data-stu-id="62b44-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="62b44-104">Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="62b44-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="62b44-105">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие образцы аутентификации в папке *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="62b44-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="62b44-106">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="62b44-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="62b44-107">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="62b44-107">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [<span data-ttu-id="62b44-108">Поставщик пользовательских полисов - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="62b44-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="62b44-109">Динамические схемы проверки подлинности и варианты</span><span class="sxs-lookup"><span data-stu-id="62b44-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="62b44-110">Внешние претензии</span><span class="sxs-lookup"><span data-stu-id="62b44-110">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="62b44-111">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="62b44-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="62b44-112">Ограничение доступа к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="62b44-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="62b44-113">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="62b44-113">Run the samples</span></span>

* <span data-ttu-id="62b44-114">Выберите [ветку.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="62b44-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="62b44-115">Например: `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="62b44-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="62b44-116">Клон или скачать [репозиторий core ASP.NET.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="62b44-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="62b44-117">Убедитесь, что вы установили версию [.NET Core SDK,](https://dotnet.microsoft.com/download/dotnet-core) соответствующую клону ASP.NET репозитория Core.</span><span class="sxs-lookup"><span data-stu-id="62b44-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="62b44-118">Перейдите к образцу в *AspNetCore/src/Security/samples* и запустите образец с `dotnet run`помощью .</span><span class="sxs-lookup"><span data-stu-id="62b44-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="62b44-119">[Репозиторий ASP.NET Core](https://github.com/dotnet/AspNetCore) содержит следующие образцы аутентификации в папке *AspNetCore/src/Security/samples:*</span><span class="sxs-lookup"><span data-stu-id="62b44-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="62b44-120">Преобразование утверждений</span><span class="sxs-lookup"><span data-stu-id="62b44-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [<span data-ttu-id="62b44-121">Проверка подлинности файлов cookie</span><span class="sxs-lookup"><span data-stu-id="62b44-121">Cookie authentication</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [<span data-ttu-id="62b44-122">Поставщик пользовательских полисов - IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="62b44-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="62b44-123">Динамические схемы проверки подлинности и варианты</span><span class="sxs-lookup"><span data-stu-id="62b44-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [<span data-ttu-id="62b44-124">Внешние претензии</span><span class="sxs-lookup"><span data-stu-id="62b44-124">External claims</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [<span data-ttu-id="62b44-125">Выбор между файлами cookie и другой схемой проверки подлинности на основе запроса</span><span class="sxs-lookup"><span data-stu-id="62b44-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="62b44-126">Ограничение доступа к статическим файлам</span><span class="sxs-lookup"><span data-stu-id="62b44-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="62b44-127">Запуск шаблонов</span><span class="sxs-lookup"><span data-stu-id="62b44-127">Run the samples</span></span>

* <span data-ttu-id="62b44-128">Выберите [ветку.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="62b44-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="62b44-129">Например: `release/2.2`</span><span class="sxs-lookup"><span data-stu-id="62b44-129">For example, `release/2.2`</span></span>
* <span data-ttu-id="62b44-130">Клон или скачать [репозиторий core ASP.NET.](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="62b44-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="62b44-131">Убедитесь, что вы установили версию [.NET Core SDK,](https://dotnet.microsoft.com/download/dotnet-core) соответствующую клону ASP.NET репозитория Core.</span><span class="sxs-lookup"><span data-stu-id="62b44-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="62b44-132">Перейдите к образцу в *AspNetCore/src/Security/samples* и запустите образец с `dotnet run`помощью .</span><span class="sxs-lookup"><span data-stu-id="62b44-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
