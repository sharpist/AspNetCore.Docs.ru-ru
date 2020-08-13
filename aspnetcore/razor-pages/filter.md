---
title: Методы фильтрации для Razor Pages в ASP.NET Core
author: Rick-Anderson
description: Описание способов создания методов фильтрации Razor Pages в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: b8942020e98d0f985e5445bb1816c540bcb7829b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021410"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="6961b-103">Методы фильтрации для Razor Pages в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6961b-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6961b-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="6961b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6961b-105">Фильтры Razor Pages [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) и [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) разрешают Razor Pages выполнять код до и после запуска обработчика Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6961b-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="6961b-106">Фильтры страницы Razor похожи на [фильтры действий MVC ASP.NET Core](xref:mvc/controllers/filters#action-filters), но их нельзя применять к методам обработчика отдельной страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="6961b-107">Фильтры страниц Razor:</span><span class="sxs-lookup"><span data-stu-id="6961b-107">Razor Page filters:</span></span>

* <span data-ttu-id="6961b-108">Запускают код после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="6961b-109">Запускают код до выполнения метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="6961b-110">Запускают код после выполнения метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="6961b-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="6961b-111">Могут реализовываться глобально или на странице.</span><span class="sxs-lookup"><span data-stu-id="6961b-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="6961b-112">Не могут применяться к методам обработчика для конкретной страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="6961b-113">Могут иметь зависимости конструктора, заполняемые путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6961b-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6961b-114">Дополнительные сведения см. в описаниях атрибутов [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) и [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="6961b-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="6961b-115">Хотя конструкторы страниц и ПО промежуточного слоя позволяют выполнять пользовательский код до выполнения метода обработчика, только фильтры страницы Razor предоставляют доступ к <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> и странице.</span><span class="sxs-lookup"><span data-stu-id="6961b-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="6961b-116">ПО промежуточного слоя имеет доступ к `HttpContext`, но не к контексту страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="6961b-117">Фильтры имеют производный параметр <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext>, который предоставляет доступ к `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6961b-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="6961b-118">Вот пример фильтра страницы: [Применение атрибута фильтра](#ifa), который добавляет заголовок к ответу. Это невозможно сделать с помощью конструкторов или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6961b-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="6961b-119">Доступ к контексту страницы, который включает в себя доступ к экземплярам страницы и ее модели, возможен только при выполнении фильтров, обработчиков или текста страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="6961b-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="6961b-120">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6961b-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6961b-121">Фильтры страницы Razor предоставляют следующие методы, которые могут применяться глобально или на уровне страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="6961b-122">Синхронные методы:</span><span class="sxs-lookup"><span data-stu-id="6961b-122">Synchronous methods:</span></span>

  * <span data-ttu-id="6961b-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): вызывается после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6961b-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): вызывается до выполнения метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="6961b-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): вызывается после выполнения метода обработчика, но до результата действия.</span><span class="sxs-lookup"><span data-stu-id="6961b-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="6961b-126">Асинхронные методы:</span><span class="sxs-lookup"><span data-stu-id="6961b-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="6961b-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): вызывается асинхронно после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6961b-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): вызывается асинхронно до вызова метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="6961b-129">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="6961b-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6961b-130">Платформа сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается он.</span><span class="sxs-lookup"><span data-stu-id="6961b-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6961b-131">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="6961b-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6961b-132">Если реализуются оба интерфейса, вызываются только асинхронные методы.</span><span class="sxs-lookup"><span data-stu-id="6961b-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="6961b-133">Это же правило применяется к переопределению на страницах — реализуйте синхронную или асинхронную версию переопределения, но не обе сразу.</span><span class="sxs-lookup"><span data-stu-id="6961b-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="6961b-134">Реализация фильтров страницы Razor глобально</span><span class="sxs-lookup"><span data-stu-id="6961b-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="6961b-135">В следующем коде реализуется фильтр `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="6961b-136">В приведенном выше коде `ProcessUserAgent.Write` — это пользовательский код, который работает со строкой агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="6961b-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="6961b-137">В следующем коде включается `SampleAsyncPageFilter` в классе `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6961b-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="6961b-138">Следующий код вызывает <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> для применения `SampleAsyncPageFilter` только к страницам в */Movies*:</span><span class="sxs-lookup"><span data-stu-id="6961b-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="6961b-139">В следующем коде реализуется синхронный метод `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="6961b-140">В следующем коде включается `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="6961b-141">Реализация фильтров страницы Razor путем переопределения методов фильтра</span><span class="sxs-lookup"><span data-stu-id="6961b-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="6961b-142">В следующем коде переопределяются асинхронные фильтры страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="6961b-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="6961b-143">Реализация атрибута фильтра</span><span class="sxs-lookup"><span data-stu-id="6961b-143">Implement a filter attribute</span></span>

<span data-ttu-id="6961b-144">Встроенный фильтр на основе атрибутов <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> может быть разделен на подклассы.</span><span class="sxs-lookup"><span data-stu-id="6961b-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="6961b-145">Следующий фильтр добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="6961b-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="6961b-146">В следующем коде применяется атрибут `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="6961b-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="6961b-147">Проверьте заголовки с помощью инструментов разработчика в браузере.</span><span class="sxs-lookup"><span data-stu-id="6961b-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="6961b-148">В **заголовке ответа** отображается `author: Rick`.</span><span class="sxs-lookup"><span data-stu-id="6961b-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="6961b-149">Инструкции по переопределению порядка см. в разделе [Переопределение порядка по умолчанию](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="6961b-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="6961b-150">Инструкции по замыканию конвейера фильтров из фильтра см. в разделе [Отмена и замыкание](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="6961b-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="6961b-151">Атрибут фильтра Authorize</span><span class="sxs-lookup"><span data-stu-id="6961b-151">Authorize filter attribute</span></span>

<span data-ttu-id="6961b-152">Атрибут [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) можно применить к `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6961b-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6961b-153">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="6961b-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6961b-154">Фильтры Razor Pages [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) и [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) разрешают Razor Pages выполнять код до и после запуска обработчика Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6961b-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="6961b-155">Фильтры страницы Razor похожи на [фильтры действий MVC ASP.NET Core](xref:mvc/controllers/filters#action-filters), но их нельзя применять к методам обработчика отдельной страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="6961b-156">Фильтры страниц Razor:</span><span class="sxs-lookup"><span data-stu-id="6961b-156">Razor Page filters:</span></span>

