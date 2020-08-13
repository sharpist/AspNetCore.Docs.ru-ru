---
title: Поддерживаемые платформы ASP.NET Core Blazor
author: guardrex
description: Сведения о поддерживаемых платформах для ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013532"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="44afc-103">Поддерживаемые платформы ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="44afc-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="44afc-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="44afc-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="44afc-105">Требования к браузеру</span><span class="sxs-lookup"><span data-stu-id="44afc-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="44afc-106">Браузер</span><span class="sxs-lookup"><span data-stu-id="44afc-106">Browser</span></span>                          | <span data-ttu-id="44afc-107">Version</span><span class="sxs-lookup"><span data-stu-id="44afc-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="44afc-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44afc-108">Microsoft Edge</span></span>                   | <span data-ttu-id="44afc-109">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-109">Current</span></span>               |
| <span data-ttu-id="44afc-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44afc-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="44afc-111">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-111">Current</span></span>               |
| <span data-ttu-id="44afc-112">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="44afc-112">Google Chrome, including Android</span></span> | <span data-ttu-id="44afc-113">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-113">Current</span></span>               |
| <span data-ttu-id="44afc-114">Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="44afc-114">Safari, including iOS</span></span>            | <span data-ttu-id="44afc-115">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-115">Current</span></span>               |
| <span data-ttu-id="44afc-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44afc-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44afc-117">Не поддерживается&dagger;</span><span class="sxs-lookup"><span data-stu-id="44afc-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="44afc-118">&dagger;Microsoft Internet Explorer не поддерживает [WebAssembly](https://webassembly.org).</span><span class="sxs-lookup"><span data-stu-id="44afc-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="44afc-119">Браузер</span><span class="sxs-lookup"><span data-stu-id="44afc-119">Browser</span></span>                          | <span data-ttu-id="44afc-120">Version</span><span class="sxs-lookup"><span data-stu-id="44afc-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="44afc-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="44afc-121">Microsoft Edge</span></span>                   | <span data-ttu-id="44afc-122">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-122">Current</span></span>    |
| <span data-ttu-id="44afc-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="44afc-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="44afc-124">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-124">Current</span></span>    |
| <span data-ttu-id="44afc-125">Google Chrome, включая Android</span><span class="sxs-lookup"><span data-stu-id="44afc-125">Google Chrome, including Android</span></span> | <span data-ttu-id="44afc-126">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-126">Current</span></span>    |
| <span data-ttu-id="44afc-127">Safari, включая iOS</span><span class="sxs-lookup"><span data-stu-id="44afc-127">Safari, including iOS</span></span>            | <span data-ttu-id="44afc-128">Текущие</span><span class="sxs-lookup"><span data-stu-id="44afc-128">Current</span></span>    |
| <span data-ttu-id="44afc-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="44afc-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="44afc-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="44afc-130">11&dagger;</span></span> |

<span data-ttu-id="44afc-131">&dagger;Требуются дополнительные заполнения (например, обещания можно добавить с помощью пакета [`Polyfill.io`](https://polyfill.io/v3/)).</span><span class="sxs-lookup"><span data-stu-id="44afc-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44afc-132">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44afc-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
