---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625951"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="ac64b-103">Поддерживаемые платформы ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ac64b-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="ac64b-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="ac64b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="ac64b-105">Требования к браузеру</span><span class="sxs-lookup"><span data-stu-id="ac64b-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="ac64b-106">Браузер</span><span class="sxs-lookup"><span data-stu-id="ac64b-106">Browser</span></span>                          | <span data-ttu-id="ac64b-107">Version</span><span class="sxs-lookup"><span data-stu-id="ac64b-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="ac64b-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ac64b-108">Microsoft Edge</span></span>                   | <span data-ttu-id="ac64b-109">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-109">Current</span></span>               |
| <span data-ttu-id="ac64b-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ac64b-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="ac64b-111">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-111">Current</span></span>               |
| <span data-ttu-id="ac64b-112">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="ac64b-112">Google Chrome, including Android</span></span> | <span data-ttu-id="ac64b-113">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-113">Current</span></span>               |
| <span data-ttu-id="ac64b-114">Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="ac64b-114">Safari, including iOS</span></span>            | <span data-ttu-id="ac64b-115">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-115">Current</span></span>               |
| <span data-ttu-id="ac64b-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ac64b-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="ac64b-117">Не поддерживается&dagger;</span><span class="sxs-lookup"><span data-stu-id="ac64b-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="ac64b-118">&dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="ac64b-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="ac64b-119">Браузер</span><span class="sxs-lookup"><span data-stu-id="ac64b-119">Browser</span></span>                          | <span data-ttu-id="ac64b-120">Version</span><span class="sxs-lookup"><span data-stu-id="ac64b-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="ac64b-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ac64b-121">Microsoft Edge</span></span>                   | <span data-ttu-id="ac64b-122">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-122">Current</span></span>    |
| <span data-ttu-id="ac64b-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="ac64b-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="ac64b-124">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-124">Current</span></span>    |
| <span data-ttu-id="ac64b-125">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="ac64b-125">Google Chrome, including Android</span></span> | <span data-ttu-id="ac64b-126">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-126">Current</span></span>    |
| <span data-ttu-id="ac64b-127">Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="ac64b-127">Safari, including iOS</span></span>            | <span data-ttu-id="ac64b-128">Текущие</span><span class="sxs-lookup"><span data-stu-id="ac64b-128">Current</span></span>    |
| <span data-ttu-id="ac64b-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ac64b-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="ac64b-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="ac64b-130">11&dagger;</span></span> |

<span data-ttu-id="ac64b-131">&dagger;Требуются дополнительные заполнения (например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="ac64b-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ac64b-132">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ac64b-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
