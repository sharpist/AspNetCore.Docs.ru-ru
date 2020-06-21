---
title: Фильтры в ASP.NET Core
author: Rick-Anderson
description: Из этой статьи вы узнаете, как работают фильтры и как их использовать в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122169"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="fa889-103">Фильтры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa889-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa889-104">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="fa889-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fa889-105">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="fa889-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="fa889-106">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="fa889-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="fa889-107">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="fa889-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="fa889-108">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="fa889-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="fa889-109">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="fa889-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="fa889-110">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="fa889-111">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="fa889-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="fa889-112">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="fa889-113">Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="fa889-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="fa889-114">Фильтры не работают непосредственно с [ Razor компонентами](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="fa889-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="fa889-115">Фильтр может влиять на компонент только косвенно в таких случаях:</span><span class="sxs-lookup"><span data-stu-id="fa889-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="fa889-116">Компонент внедряется в страницу или представление.</span><span class="sxs-lookup"><span data-stu-id="fa889-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="fa889-117">Страница, контроллер или представление использует фильтр.</span><span class="sxs-lookup"><span data-stu-id="fa889-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="fa889-118">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fa889-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="fa889-119">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="fa889-119">How filters work</span></span>

<span data-ttu-id="fa889-120">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="fa889-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="fa889-121">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="fa889-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с помощью прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="fa889-124">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-124">Filter types</span></span>