* <span data-ttu-id="6961b-157">Запускают код после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="6961b-158">Запускают код до выполнения метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="6961b-159">Запускают код после выполнения метода обработчика.</span><span class="sxs-lookup"><span data-stu-id="6961b-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="6961b-160">Могут реализовываться глобально или на странице.</span><span class="sxs-lookup"><span data-stu-id="6961b-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="6961b-161">Не могут применяться к методам обработчика для конкретной страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="6961b-162">Код может быть запущен до выполнения метода обработчика с помощью конструктора страницы или ПО промежуточного слоя, но только фильтры страницы Razor имеют доступ к [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="6961b-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="6961b-163">Фильтры имеют производный параметр [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), который предоставляет доступ к `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="6961b-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="6961b-164">Например, образец [Применение атрибута фильтра](#ifa) добавляет заголовок к ответу. Это невозможно сделать с помощью конструкторов или ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6961b-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="6961b-165">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6961b-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6961b-166">Фильтры страницы Razor предоставляют следующие методы, которые могут применяться глобально или на уровне страницы.</span><span class="sxs-lookup"><span data-stu-id="6961b-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="6961b-167">Синхронные методы:</span><span class="sxs-lookup"><span data-stu-id="6961b-167">Synchronous methods:</span></span>

  * <span data-ttu-id="6961b-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): вызывается после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6961b-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): вызывается до выполнения метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="6961b-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): вызывается после выполнения метода обработчика, но до результата действия.</span><span class="sxs-lookup"><span data-stu-id="6961b-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="6961b-171">Асинхронные методы:</span><span class="sxs-lookup"><span data-stu-id="6961b-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="6961b-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): вызывается асинхронно после выбора метода обработчика, но до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="6961b-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): вызывается асинхронно до вызова метода обработчика, но после привязки модели.</span><span class="sxs-lookup"><span data-stu-id="6961b-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="6961b-174">Реализуйте **либо** синхронный, либо асинхронный вариант интерфейса фильтра, но не оба варианта.</span><span class="sxs-lookup"><span data-stu-id="6961b-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="6961b-175">Платформа сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается он.</span><span class="sxs-lookup"><span data-stu-id="6961b-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6961b-176">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="6961b-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6961b-177">Если реализуются оба интерфейса, вызываются только асинхронные методы.</span><span class="sxs-lookup"><span data-stu-id="6961b-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="6961b-178">Это же правило применяется к переопределению на страницах — реализуйте синхронную или асинхронную версию переопределения, но не обе сразу.</span><span class="sxs-lookup"><span data-stu-id="6961b-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="6961b-179">Реализация фильтров страницы Razor глобально</span><span class="sxs-lookup"><span data-stu-id="6961b-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="6961b-180">В следующем коде реализуется фильтр `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="6961b-181">В приведенном выше коде [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) не является обязательным.</span><span class="sxs-lookup"><span data-stu-id="6961b-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="6961b-182">Он используется в образце для предоставления сведений о трассировке для приложения.</span><span class="sxs-lookup"><span data-stu-id="6961b-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="6961b-183">В следующем коде включается `SampleAsyncPageFilter` в классе `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6961b-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="6961b-184">В следующем коде демонстрируется полный класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="6961b-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="6961b-185">Следующий код вызывает `AddFolderApplicationModelConvention` для применения `SampleAsyncPageFilter` только к страницам в */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="6961b-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="6961b-186">В следующем коде реализуется синхронный метод `IPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="6961b-187">В следующем коде включается `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="6961b-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="6961b-188">Реализация фильтров страницы Razor путем переопределения методов фильтра</span><span class="sxs-lookup"><span data-stu-id="6961b-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="6961b-189">В следующем коде переопределяются синхронные фильтры страницы Razor.</span><span class="sxs-lookup"><span data-stu-id="6961b-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="6961b-190">Реализация атрибута фильтра</span><span class="sxs-lookup"><span data-stu-id="6961b-190">Implement a filter attribute</span></span>

<span data-ttu-id="6961b-191">Встроенный фильтр на основе атрибутов [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) может быть разделен на подклассы.</span><span class="sxs-lookup"><span data-stu-id="6961b-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="6961b-192">Следующий фильтр добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="6961b-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="6961b-193">В следующем коде применяется атрибут `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="6961b-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="6961b-194">Инструкции по переопределению порядка см. в разделе [Переопределение порядка по умолчанию](xref:mvc/controllers/filters#overriding-the-default-order).</span><span class="sxs-lookup"><span data-stu-id="6961b-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="6961b-195">Инструкции по замыканию конвейера фильтров из фильтра см. в разделе [Отмена и замыкание](xref:mvc/controllers/filters#cancellation-and-short-circuiting).</span><span class="sxs-lookup"><span data-stu-id="6961b-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="6961b-196">Атрибут фильтра Authorize</span><span class="sxs-lookup"><span data-stu-id="6961b-196">Authorize filter attribute</span></span>

<span data-ttu-id="6961b-197">Атрибут [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) можно применить к `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="6961b-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
