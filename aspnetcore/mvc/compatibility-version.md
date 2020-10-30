---
title: Совместимая версия для ASP.NET Core MVC
author: rick-anderson
description: Сведения о том, как класс Startup в ASP.NET Core настраивает службы и конвейер запросов приложения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051295"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="ed22a-103">Совместимая версия для ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="ed22a-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="ed22a-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="ed22a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ed22a-105">Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> не предназначен для приложений ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="ed22a-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="ed22a-106">То есть вызов `SetCompatibilityVersion` с любым значением <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> не влияет на приложение.</span><span class="sxs-lookup"><span data-stu-id="ed22a-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="ed22a-107">Следующая дополнительная версия ASP.NET Core может предоставить новое значение `CompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="ed22a-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="ed22a-108">Значения `CompatibilityVersion` от `Version_2_0` до `Version_2_2` помечаются как `[Obsolete(...)]`.</span><span class="sxs-lookup"><span data-stu-id="ed22a-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="ed22a-109">См. статью [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387) (Критические изменения API в областях борьбы с фальсификацией, CORS, диагностики, MVC и маршрутизации).</span><span class="sxs-lookup"><span data-stu-id="ed22a-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="ed22a-110">Этот список содержит критические изменения для параметров совместимости.</span><span class="sxs-lookup"><span data-stu-id="ed22a-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="ed22a-111">Чтобы узнать, как `SetCompatibilityVersion` работает с приложениями ASP.NET Core 2.x, выберите [версию этой статьи для ASP.NET Core 2.2](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="ed22a-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ed22a-112">Метод <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> позволяет приложению ASP.NET Core 2.x принимать или отклонять потенциально критические изменения в поведении, появившиеся в ASP.NET Core MVC 2.1 или 2.2.</span><span class="sxs-lookup"><span data-stu-id="ed22a-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="ed22a-113">Эти потенциально критические изменения обычно затрагивают поведение подсистем MVC и способы вызова **кода** средой выполнения.</span><span class="sxs-lookup"><span data-stu-id="ed22a-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="ed22a-114">Если принять эти изменения, вы сможете использовать актуальное поведение, которое сохранится в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ed22a-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="ed22a-115">Следующий код задает режим совместимости в ASP.NET Core 2.2:</span><span class="sxs-lookup"><span data-stu-id="ed22a-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="ed22a-116">Мы рекомендуем протестировать приложение с помощью последней версии (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="ed22a-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="ed22a-117">Мы полагаем, что в большинстве приложений использование последней версии не приведет к критически важным изменениям в поведении.</span><span class="sxs-lookup"><span data-stu-id="ed22a-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="ed22a-118">Приложения, вызывающие `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`, защищены от потенциальных критически важных изменений в поведении, появившихся в версиях MVC ASP.NET Core 2.1/2.2.</span><span class="sxs-lookup"><span data-stu-id="ed22a-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="ed22a-119">Особенности защиты:</span><span class="sxs-lookup"><span data-stu-id="ed22a-119">This protection:</span></span>

* <span data-ttu-id="ed22a-120">Не применяется ко всем изменениям в версии 2.1 и более поздних версиях. Она направлена только на потенциально критические изменения в поведении среды выполнения ASP.NET Core в подсистеме MVC.</span><span class="sxs-lookup"><span data-stu-id="ed22a-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="ed22a-121">Не распространяется на ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="ed22a-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="ed22a-122">По умолчанию для приложений ASP.NET Core 2.1 и 2.2, которые **не** вызывают `SetCompatibilityVersion`, предусмотрена совместимость с 2.0.</span><span class="sxs-lookup"><span data-stu-id="ed22a-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="ed22a-123">Отсутствие вызова `SetCompatibilityVersion` равноценно вызову `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="ed22a-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="ed22a-124">Следующий код задает режим совместимости в ASP.NET Core 2.2, за исключением следующего поведения:</span><span class="sxs-lookup"><span data-stu-id="ed22a-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="ed22a-125">Если в приложении возникают критические изменения в поведении, использование параметров совместимости имеет следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="ed22a-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="ed22a-126">Позволяет использовать последний выпуск и отказаться от конкретных критических изменений.</span><span class="sxs-lookup"><span data-stu-id="ed22a-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="ed22a-127">Дает вам время обновить приложение, чтобы оно работало с последними изменениями.</span><span class="sxs-lookup"><span data-stu-id="ed22a-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="ed22a-128">В документации по <xref:Microsoft.AspNetCore.Mvc.MvcOptions> подробно объясняется, что изменилось и почему изменения выгодны для большинства пользователей.</span><span class="sxs-lookup"><span data-stu-id="ed22a-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="ed22a-129">Для ASP.NET Core 3.0 старые варианты поведения, поддерживаемые параметрами совместимости, были удалены.</span><span class="sxs-lookup"><span data-stu-id="ed22a-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="ed22a-130">Мы думаем, что это полезные изменения почти для всех пользователей.</span><span class="sxs-lookup"><span data-stu-id="ed22a-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="ed22a-131">С внедрением этих изменений в 2.1 и 2.2 большинство приложений могут сразу воспользоваться преимуществами, в то время как для обновления других требуется время.</span><span class="sxs-lookup"><span data-stu-id="ed22a-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end