<span data-ttu-id="fa889-125">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="fa889-126">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="fa889-127">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="fa889-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="fa889-128">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="fa889-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="fa889-129">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-129">Run after authorization.</span></span>  
  * <span data-ttu-id="fa889-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> выполняет код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="fa889-131">Например, `OnResourceExecuting` выполняет код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="fa889-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="fa889-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> выполняет код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="fa889-133">[Фильтры действий](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="fa889-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="fa889-134">выполняют код непосредственно до и после вызова метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="fa889-135">Могут изменять аргументы, передаваемые в действие.</span><span class="sxs-lookup"><span data-stu-id="fa889-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="fa889-136">Могут изменять результат, возвращенный действием.</span><span class="sxs-lookup"><span data-stu-id="fa889-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="fa889-137">**Не** поддерживаются на Razor страницах.</span><span class="sxs-lookup"><span data-stu-id="fa889-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="fa889-138">[Фильтры исключений](#exception-filters) применяют глобальные политики к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="fa889-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="fa889-139">[Фильтры результатов](#result-filters) выполняют код непосредственно до и после выполнения результатов действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="fa889-140">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="fa889-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="fa889-141">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="fa889-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="fa889-142">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="fa889-145">Реализация</span><span class="sxs-lookup"><span data-stu-id="fa889-145">Implementation</span></span>

<span data-ttu-id="fa889-146">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="fa889-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="fa889-147">Синхронные фильтры выполняют код до и после этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="fa889-148">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="fa889-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="fa889-149">а <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="fa889-150">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="fa889-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="fa889-151">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="fa889-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="fa889-152">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="fa889-153">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-153">Multiple filter stages</span></span>

<span data-ttu-id="fa889-154">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="fa889-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="fa889-155">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует следующие интерфейсы:</span><span class="sxs-lookup"><span data-stu-id="fa889-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="fa889-156">синхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>;</span><span class="sxs-lookup"><span data-stu-id="fa889-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="fa889-157">асинхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="fa889-158">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="fa889-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="fa889-159">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="fa889-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fa889-160">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="fa889-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fa889-161">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="fa889-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="fa889-162">При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="fa889-163">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-163">Built-in filter attributes</span></span>

<span data-ttu-id="fa889-164">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="fa889-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="fa889-165">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="fa889-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-166">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="fa889-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="fa889-167">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="fa889-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="fa889-168">Для изучения заголовков используйте средство [разработчика браузера](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="fa889-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="fa889-169">В **заголовке ответа** отображается `author: Rick Anderson`.</span><span class="sxs-lookup"><span data-stu-id="fa889-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="fa889-170">В следующем коде реализуется класс `ActionFilterAttribute`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="fa889-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="fa889-171">Считывает заголовок и имя в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="fa889-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="fa889-172">В отличие от предыдущего примера, для указанного ниже кода не нужно добавлять параметры фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="fa889-173">Добавляет заголовок и имя к заголовку ответа.</span><span class="sxs-lookup"><span data-stu-id="fa889-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-174">Параметры конфигурации предоставляются из [системы конфигурации](xref:fundamentals/configuration/index) с помощью [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fa889-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fa889-175">Например, из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="fa889-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="fa889-176">В `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa889-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="fa889-177">Класс `PositionOptions` добавляется в контейнер службы с помощью области конфигурации `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="fa889-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="fa889-178">`MyActionFilterAttribute` добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="fa889-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="fa889-179">В следующем коде демонстрируется класс `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="fa889-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="fa889-180">В следующем коде применяется атрибут `MyActionFilterAttribute` к методу `Index2`:</span><span class="sxs-lookup"><span data-stu-id="fa889-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="fa889-181">В разделе **заголовки ответов**, `author: Rick Anderson` и `Editor: Joe Smith` отображается при `Sample/Index2` вызове конечной точки.</span><span class="sxs-lookup"><span data-stu-id="fa889-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="fa889-182">Следующий код применяет `MyActionFilterAttribute` и `AddHeaderAttribute` к Razor странице:</span><span class="sxs-lookup"><span data-stu-id="fa889-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="fa889-183">Фильтры не могут применяться к Razor методам обработчика страниц.</span><span class="sxs-lookup"><span data-stu-id="fa889-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="fa889-184">Их можно применять либо к Razor модели страницы, либо глобально.</span><span class="sxs-lookup"><span data-stu-id="fa889-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="fa889-185">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="fa889-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="fa889-186">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="fa889-187">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="fa889-188">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="fa889-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="fa889-189">С помощью атрибута в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="fa889-190">Атрибуты фильтра не могут применяться к Razor методам обработчика страниц.</span><span class="sxs-lookup"><span data-stu-id="fa889-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="fa889-191">Использование атрибута на контроллере или Razor странице.</span><span class="sxs-lookup"><span data-stu-id="fa889-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="fa889-192">Глобально для всех контроллеров, действий и Razor страниц, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="fa889-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="fa889-193">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="fa889-193">Default order of execution</span></span>

<span data-ttu-id="fa889-194">Если для определенного этапа конвейера имеется несколько фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fa889-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="fa889-195">Глобальные фильтры заключают в себя фильтры классов, которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="fa889-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="fa889-196">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="fa889-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="fa889-197">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-197">The filter sequence:</span></span>

* <span data-ttu-id="fa889-198">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="fa889-199">Код, *предшествующий* контроллеру и Razor фильтрам страниц.</span><span class="sxs-lookup"><span data-stu-id="fa889-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="fa889-200">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="fa889-201">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="fa889-202">Код *после* кода фильтров контроллеров и Razor страниц.</span><span class="sxs-lookup"><span data-stu-id="fa889-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="fa889-203">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="fa889-204">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="fa889-205">Последовательность</span><span class="sxs-lookup"><span data-stu-id="fa889-205">Sequence</span></span> | <span data-ttu-id="fa889-206">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-206">Filter scope</span></span> | <span data-ttu-id="fa889-207">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="fa889-208">1</span><span class="sxs-lookup"><span data-stu-id="fa889-208">1</span></span> | <span data-ttu-id="fa889-209">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-210">2</span><span class="sxs-lookup"><span data-stu-id="fa889-210">2</span></span> | <span data-ttu-id="fa889-211">Контроллер или Razor страница</span><span class="sxs-lookup"><span data-stu-id="fa889-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="fa889-212">3</span><span class="sxs-lookup"><span data-stu-id="fa889-212">3</span></span> | <span data-ttu-id="fa889-213">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-214">4</span><span class="sxs-lookup"><span data-stu-id="fa889-214">4</span></span> | <span data-ttu-id="fa889-215">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="fa889-216">5</span><span class="sxs-lookup"><span data-stu-id="fa889-216">5</span></span> | <span data-ttu-id="fa889-217">Контроллер или Razor страница</span><span class="sxs-lookup"><span data-stu-id="fa889-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="fa889-218">6</span><span class="sxs-lookup"><span data-stu-id="fa889-218">6</span></span> | <span data-ttu-id="fa889-219">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="fa889-220">Фильтры на уровне контроллера</span><span class="sxs-lookup"><span data-stu-id="fa889-220">Controller level filters</span></span>

<span data-ttu-id="fa889-221">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="fa889-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="fa889-222">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="fa889-222">These methods:</span></span>

* <span data-ttu-id="fa889-223">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="fa889-224">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="fa889-225">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="fa889-226">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="fa889-227">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="fa889-228">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="fa889-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="fa889-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="fa889-229">The `TestController`:</span></span>

* <span data-ttu-id="fa889-230">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="fa889-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="fa889-231">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="fa889-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="fa889-232">Переходит к `https://localhost:5001/Test2/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="fa889-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="fa889-233">Фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="fa889-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="fa889-234">Их **нельзя** настроить, чтобы они запускались после применения фильтров к методам.</span><span class="sxs-lookup"><span data-stu-id="fa889-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="fa889-235">Свойство Order рассматривается в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="fa889-235">Order is explained in the next section.</span></span>

<span data-ttu-id="fa889-236">Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="fa889-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="fa889-237">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="fa889-237">Overriding the default order</span></span>

<span data-ttu-id="fa889-238">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="fa889-239">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="fa889-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="fa889-240">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="fa889-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="fa889-241">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="fa889-242">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="fa889-243">Свойство `Order` задается с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="fa889-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="fa889-244">Рассмотрим два фильтра действий в следующем контроллере:</span><span class="sxs-lookup"><span data-stu-id="fa889-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="fa889-245">Глобальный фильтр добавляется в `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fa889-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="fa889-246">3 фильтра выполняются в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="fa889-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="fa889-247">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="fa889-248">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="fa889-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="fa889-249">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="fa889-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="fa889-250">Как упоминалось ранее, фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`. Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="fa889-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="fa889-251">В указанном выше коде `MySampleActionFilter` имеет глобальную область действия и запускается перед `MyAction2FilterAttribute`, у которого область действия контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="fa889-252">Чтобы `MyAction2FilterAttribute` запускался первым, задайте для свойства Order значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="fa889-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="fa889-253">Чтобы глобальный фильтр `MySampleActionFilter` запускался первым, задайте для свойства `Order` значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="fa889-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="fa889-254">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="fa889-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="fa889-255">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="fa889-256">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="fa889-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-257">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="fa889-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="fa889-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="fa889-259">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="fa889-260">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="fa889-261">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="fa889-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="fa889-262">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="fa889-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="fa889-263">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="fa889-264">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa889-264">Dependency injection</span></span>

<span data-ttu-id="fa889-265">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="fa889-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="fa889-266">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="fa889-267">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="fa889-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="fa889-268">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="fa889-268">A type-activated filter means:</span></span>

* <span data-ttu-id="fa889-269">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-269">An instance is created for each request.</span></span>
* <span data-ttu-id="fa889-270">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa889-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="fa889-271">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa889-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="fa889-272">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="fa889-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="fa889-273">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="fa889-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="fa889-274">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="fa889-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="fa889-275">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="fa889-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="fa889-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="fa889-277">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="fa889-278">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-278">Loggers are available from DI.</span></span> <span data-ttu-id="fa889-279">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="fa889-280">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="fa889-281">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="fa889-281">Logging added to filters:</span></span>

* <span data-ttu-id="fa889-282">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="fa889-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="fa889-283">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="fa889-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="fa889-284">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="fa889-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="fa889-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="fa889-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="fa889-286">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa889-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="fa889-287">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="fa889-288">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="fa889-289">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="fa889-290">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="fa889-291">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="fa889-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="fa889-292">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="fa889-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="fa889-293">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="fa889-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="fa889-294">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="fa889-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="fa889-295">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="fa889-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="fa889-296">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="fa889-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="fa889-297">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="fa889-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="fa889-298">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="fa889-299">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="fa889-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="fa889-300">TypeFilterAttribute</span></span>

<span data-ttu-id="fa889-301">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="fa889-302">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="fa889-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="fa889-303">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="fa889-304">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="fa889-305">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="fa889-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="fa889-306">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="fa889-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="fa889-307">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="fa889-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="fa889-308">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="fa889-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="fa889-309">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="fa889-310">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="fa889-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="fa889-311">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="fa889-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="fa889-312">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="fa889-312">Authorization filters</span></span>

<span data-ttu-id="fa889-313">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-313">Authorization filters:</span></span>

* <span data-ttu-id="fa889-314">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="fa889-315">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-315">Control access to action methods.</span></span>
* <span data-ttu-id="fa889-316">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="fa889-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="fa889-317">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="fa889-318">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="fa889-319">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="fa889-320">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-320">Calls the authorization system.</span></span>
* <span data-ttu-id="fa889-321">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="fa889-321">Does not authorize requests.</span></span>

<span data-ttu-id="fa889-322">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="fa889-323">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="fa889-323">The exception will not be handled.</span></span>
* <span data-ttu-id="fa889-324">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="fa889-325">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="fa889-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="fa889-326">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="fa889-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="fa889-327">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="fa889-327">Resource filters</span></span>

<span data-ttu-id="fa889-328">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="fa889-328">Resource filters:</span></span>

* <span data-ttu-id="fa889-329">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="fa889-330">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="fa889-331">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="fa889-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="fa889-332">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="fa889-333">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="fa889-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="fa889-334">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="fa889-334">Resource filter examples:</span></span>

* <span data-ttu-id="fa889-335">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="fa889-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="fa889-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="fa889-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="fa889-337">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="fa889-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="fa889-338">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="fa889-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="fa889-339">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="fa889-339">Action filters</span></span>

<span data-ttu-id="fa889-340">Фильтры действий **не** применяются к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="fa889-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="fa889-341">Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="fa889-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="fa889-342">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="fa889-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="fa889-343">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="fa889-343">Action filters:</span></span>

* <span data-ttu-id="fa889-344">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="fa889-345">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="fa889-346">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="fa889-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="fa889-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="fa889-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="fa889-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="fa889-349"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="fa889-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="fa889-351">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="fa889-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="fa889-352">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="fa889-353">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="fa889-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="fa889-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="fa889-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="fa889-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="fa889-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="fa889-357">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="fa889-357">Setting this property to null:</span></span>

  * <span data-ttu-id="fa889-358">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="fa889-359">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="fa889-360">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="fa889-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="fa889-361">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="fa889-362">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="fa889-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="fa889-363">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="fa889-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="fa889-364">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="fa889-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="fa889-365">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="fa889-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="fa889-366">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="fa889-366">Validate model state.</span></span>
* <span data-ttu-id="fa889-367">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="fa889-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="fa889-368">Контроллеры, отмеченные `[ApiController]` атрибутом, автоматически проверяют состояние модели и возвращают ответ 400.</span><span class="sxs-lookup"><span data-stu-id="fa889-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="fa889-369">Дополнительные сведения см. в разделе [Автоматические отклики HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="fa889-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="fa889-370">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="fa889-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="fa889-371">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="fa889-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="fa889-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="fa889-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="fa889-374">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="fa889-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="fa889-375">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="fa889-376">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="fa889-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="fa889-377">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="fa889-377">Exception filters</span></span>

<span data-ttu-id="fa889-378">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-378">Exception filters:</span></span>

* <span data-ttu-id="fa889-379">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="fa889-380">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="fa889-381">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="fa889-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="fa889-382">В следующем коде проверяется фильтр исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="fa889-383">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-383">Exception filters:</span></span>

* <span data-ttu-id="fa889-384">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="fa889-384">Don't have before and after events.</span></span>
* <span data-ttu-id="fa889-385">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fa889-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="fa889-386">Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="fa889-387">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="fa889-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="fa889-388">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="fa889-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="fa889-389">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="fa889-389">This stops propagation of the exception.</span></span> <span data-ttu-id="fa889-390">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="fa889-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="fa889-391">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-391">Only an action filter can do that.</span></span>

<span data-ttu-id="fa889-392">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-392">Exception filters:</span></span>

* <span data-ttu-id="fa889-393">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="fa889-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="fa889-394">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="fa889-395">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="fa889-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="fa889-396">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="fa889-397">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="fa889-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="fa889-398">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="fa889-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="fa889-399">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="fa889-399">Result filters</span></span>

<span data-ttu-id="fa889-400">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="fa889-400">Result filters:</span></span>

* <span data-ttu-id="fa889-401">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="fa889-401">Implement an interface:</span></span>
  * <span data-ttu-id="fa889-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="fa889-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="fa889-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="fa889-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="fa889-404">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="fa889-405">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="fa889-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="fa889-406">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="fa889-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="fa889-407">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="fa889-408">Действие, возвращающее представление, включает всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="fa889-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="fa889-409">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="fa889-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="fa889-410">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="fa889-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="fa889-411">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="fa889-412">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="fa889-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="fa889-413">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="fa889-414">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="fa889-415">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="fa889-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="fa889-416">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="fa889-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="fa889-417">Создание исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="fa889-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="fa889-418">предотвращает выполнение результата действия и последующих фильтров;</span><span class="sxs-lookup"><span data-stu-id="fa889-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="fa889-419">рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="fa889-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="fa889-420">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="fa889-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="fa889-421">Если ответ уже был отправлен клиенту, его нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="fa889-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="fa889-422">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="fa889-423">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="fa889-424">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="fa889-425">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="fa889-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="fa889-426">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="fa889-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="fa889-427">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="fa889-428">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="fa889-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="fa889-429">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="fa889-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="fa889-430">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="fa889-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="fa889-431">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="fa889-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="fa889-432">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="fa889-433">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="fa889-433">This includes action results produced by:</span></span>

* <span data-ttu-id="fa889-434">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="fa889-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="fa889-435">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="fa889-435">Exception filters.</span></span>

<span data-ttu-id="fa889-436">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="fa889-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="fa889-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="fa889-437">IFilterFactory</span></span>

<span data-ttu-id="fa889-438">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="fa889-439">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="fa889-440">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="fa889-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="fa889-441">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="fa889-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="fa889-442">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="fa889-443">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="fa889-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="fa889-444">В следующем коде применяется фильтр:</span><span class="sxs-lookup"><span data-stu-id="fa889-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="fa889-445">Проверьте приведенный выше код, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="fa889-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="fa889-446">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="fa889-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="fa889-447">Перейдите к `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="fa889-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="fa889-448">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="fa889-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="fa889-449">**Автор:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="fa889-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="fa889-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="fa889-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="fa889-451">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="fa889-451">**internal:** `My header`</span></span>

<span data-ttu-id="fa889-452">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="fa889-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="fa889-453">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="fa889-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="fa889-454">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="fa889-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="fa889-455">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="fa889-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="fa889-456">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="fa889-457">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="fa889-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="fa889-458">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="fa889-459">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="fa889-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="fa889-460">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="fa889-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="fa889-461">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="fa889-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="fa889-462">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="fa889-463">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="fa889-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="fa889-464">Однако фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения, а значит имеют доступ к контексту и конструкциям.</span><span class="sxs-lookup"><span data-stu-id="fa889-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="fa889-465">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="fa889-466">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="fa889-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="fa889-467">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="fa889-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="fa889-468">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="fa889-469">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="fa889-469">Next actions</span></span>

* <span data-ttu-id="fa889-470">См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="fa889-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="fa889-471">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="fa889-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa889-472">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="fa889-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fa889-473">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="fa889-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="fa889-474">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="fa889-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="fa889-475">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="fa889-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="fa889-476">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="fa889-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="fa889-477">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="fa889-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="fa889-478">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="fa889-479">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="fa889-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="fa889-480">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="fa889-481">Этот документ применяется к Razor страницам, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="fa889-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="fa889-482">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fa889-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="fa889-483">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="fa889-483">How filters work</span></span>

<span data-ttu-id="fa889-484">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="fa889-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="fa889-485">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="fa889-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с использованием прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="fa889-488">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-488">Filter types</span></span>

<span data-ttu-id="fa889-489">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="fa889-490">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="fa889-491">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="fa889-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="fa889-492">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="fa889-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="fa889-493">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-493">Run after authorization.</span></span>  
  * <span data-ttu-id="fa889-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> может выполнять код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="fa889-495">Например, `OnResourceExecuting` может выполнять код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="fa889-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="fa889-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> может выполнять код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="fa889-497">[Фильтры действий](#action-filters) могут выполнять код непосредственно до и после вызова отдельного метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="fa889-498">С их помощью можно управлять аргументами, передаваемыми в действие, и возвращаемым из него результатом.</span><span class="sxs-lookup"><span data-stu-id="fa889-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="fa889-499">Фильтры действий **не** поддерживаются на Razor страницах.</span><span class="sxs-lookup"><span data-stu-id="fa889-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="fa889-500">[Фильтры исключений](#exception-filters) служат для применения глобальных политик к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="fa889-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="fa889-501">[Фильтры результатов](#result-filters) могут выполнять код непосредственно до и после выполнения результатов отдельного действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="fa889-502">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="fa889-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="fa889-503">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="fa889-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="fa889-504">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="fa889-507">Реализация</span><span class="sxs-lookup"><span data-stu-id="fa889-507">Implementation</span></span>

<span data-ttu-id="fa889-508">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="fa889-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="fa889-509">Синхронные фильтры могут выполнять код до (`On-Stage-Executing`) и после (`On-Stage-Executed`) этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="fa889-510">Например, `OnActionExecuting` вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="fa889-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="fa889-511">а `OnActionExecuted` — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="fa889-512">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="fa889-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="fa889-513">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="fa889-514">Каждый из методов `On-Stage-ExecutionAsync` принимает `FilterType-ExecutionDelegate` для выполнения этапа конвейера фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="fa889-515">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-515">Multiple filter stages</span></span>

<span data-ttu-id="fa889-516">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="fa889-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="fa889-517">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует интерфейсы `IActionFilter` и `IResultFilter`, а также их асинхронные эквиваленты.</span><span class="sxs-lookup"><span data-stu-id="fa889-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="fa889-518">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="fa889-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="fa889-519">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="fa889-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fa889-520">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="fa889-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fa889-521">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="fa889-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="fa889-522">При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="fa889-523">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-523">Built-in filter attributes</span></span>

<span data-ttu-id="fa889-524">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="fa889-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="fa889-525">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="fa889-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-526">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="fa889-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="fa889-527">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="fa889-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="fa889-528">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="fa889-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="fa889-529">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="fa889-530">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="fa889-531">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="fa889-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="fa889-532">С помощью атрибута в действии.</span><span class="sxs-lookup"><span data-stu-id="fa889-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="fa889-533">С помощью атрибута в контроллере.</span><span class="sxs-lookup"><span data-stu-id="fa889-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="fa889-534">Глобально для всех контроллеров и действий, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="fa889-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="fa889-535">Предыдущий код глобально добавляет три фильтра с помощью коллекции [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="fa889-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="fa889-536">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="fa889-536">Default order of execution</span></span>

<span data-ttu-id="fa889-537">Если есть несколько *одинаковых* фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fa889-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="fa889-538">Глобальные фильтры заключают в себя фильтры классов,</span><span class="sxs-lookup"><span data-stu-id="fa889-538">Global filters surround class filters.</span></span> <span data-ttu-id="fa889-539">которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="fa889-539">Class filters surround method filters.</span></span>

<span data-ttu-id="fa889-540">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="fa889-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="fa889-541">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="fa889-541">The filter sequence:</span></span>

* <span data-ttu-id="fa889-542">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="fa889-543">*Предшествующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="fa889-544">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="fa889-545">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="fa889-546">*Последующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="fa889-547">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="fa889-548">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="fa889-549">Последовательность</span><span class="sxs-lookup"><span data-stu-id="fa889-549">Sequence</span></span> | <span data-ttu-id="fa889-550">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-550">Filter scope</span></span> | <span data-ttu-id="fa889-551">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="fa889-552">1</span><span class="sxs-lookup"><span data-stu-id="fa889-552">1</span></span> | <span data-ttu-id="fa889-553">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-554">2</span><span class="sxs-lookup"><span data-stu-id="fa889-554">2</span></span> | <span data-ttu-id="fa889-555">Контроллер</span><span class="sxs-lookup"><span data-stu-id="fa889-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-556">3</span><span class="sxs-lookup"><span data-stu-id="fa889-556">3</span></span> | <span data-ttu-id="fa889-557">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-558">4</span><span class="sxs-lookup"><span data-stu-id="fa889-558">4</span></span> | <span data-ttu-id="fa889-559">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="fa889-560">5</span><span class="sxs-lookup"><span data-stu-id="fa889-560">5</span></span> | <span data-ttu-id="fa889-561">Контроллер</span><span class="sxs-lookup"><span data-stu-id="fa889-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="fa889-562">6</span><span class="sxs-lookup"><span data-stu-id="fa889-562">6</span></span> | <span data-ttu-id="fa889-563">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="fa889-564">Эта последовательность показывает:</span><span class="sxs-lookup"><span data-stu-id="fa889-564">This sequence shows:</span></span>

* <span data-ttu-id="fa889-565">Фильтр метода вкладывается в фильтр контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="fa889-566">Фильтр контроллера вкладывается в глобальный фильтр.</span><span class="sxs-lookup"><span data-stu-id="fa889-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="fa889-567">RazorФильтры уровня контроллера и страницы</span><span class="sxs-lookup"><span data-stu-id="fa889-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="fa889-568">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="fa889-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="fa889-569">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="fa889-569">These methods:</span></span>

* <span data-ttu-id="fa889-570">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="fa889-571">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="fa889-572">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="fa889-573">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="fa889-574">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="fa889-575">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="fa889-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="fa889-576">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="fa889-576">The `TestController`:</span></span>

* <span data-ttu-id="fa889-577">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="fa889-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="fa889-578">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="fa889-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="fa889-579">Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="fa889-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="fa889-580">Для Razor страниц см. раздел [реализация Razor фильтров страниц путем переопределения методов фильтров](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="fa889-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="fa889-581">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="fa889-581">Overriding the default order</span></span>

<span data-ttu-id="fa889-582">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="fa889-583">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="fa889-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="fa889-584">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="fa889-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="fa889-585">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="fa889-586">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="fa889-587">Свойство `Order` можно задать с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="fa889-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="fa889-588">Рассмотрим три фильтра действий, показанные в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="fa889-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="fa889-589">Если свойство `Order` контроллера и глобальные фильтры имеют значения 1 и 2 соответственно, порядок выполнения инвертируется.</span><span class="sxs-lookup"><span data-stu-id="fa889-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="fa889-590">Последовательность</span><span class="sxs-lookup"><span data-stu-id="fa889-590">Sequence</span></span> | <span data-ttu-id="fa889-591">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-591">Filter scope</span></span> | <span data-ttu-id="fa889-592">Свойство`Order`</span><span class="sxs-lookup"><span data-stu-id="fa889-592">`Order` property</span></span> | <span data-ttu-id="fa889-593">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="fa889-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="fa889-594">1</span><span class="sxs-lookup"><span data-stu-id="fa889-594">1</span></span> | <span data-ttu-id="fa889-595">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-595">Method</span></span> | <span data-ttu-id="fa889-596">0</span><span class="sxs-lookup"><span data-stu-id="fa889-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="fa889-597">2</span><span class="sxs-lookup"><span data-stu-id="fa889-597">2</span></span> | <span data-ttu-id="fa889-598">Контроллер</span><span class="sxs-lookup"><span data-stu-id="fa889-598">Controller</span></span> | <span data-ttu-id="fa889-599">1</span><span class="sxs-lookup"><span data-stu-id="fa889-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="fa889-600">3</span><span class="sxs-lookup"><span data-stu-id="fa889-600">3</span></span> | <span data-ttu-id="fa889-601">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-601">Global</span></span> | <span data-ttu-id="fa889-602">2</span><span class="sxs-lookup"><span data-stu-id="fa889-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="fa889-603">4</span><span class="sxs-lookup"><span data-stu-id="fa889-603">4</span></span> | <span data-ttu-id="fa889-604">Глобальный</span><span class="sxs-lookup"><span data-stu-id="fa889-604">Global</span></span> | <span data-ttu-id="fa889-605">2</span><span class="sxs-lookup"><span data-stu-id="fa889-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="fa889-606">5</span><span class="sxs-lookup"><span data-stu-id="fa889-606">5</span></span> | <span data-ttu-id="fa889-607">Контроллер</span><span class="sxs-lookup"><span data-stu-id="fa889-607">Controller</span></span> | <span data-ttu-id="fa889-608">1</span><span class="sxs-lookup"><span data-stu-id="fa889-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="fa889-609">6</span><span class="sxs-lookup"><span data-stu-id="fa889-609">6</span></span> | <span data-ttu-id="fa889-610">Метод</span><span class="sxs-lookup"><span data-stu-id="fa889-610">Method</span></span> | <span data-ttu-id="fa889-611">0</span><span class="sxs-lookup"><span data-stu-id="fa889-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="fa889-612">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="fa889-613">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="fa889-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="fa889-614">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="fa889-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="fa889-615">Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="fa889-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="fa889-616">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="fa889-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="fa889-617">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="fa889-618">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="fa889-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-619">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="fa889-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="fa889-620">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="fa889-621">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="fa889-622">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="fa889-623">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="fa889-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="fa889-624">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="fa889-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="fa889-625">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="fa889-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="fa889-626">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="fa889-626">Dependency injection</span></span>

<span data-ttu-id="fa889-627">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="fa889-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="fa889-628">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="fa889-629">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="fa889-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="fa889-630">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="fa889-630">A type-activated filter means:</span></span>

* <span data-ttu-id="fa889-631">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="fa889-631">An instance is created for each request.</span></span>
* <span data-ttu-id="fa889-632">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa889-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="fa889-633">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa889-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="fa889-634">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="fa889-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="fa889-635">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="fa889-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="fa889-636">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="fa889-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="fa889-637">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="fa889-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="fa889-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="fa889-639">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="fa889-640">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-640">Loggers are available from DI.</span></span> <span data-ttu-id="fa889-641">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="fa889-642">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="fa889-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="fa889-643">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="fa889-643">Logging added to filters:</span></span>

* <span data-ttu-id="fa889-644">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="fa889-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="fa889-645">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="fa889-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="fa889-646">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="fa889-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="fa889-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="fa889-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="fa889-648">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fa889-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="fa889-649">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="fa889-650">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="fa889-651">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="fa889-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="fa889-652">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="fa889-653">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="fa889-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="fa889-654">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="fa889-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="fa889-655">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="fa889-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="fa889-656">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="fa889-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="fa889-657">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="fa889-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="fa889-658">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="fa889-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="fa889-659">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="fa889-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="fa889-660">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="fa889-661">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="fa889-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="fa889-662">TypeFilterAttribute</span></span>

<span data-ttu-id="fa889-663">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="fa889-664">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="fa889-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="fa889-665">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="fa889-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="fa889-666">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="fa889-667">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="fa889-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="fa889-668">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="fa889-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="fa889-669">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="fa889-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="fa889-670">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="fa889-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="fa889-671">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="fa889-672">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="fa889-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="fa889-673">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="fa889-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="fa889-674">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="fa889-674">Authorization filters</span></span>

<span data-ttu-id="fa889-675">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-675">Authorization filters:</span></span>

* <span data-ttu-id="fa889-676">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="fa889-677">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-677">Control access to action methods.</span></span>
* <span data-ttu-id="fa889-678">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="fa889-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="fa889-679">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="fa889-680">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="fa889-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="fa889-681">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="fa889-682">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="fa889-682">Calls the authorization system.</span></span>
* <span data-ttu-id="fa889-683">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="fa889-683">Does not authorize requests.</span></span>

<span data-ttu-id="fa889-684">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="fa889-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="fa889-685">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="fa889-685">The exception will not be handled.</span></span>
* <span data-ttu-id="fa889-686">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="fa889-687">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="fa889-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="fa889-688">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="fa889-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="fa889-689">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="fa889-689">Resource filters</span></span>

<span data-ttu-id="fa889-690">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="fa889-690">Resource filters:</span></span>

* <span data-ttu-id="fa889-691">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="fa889-692">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="fa889-693">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="fa889-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="fa889-694">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="fa889-695">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="fa889-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="fa889-696">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="fa889-696">Resource filter examples:</span></span>

* <span data-ttu-id="fa889-697">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="fa889-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="fa889-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="fa889-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="fa889-699">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="fa889-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="fa889-700">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="fa889-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="fa889-701">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="fa889-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fa889-702">Фильтры действий **не** применяются к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="fa889-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="fa889-703">Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="fa889-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="fa889-704">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="fa889-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="fa889-705">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="fa889-705">Action filters:</span></span>

* <span data-ttu-id="fa889-706">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="fa889-707">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="fa889-708">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="fa889-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="fa889-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="fa889-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="fa889-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="fa889-711"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="fa889-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="fa889-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="fa889-713">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="fa889-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="fa889-714">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="fa889-715">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="fa889-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="fa889-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="fa889-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="fa889-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="fa889-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="fa889-719">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="fa889-719">Setting this property to null:</span></span>

  * <span data-ttu-id="fa889-720">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="fa889-721">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="fa889-722">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="fa889-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="fa889-723">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="fa889-724">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="fa889-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="fa889-725">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="fa889-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="fa889-726">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="fa889-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="fa889-727">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="fa889-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="fa889-728">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="fa889-728">Validate model state.</span></span>
* <span data-ttu-id="fa889-729">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="fa889-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="fa889-730">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="fa889-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="fa889-731">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="fa889-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="fa889-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="fa889-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="fa889-734">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="fa889-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="fa889-735">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="fa889-736">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="fa889-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="fa889-737">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="fa889-737">Exception filters</span></span>

<span data-ttu-id="fa889-738">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-738">Exception filters:</span></span>

* <span data-ttu-id="fa889-739">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="fa889-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="fa889-740">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="fa889-741">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="fa889-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="fa889-742">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-742">Exception filters:</span></span>

* <span data-ttu-id="fa889-743">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="fa889-743">Don't have before and after events.</span></span>
* <span data-ttu-id="fa889-744">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="fa889-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="fa889-745">Обрабатывать необработанные исключения, происходящие при Razor создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="fa889-746">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="fa889-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="fa889-747">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="fa889-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="fa889-748">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="fa889-748">This stops propagation of the exception.</span></span> <span data-ttu-id="fa889-749">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="fa889-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="fa889-750">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-750">Only an action filter can do that.</span></span>

<span data-ttu-id="fa889-751">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="fa889-751">Exception filters:</span></span>

* <span data-ttu-id="fa889-752">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="fa889-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="fa889-753">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="fa889-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="fa889-754">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="fa889-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="fa889-755">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="fa889-756">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="fa889-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="fa889-757">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="fa889-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="fa889-758">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="fa889-758">Result filters</span></span>

<span data-ttu-id="fa889-759">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="fa889-759">Result filters:</span></span>

* <span data-ttu-id="fa889-760">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="fa889-760">Implement an interface:</span></span>
  * <span data-ttu-id="fa889-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="fa889-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="fa889-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="fa889-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="fa889-763">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="fa889-764">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="fa889-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="fa889-765">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="fa889-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="fa889-766">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="fa889-767">Действие, возвращающее представление, будет включать всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="fa889-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="fa889-768">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="fa889-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="fa889-769">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="fa889-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="fa889-770">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="fa889-771">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="fa889-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="fa889-772">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="fa889-773">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="fa889-774">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="fa889-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="fa889-775">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="fa889-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="fa889-776">Вызов исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="fa889-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="fa889-777">Предотвращает выполнение результата действия и последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="fa889-778">Рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="fa889-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="fa889-779">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="fa889-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="fa889-780">Если ответ уже был отправлен клиенту его больше нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="fa889-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="fa889-781">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="fa889-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="fa889-782">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="fa889-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="fa889-783">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов ASP.NET Core на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="fa889-784">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="fa889-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="fa889-785">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="fa889-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="fa889-786">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="fa889-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="fa889-787">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="fa889-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="fa889-788">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="fa889-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="fa889-789">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="fa889-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="fa889-790">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="fa889-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="fa889-791">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="fa889-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="fa889-792">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="fa889-792">This includes action results produced by:</span></span>

* <span data-ttu-id="fa889-793">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="fa889-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="fa889-794">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="fa889-794">Exception filters.</span></span>

<span data-ttu-id="fa889-795">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="fa889-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="fa889-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="fa889-796">IFilterFactory</span></span>

<span data-ttu-id="fa889-797">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="fa889-798">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="fa889-799">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="fa889-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="fa889-800">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="fa889-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="fa889-801">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="fa889-802">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="fa889-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="fa889-803">Приведенный выше код можно проверить, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="fa889-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="fa889-804">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="fa889-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="fa889-805">Перейдите к `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="fa889-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="fa889-806">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="fa889-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="fa889-807">**Автор:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="fa889-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="fa889-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="fa889-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="fa889-809">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="fa889-809">**internal:** `My header`</span></span>

<span data-ttu-id="fa889-810">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="fa889-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="fa889-811">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="fa889-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="fa889-812">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="fa889-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="fa889-813">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="fa889-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="fa889-814">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="fa889-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="fa889-815">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="fa889-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="fa889-816">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="fa889-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="fa889-817">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="fa889-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="fa889-818">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="fa889-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="fa889-819">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="fa889-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="fa889-820">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="fa889-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="fa889-821">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="fa889-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="fa889-822">При этом фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения ASP.NET Core, то есть они имеют доступ к контексту и конструкциям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa889-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="fa889-823">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="fa889-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="fa889-824">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="fa889-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="fa889-825">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="fa889-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="fa889-826">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="fa889-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="fa889-827">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="fa889-827">Next actions</span></span>

* <span data-ttu-id="fa889-828">См. раздел [методы фильтров для Razor страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="fa889-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="fa889-829">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="fa889-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
