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
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776790"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="d133e-103">Фильтры в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d133e-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d133e-104">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="d133e-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d133e-105">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="d133e-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d133e-106">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="d133e-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d133e-107">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="d133e-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d133e-108">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="d133e-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d133e-109">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="d133e-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d133e-110">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d133e-111">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="d133e-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="d133e-112">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d133e-113">Этот документ применим к Razor Pages, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="d133e-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="d133e-114">Фильтры не работают непосредственно с [компонентами Razor](xref:blazor/components).</span><span class="sxs-lookup"><span data-stu-id="d133e-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="d133e-115">Фильтр может влиять на компонент только косвенно в таких случаях:</span><span class="sxs-lookup"><span data-stu-id="d133e-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="d133e-116">Компонент внедряется в страницу или представление.</span><span class="sxs-lookup"><span data-stu-id="d133e-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="d133e-117">Страница, контроллер или представление использует фильтр.</span><span class="sxs-lookup"><span data-stu-id="d133e-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="d133e-118">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d133e-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d133e-119">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="d133e-119">How filters work</span></span>

<span data-ttu-id="d133e-120">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="d133e-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="d133e-121">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="d133e-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с помощью прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d133e-124">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-124">Filter types</span></span>

<span data-ttu-id="d133e-125">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d133e-126">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d133e-127">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="d133e-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="d133e-128">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="d133e-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d133e-129">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-129">Run after authorization.</span></span>  
  * <span data-ttu-id="d133e-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> выполняет код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d133e-131">Например, `OnResourceExecuting` выполняет код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="d133e-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="d133e-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> выполняет код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d133e-133">[Фильтры действий](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="d133e-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="d133e-134">выполняют код непосредственно до и после вызова метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="d133e-135">Могут изменять аргументы, передаваемые в действие.</span><span class="sxs-lookup"><span data-stu-id="d133e-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="d133e-136">Могут изменять результат, возвращенный действием.</span><span class="sxs-lookup"><span data-stu-id="d133e-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="d133e-137">**Не** поддерживаются в Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d133e-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d133e-138">[Фильтры исключений](#exception-filters) применяют глобальные политики к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="d133e-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="d133e-139">[Фильтры результатов](#result-filters) выполняют код непосредственно до и после выполнения результатов действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="d133e-140">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="d133e-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d133e-141">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="d133e-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d133e-142">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d133e-145">Реализация</span><span class="sxs-lookup"><span data-stu-id="d133e-145">Implementation</span></span>

<span data-ttu-id="d133e-146">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="d133e-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d133e-147">Синхронные фильтры выполняют код до и после этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="d133e-148">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="d133e-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="d133e-149">а <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d133e-150">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="d133e-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="d133e-151">Например, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="d133e-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d133e-152">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d133e-153">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-153">Multiple filter stages</span></span>

<span data-ttu-id="d133e-154">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="d133e-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d133e-155">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует следующие интерфейсы:</span><span class="sxs-lookup"><span data-stu-id="d133e-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="d133e-156">синхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>;</span><span class="sxs-lookup"><span data-stu-id="d133e-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="d133e-157">асинхронные: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="d133e-158">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="d133e-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d133e-159">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="d133e-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d133e-160">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="d133e-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d133e-161">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="d133e-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d133e-162">При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d133e-163">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-163">Built-in filter attributes</span></span>

<span data-ttu-id="d133e-164">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="d133e-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d133e-165">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="d133e-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-166">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="d133e-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d133e-167">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="d133e-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="d133e-168">Проверьте заголовки с помощью [инструментов разработчика для браузера](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools).</span><span class="sxs-lookup"><span data-stu-id="d133e-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="d133e-169">В разделе `author: Rick Anderson` **заголовки ответов**отображается.</span><span class="sxs-lookup"><span data-stu-id="d133e-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="d133e-170">В следующем коде реализуется класс `ActionFilterAttribute`, который выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="d133e-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="d133e-171">Считывает заголовок и имя в системе конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d133e-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="d133e-172">В отличие от предыдущего примера, для указанного ниже кода не нужно добавлять параметры фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="d133e-173">Добавляет заголовок и имя к заголовку ответа.</span><span class="sxs-lookup"><span data-stu-id="d133e-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-174">Параметры конфигурации предоставляются из [системы конфигурации](xref:fundamentals/configuration/index) с помощью [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="d133e-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d133e-175">Например, из файла *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d133e-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="d133e-176">В `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d133e-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="d133e-177">Класс `PositionOptions` добавляется в контейнер службы с помощью области конфигурации `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="d133e-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="d133e-178">`MyActionFilterAttribute` добавляется в контейнер службы.</span><span class="sxs-lookup"><span data-stu-id="d133e-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="d133e-179">В следующем коде демонстрируется класс `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="d133e-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="d133e-180">В следующем коде применяется атрибут `MyActionFilterAttribute` к методу `Index2`:</span><span class="sxs-lookup"><span data-stu-id="d133e-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="d133e-181">В разделе `author: Rick Anderson` **заголовки ответов**, и `Editor: Joe Smith` отображается при вызове `Sample/Index2` конечной точки.</span><span class="sxs-lookup"><span data-stu-id="d133e-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="d133e-182">В следующем коде применяется атрибут `MyActionFilterAttribute` и `AddHeaderAttribute` к Razor Page:</span><span class="sxs-lookup"><span data-stu-id="d133e-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d133e-183">К методам обработчика Razor Page нельзя применить фильтры.</span><span class="sxs-lookup"><span data-stu-id="d133e-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="d133e-184">Их можно применить глобально или к модели Razor Page.</span><span class="sxs-lookup"><span data-stu-id="d133e-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="d133e-185">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="d133e-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d133e-186">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d133e-187">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="d133e-188">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="d133e-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d133e-189">С помощью атрибута в действии контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="d133e-190">Атрибуты фильтра нельзя применить к методам обработчика Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d133e-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="d133e-191">С помощью атрибута в контроллере или Razor Page.</span><span class="sxs-lookup"><span data-stu-id="d133e-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="d133e-192">Глобально для всех контроллеров, действий и Razor Pages, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="d133e-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="d133e-193">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d133e-193">Default order of execution</span></span>

<span data-ttu-id="d133e-194">Если для определенного этапа конвейера имеется несколько фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d133e-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d133e-195">Глобальные фильтры заключают в себя фильтры классов, которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="d133e-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="d133e-196">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="d133e-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d133e-197">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-197">The filter sequence:</span></span>

* <span data-ttu-id="d133e-198">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d133e-199">*Предшествующий* код фильтров контроллера и Razor Page.</span><span class="sxs-lookup"><span data-stu-id="d133e-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="d133e-200">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d133e-201">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d133e-202">*Последующий* код фильтров контроллера и Razor Page.</span><span class="sxs-lookup"><span data-stu-id="d133e-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="d133e-203">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d133e-204">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d133e-205">Последовательность</span><span class="sxs-lookup"><span data-stu-id="d133e-205">Sequence</span></span> | <span data-ttu-id="d133e-206">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-206">Filter scope</span></span> | <span data-ttu-id="d133e-207">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d133e-208">1</span><span class="sxs-lookup"><span data-stu-id="d133e-208">1</span></span> | <span data-ttu-id="d133e-209">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-210">2</span><span class="sxs-lookup"><span data-stu-id="d133e-210">2</span></span> | <span data-ttu-id="d133e-211">Контроллер или Razor Page</span><span class="sxs-lookup"><span data-stu-id="d133e-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="d133e-212">3</span><span class="sxs-lookup"><span data-stu-id="d133e-212">3</span></span> | <span data-ttu-id="d133e-213">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-214">4</span><span class="sxs-lookup"><span data-stu-id="d133e-214">4</span></span> | <span data-ttu-id="d133e-215">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d133e-216">5</span><span class="sxs-lookup"><span data-stu-id="d133e-216">5</span></span> | <span data-ttu-id="d133e-217">Контроллер или Razor Page</span><span class="sxs-lookup"><span data-stu-id="d133e-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d133e-218">6</span><span class="sxs-lookup"><span data-stu-id="d133e-218">6</span></span> | <span data-ttu-id="d133e-219">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="d133e-220">Фильтры на уровне контроллера</span><span class="sxs-lookup"><span data-stu-id="d133e-220">Controller level filters</span></span>

<span data-ttu-id="d133e-221">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="d133e-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d133e-222">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="d133e-222">These methods:</span></span>

* <span data-ttu-id="d133e-223">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d133e-224">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d133e-225">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d133e-226">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d133e-227">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d133e-228">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="d133e-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d133e-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="d133e-229">The `TestController`:</span></span>

* <span data-ttu-id="d133e-230">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="d133e-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d133e-231">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="d133e-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="d133e-232">Переходит к `https://localhost:5001/Test2/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="d133e-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d133e-233">Фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="d133e-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="d133e-234">Их **нельзя** настроить, чтобы они запускались после применения фильтров к методам.</span><span class="sxs-lookup"><span data-stu-id="d133e-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="d133e-235">Свойство Order рассматривается в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="d133e-235">Order is explained in the next section.</span></span>

<span data-ttu-id="d133e-236">См. подробнее о [реализации фильтров страницы Razor путем переопределения методов фильтра](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="d133e-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d133e-237">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d133e-237">Overriding the default order</span></span>

<span data-ttu-id="d133e-238">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d133e-239">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="d133e-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d133e-240">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="d133e-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d133e-241">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d133e-242">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d133e-243">Свойство `Order` задается с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="d133e-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="d133e-244">Рассмотрим два фильтра действий в следующем контроллере:</span><span class="sxs-lookup"><span data-stu-id="d133e-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="d133e-245">Глобальный фильтр добавляется в `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d133e-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="d133e-246">3 фильтра выполняются в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="d133e-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="d133e-247">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d133e-248">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="d133e-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d133e-249">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="d133e-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d133e-250">Как упоминалось ранее, фильтры на уровне контроллера задают для свойства [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) значение `int.MinValue`. Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="d133e-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="d133e-251">В указанном выше коде `MySampleActionFilter` имеет глобальную область действия и запускается перед `MyAction2FilterAttribute`, у которого область действия контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="d133e-252">Чтобы `MyAction2FilterAttribute` запускался первым, задайте для свойства Order значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="d133e-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="d133e-253">Чтобы глобальный фильтр `MySampleActionFilter` запускался первым, задайте для свойства `Order` значение `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="d133e-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d133e-254">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="d133e-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d133e-255">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d133e-256">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="d133e-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-257">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="d133e-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d133e-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d133e-259">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d133e-260">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d133e-261">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="d133e-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d133e-262">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="d133e-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d133e-263">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="d133e-264">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="d133e-264">Dependency injection</span></span>

<span data-ttu-id="d133e-265">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="d133e-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d133e-266">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d133e-267">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="d133e-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d133e-268">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="d133e-268">A type-activated filter means:</span></span>

* <span data-ttu-id="d133e-269">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-269">An instance is created for each request.</span></span>
* <span data-ttu-id="d133e-270">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d133e-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d133e-271">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d133e-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d133e-272">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="d133e-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d133e-273">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="d133e-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d133e-274">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="d133e-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d133e-275">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d133e-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="d133e-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d133e-277">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d133e-278">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-278">Loggers are available from DI.</span></span> <span data-ttu-id="d133e-279">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d133e-280">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d133e-281">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="d133e-281">Logging added to filters:</span></span>

* <span data-ttu-id="d133e-282">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="d133e-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d133e-283">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="d133e-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d133e-284">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="d133e-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d133e-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d133e-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="d133e-286">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d133e-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d133e-287">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d133e-288">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d133e-289">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="d133e-290">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d133e-291">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d133e-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d133e-292">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="d133e-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d133e-293">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="d133e-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d133e-294">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="d133e-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d133e-295">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="d133e-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d133e-296">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="d133e-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d133e-297">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="d133e-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d133e-298">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d133e-299">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d133e-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d133e-300">TypeFilterAttribute</span></span>

<span data-ttu-id="d133e-301">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d133e-302">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d133e-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d133e-303">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d133e-304">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d133e-305">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="d133e-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d133e-306">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="d133e-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d133e-307">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d133e-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d133e-308">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="d133e-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d133e-309">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d133e-310">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="d133e-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d133e-311">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="d133e-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d133e-312">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="d133e-312">Authorization filters</span></span>

<span data-ttu-id="d133e-313">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-313">Authorization filters:</span></span>

* <span data-ttu-id="d133e-314">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d133e-315">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-315">Control access to action methods.</span></span>
* <span data-ttu-id="d133e-316">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="d133e-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="d133e-317">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d133e-318">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d133e-319">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="d133e-320">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-320">Calls the authorization system.</span></span>
* <span data-ttu-id="d133e-321">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="d133e-321">Does not authorize requests.</span></span>

<span data-ttu-id="d133e-322">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d133e-323">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="d133e-323">The exception will not be handled.</span></span>
* <span data-ttu-id="d133e-324">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d133e-325">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="d133e-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d133e-326">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="d133e-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d133e-327">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="d133e-327">Resource filters</span></span>

<span data-ttu-id="d133e-328">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="d133e-328">Resource filters:</span></span>

* <span data-ttu-id="d133e-329">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d133e-330">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d133e-331">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="d133e-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d133e-332">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d133e-333">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="d133e-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d133e-334">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="d133e-334">Resource filter examples:</span></span>

* <span data-ttu-id="d133e-335">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="d133e-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d133e-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="d133e-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d133e-337">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="d133e-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d133e-338">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="d133e-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d133e-339">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="d133e-339">Action filters</span></span>

<span data-ttu-id="d133e-340">Фильтры действий **неприменимы** к Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d133e-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="d133e-341">Razor Pages поддерживает <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d133e-342">Дополнительные сведения см. в разделе [Методы фильтрации для Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d133e-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d133e-343">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="d133e-343">Action filters:</span></span>

* <span data-ttu-id="d133e-344">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d133e-345">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d133e-346">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="d133e-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d133e-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="d133e-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d133e-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="d133e-349"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d133e-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d133e-351">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="d133e-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d133e-352">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d133e-353">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="d133e-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d133e-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="d133e-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d133e-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d133e-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d133e-357">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="d133e-357">Setting this property to null:</span></span>

  * <span data-ttu-id="d133e-358">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d133e-359">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d133e-360">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="d133e-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d133e-361">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d133e-362">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="d133e-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d133e-363">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="d133e-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d133e-364">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="d133e-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d133e-365">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="d133e-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d133e-366">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="d133e-366">Validate model state.</span></span>
* <span data-ttu-id="d133e-367">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="d133e-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-368">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="d133e-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d133e-369">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="d133e-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d133e-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d133e-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d133e-372">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="d133e-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d133e-373">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d133e-374">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="d133e-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d133e-375">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="d133e-375">Exception filters</span></span>

<span data-ttu-id="d133e-376">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-376">Exception filters:</span></span>

* <span data-ttu-id="d133e-377">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="d133e-378">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d133e-379">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="d133e-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d133e-380">В следующем коде проверяется фильтр исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="d133e-381">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-381">Exception filters:</span></span>

* <span data-ttu-id="d133e-382">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="d133e-382">Don't have before and after events.</span></span>
* <span data-ttu-id="d133e-383">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d133e-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d133e-384">Обрабатывают необработанные исключения, которые возникают при создании страницы Razor или контроллера, в [привязке модели](xref:mvc/models/model-binding), фильтрах действий или методах действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d133e-385">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="d133e-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d133e-386">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="d133e-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d133e-387">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="d133e-387">This stops propagation of the exception.</span></span> <span data-ttu-id="d133e-388">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="d133e-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d133e-389">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-389">Only an action filter can do that.</span></span>

<span data-ttu-id="d133e-390">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-390">Exception filters:</span></span>

* <span data-ttu-id="d133e-391">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="d133e-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d133e-392">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d133e-393">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="d133e-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d133e-394">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d133e-395">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="d133e-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d133e-396">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="d133e-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d133e-397">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="d133e-397">Result filters</span></span>

<span data-ttu-id="d133e-398">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="d133e-398">Result filters:</span></span>

* <span data-ttu-id="d133e-399">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="d133e-399">Implement an interface:</span></span>
  * <span data-ttu-id="d133e-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d133e-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d133e-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d133e-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d133e-402">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d133e-403">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d133e-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d133e-404">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="d133e-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d133e-405">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d133e-406">Действие, возвращающее представление, включает всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="d133e-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d133e-407">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="d133e-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d133e-408">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="d133e-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d133e-409">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d133e-410">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="d133e-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="d133e-411">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d133e-412">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d133e-413">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="d133e-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d133e-414">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="d133e-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d133e-415">Создание исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="d133e-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="d133e-416">предотвращает выполнение результата действия и последующих фильтров;</span><span class="sxs-lookup"><span data-stu-id="d133e-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d133e-417">рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="d133e-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d133e-418">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="d133e-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="d133e-419">Если ответ уже был отправлен клиенту, его нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="d133e-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="d133e-420">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d133e-421">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d133e-422">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="d133e-423">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="d133e-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d133e-424">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="d133e-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d133e-425">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d133e-426">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="d133e-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d133e-427">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="d133e-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d133e-428">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="d133e-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d133e-429">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d133e-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d133e-430">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d133e-431">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="d133e-431">This includes action results produced by:</span></span>

* <span data-ttu-id="d133e-432">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="d133e-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d133e-433">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="d133e-433">Exception filters.</span></span>

<span data-ttu-id="d133e-434">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="d133e-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="d133e-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d133e-435">IFilterFactory</span></span>

<span data-ttu-id="d133e-436">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d133e-437">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d133e-438">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="d133e-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d133e-439">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="d133e-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d133e-440">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d133e-441">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="d133e-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d133e-442">В следующем коде применяется фильтр:</span><span class="sxs-lookup"><span data-stu-id="d133e-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="d133e-443">Проверьте приведенный выше код, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="d133e-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="d133e-444">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="d133e-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d133e-445">Перейдите на страницу `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="d133e-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d133e-446">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="d133e-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d133e-447">**Автор:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="d133e-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="d133e-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d133e-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d133e-449">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="d133e-449">**internal:** `My header`</span></span>

<span data-ttu-id="d133e-450">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="d133e-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d133e-451">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="d133e-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d133e-452">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="d133e-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d133e-453">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="d133e-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="d133e-454">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d133e-455">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="d133e-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d133e-456">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d133e-457">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="d133e-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d133e-458">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="d133e-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d133e-459">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="d133e-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d133e-460">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d133e-461">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="d133e-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d133e-462">Однако фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения, а значит имеют доступ к контексту и конструкциям.</span><span class="sxs-lookup"><span data-stu-id="d133e-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="d133e-463">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d133e-464">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="d133e-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d133e-465">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="d133e-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d133e-466">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d133e-467">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="d133e-467">Next actions</span></span>

* <span data-ttu-id="d133e-468">Дополнительные сведения см. в статье [Filter methods for Razor Pages in ASP.NET Core](xref:razor-pages/filter) (Методы фильтрации для Razor Pages в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="d133e-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d133e-469">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="d133e-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d133e-470">Авторы: [Кирк Ларкин](https://github.com/serpent5) (Kirk Larkin) [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Том Дайкстра](https://github.com/tdykstra/) (Tom Dykstra) и [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="d133e-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d133e-471">*Фильтры* в ASP.NET Core позволяют выполнять код до или после определенных этапов в конвейере обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="d133e-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d133e-472">Встроенные фильтры обрабатывают следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="d133e-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d133e-473">Авторизация (предотвращение несанкционированного доступа к ресурсам).</span><span class="sxs-lookup"><span data-stu-id="d133e-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d133e-474">Кэширование откликов (замыкание конвейера обработки запросов для возврата кэшированного ответа).</span><span class="sxs-lookup"><span data-stu-id="d133e-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d133e-475">Для обработки сквозной функциональности можно создавать настраиваемые фильтры.</span><span class="sxs-lookup"><span data-stu-id="d133e-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d133e-476">Примеры сквозной функциональности включают обработку ошибок, кэширование, конфигурирование, авторизацию и ведение журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d133e-477">Фильтры предотвращают дублирование кода.</span><span class="sxs-lookup"><span data-stu-id="d133e-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="d133e-478">Например, можно объединить обработку ошибок с помощью фильтра исключений обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d133e-479">Этот документ применим к Razor Pages, контроллерам API и контроллерам с представлениями.</span><span class="sxs-lookup"><span data-stu-id="d133e-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="d133e-480">[Просмотреть или скачать пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([как скачивать](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d133e-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d133e-481">Как работают фильтры</span><span class="sxs-lookup"><span data-stu-id="d133e-481">How filters work</span></span>

<span data-ttu-id="d133e-482">Фильтры выполняются в *конвейере вызова действий ASP.NET Core*, который иногда называют *конвейером фильтров*.</span><span class="sxs-lookup"><span data-stu-id="d133e-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="d133e-483">Конвейер фильтров запускается после того, как платформа ASP.NET Core выбирает выполняемое действие.</span><span class="sxs-lookup"><span data-stu-id="d133e-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Запрос обрабатывается с использованием прочего ПО промежуточного слоя, ПО промежуточного слоя маршрутизации, выбора действия и конвейера вызова действий ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d133e-486">Типы фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-486">Filter types</span></span>

<span data-ttu-id="d133e-487">Фильтр каждого типа выполняется на определенном этапе конвейера фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d133e-488">[Фильтры авторизации](#authorization-filters). Применяются в первую очередь и служат для определения того, авторизован ли пользователь для выполнения запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d133e-489">Эти фильтры могут сократить выполнение конвейера, если запрос не авторизован.</span><span class="sxs-lookup"><span data-stu-id="d133e-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="d133e-490">[Фильтры ресурсов](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="d133e-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d133e-491">Выполняются после авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-491">Run after authorization.</span></span>  
  * <span data-ttu-id="d133e-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> может выполнять код до остальной части конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d133e-493">Например, `OnResourceExecuting` может выполнять код до привязки модели.</span><span class="sxs-lookup"><span data-stu-id="d133e-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="d133e-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> может выполнять код после завершения остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d133e-495">[Фильтры действий](#action-filters) могут выполнять код непосредственно до и после вызова отдельного метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="d133e-496">С их помощью можно управлять аргументами, передаваемыми в действие, и возвращаемым из него результатом.</span><span class="sxs-lookup"><span data-stu-id="d133e-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="d133e-497">Фильтры действий **не** поддерживаются в Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d133e-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d133e-498">[Фильтры исключений](#exception-filters) служат для применения глобальных политик к необработанным исключениям, которые происходят до записи данных в тело ответа.</span><span class="sxs-lookup"><span data-stu-id="d133e-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="d133e-499">[Фильтры результатов](#result-filters) могут выполнять код непосредственно до и после выполнения результатов отдельного действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="d133e-500">Они выполняются только в том случае, если метод действия выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="d133e-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d133e-501">Они полезны для логики, которая должна включать исключения для представлений или модуля форматирования.</span><span class="sxs-lookup"><span data-stu-id="d133e-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d133e-502">На приведенной ниже схеме показано, как типы фильтров взаимодействуют друг с другом в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Запрос обрабатывается посредством фильтров авторизации, фильтров ресурсов, привязки модели, фильтров действий, выполнения действия и преобразования результата действия, фильтров исключений, фильтров результатов и выполнения результатов.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d133e-505">Реализация</span><span class="sxs-lookup"><span data-stu-id="d133e-505">Implementation</span></span>

<span data-ttu-id="d133e-506">Фильтры поддерживают как синхронные, так и асинхронные реализации посредством различных определений интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="d133e-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d133e-507">Синхронные фильтры могут выполнять код до (`On-Stage-Executing`) и после (`On-Stage-Executed`) этапа конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="d133e-508">Например, `OnActionExecuting` вызывается перед вызовом метода действия,</span><span class="sxs-lookup"><span data-stu-id="d133e-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="d133e-509">а `OnActionExecuted` — после возврата метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d133e-510">Асинхронные фильтры определяют метод `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="d133e-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d133e-511">В приведенном выше коде `SampleAsyncActionFilter` включает <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) для выполнения метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="d133e-512">Каждый из методов `On-Stage-ExecutionAsync` принимает `FilterType-ExecutionDelegate` для выполнения этапа конвейера фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d133e-513">Несколько этапов фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-513">Multiple filter stages</span></span>

<span data-ttu-id="d133e-514">Реализовать интерфейсы для нескольких этапов фильтра можно в одном классе.</span><span class="sxs-lookup"><span data-stu-id="d133e-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d133e-515">Например, класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> реализует интерфейсы `IActionFilter` и `IResultFilter`, а также их асинхронные эквиваленты.</span><span class="sxs-lookup"><span data-stu-id="d133e-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="d133e-516">Реализуйте синхронный **или** асинхронный интерфейс фильтра, но **не** оба варианта.</span><span class="sxs-lookup"><span data-stu-id="d133e-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d133e-517">Среда выполнения сначала проверяет, реализует ли фильтр асинхронный интерфейс. Если да, вызывается именно он.</span><span class="sxs-lookup"><span data-stu-id="d133e-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d133e-518">В противном случае вызываются методы синхронного интерфейса.</span><span class="sxs-lookup"><span data-stu-id="d133e-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d133e-519">Если асинхронный и синхронный интерфейсы реализованы в одном классе, вызывается только асинхронный метод.</span><span class="sxs-lookup"><span data-stu-id="d133e-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d133e-520">При использовании абстрактных классов, таких как <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, следует переопределять только синхронные методы или асинхронный метод каждого типа фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d133e-521">Встроенные атрибуты фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-521">Built-in filter attributes</span></span>

<span data-ttu-id="d133e-522">ASP.NET Core включает встроенные фильтры на основе атрибутов, с помощью которых можно создавать подклассы и которые можно настраивать.</span><span class="sxs-lookup"><span data-stu-id="d133e-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d133e-523">Например, следующий фильтр результатов добавляет заголовок к ответу:</span><span class="sxs-lookup"><span data-stu-id="d133e-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-524">Атрибуты позволяют фильтрам принимать аргументы, как показано в примере выше.</span><span class="sxs-lookup"><span data-stu-id="d133e-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d133e-525">Примените `AddHeaderAttribute` к методу контроллера или действия, а затем укажите имя и значение заголовка HTTP:</span><span class="sxs-lookup"><span data-stu-id="d133e-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="d133e-526">Некоторые интерфейсы фильтров имеют соответствующие атрибуты, которые можно использовать как базовые классы для пользовательских реализаций.</span><span class="sxs-lookup"><span data-stu-id="d133e-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d133e-527">Атрибуты фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d133e-528">Области и порядок выполнения фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="d133e-529">Фильтр можно добавить в конвейер в одной из трех *областей*:</span><span class="sxs-lookup"><span data-stu-id="d133e-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d133e-530">С помощью атрибута в действии.</span><span class="sxs-lookup"><span data-stu-id="d133e-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="d133e-531">С помощью атрибута в контроллере.</span><span class="sxs-lookup"><span data-stu-id="d133e-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="d133e-532">Глобально для всех контроллеров и действий, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="d133e-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d133e-533">Предыдущий код глобально добавляет три фильтра с помощью коллекции [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="d133e-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="d133e-534">Порядок выполнения по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d133e-534">Default order of execution</span></span>

<span data-ttu-id="d133e-535">Если есть несколько *одинаковых* фильтров, область определяет порядок их выполнения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d133e-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d133e-536">Глобальные фильтры заключают в себя фильтры классов,</span><span class="sxs-lookup"><span data-stu-id="d133e-536">Global filters surround class filters.</span></span> <span data-ttu-id="d133e-537">которые, в свою очередь, заключают в себя фильтры методов.</span><span class="sxs-lookup"><span data-stu-id="d133e-537">Class filters surround method filters.</span></span>

<span data-ttu-id="d133e-538">В результате такого вложения *последующий* код фильтров выполняется в порядке, обратном выполнению *предшествующего* кода.</span><span class="sxs-lookup"><span data-stu-id="d133e-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d133e-539">Последовательность фильтров:</span><span class="sxs-lookup"><span data-stu-id="d133e-539">The filter sequence:</span></span>

* <span data-ttu-id="d133e-540">*Предшествующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d133e-541">*Предшествующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="d133e-542">*Предшествующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d133e-543">*Последующий* код фильтров методов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d133e-544">*Последующий* код фильтров контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="d133e-545">*Последующий* код глобальных фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d133e-546">В следующем примере показан порядок вызова методов фильтров для синхронных фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d133e-547">Последовательность</span><span class="sxs-lookup"><span data-stu-id="d133e-547">Sequence</span></span> | <span data-ttu-id="d133e-548">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-548">Filter scope</span></span> | <span data-ttu-id="d133e-549">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d133e-550">1</span><span class="sxs-lookup"><span data-stu-id="d133e-550">1</span></span> | <span data-ttu-id="d133e-551">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-552">2</span><span class="sxs-lookup"><span data-stu-id="d133e-552">2</span></span> | <span data-ttu-id="d133e-553">Контроллер</span><span class="sxs-lookup"><span data-stu-id="d133e-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-554">3</span><span class="sxs-lookup"><span data-stu-id="d133e-554">3</span></span> | <span data-ttu-id="d133e-555">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-556">4</span><span class="sxs-lookup"><span data-stu-id="d133e-556">4</span></span> | <span data-ttu-id="d133e-557">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d133e-558">5</span><span class="sxs-lookup"><span data-stu-id="d133e-558">5</span></span> | <span data-ttu-id="d133e-559">Контроллер</span><span class="sxs-lookup"><span data-stu-id="d133e-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d133e-560">6</span><span class="sxs-lookup"><span data-stu-id="d133e-560">6</span></span> | <span data-ttu-id="d133e-561">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="d133e-562">Эта последовательность показывает:</span><span class="sxs-lookup"><span data-stu-id="d133e-562">This sequence shows:</span></span>

* <span data-ttu-id="d133e-563">Фильтр метода вкладывается в фильтр контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="d133e-564">Фильтр контроллера вкладывается в глобальный фильтр.</span><span class="sxs-lookup"><span data-stu-id="d133e-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="d133e-565">Фильтры уровня контроллера и страницы Razor</span><span class="sxs-lookup"><span data-stu-id="d133e-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="d133e-566">Каждый контроллер, наследуемый от базового класса <xref:Microsoft.AspNetCore.Mvc.Controller> включает методы [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) и [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="d133e-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d133e-567">Эти методы:</span><span class="sxs-lookup"><span data-stu-id="d133e-567">These methods:</span></span>

* <span data-ttu-id="d133e-568">Заключают фильтры, которые выполняются для указанного действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d133e-569">`OnActionExecuting` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d133e-570">`OnActionExecuted` вызывается после всех фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d133e-571">`OnActionExecutionAsync` вызывается перед всеми фильтрами действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d133e-572">Код в фильтре после `next` выполняется после метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d133e-573">Например, в скачиваемом примере `MySampleActionFilter` применяется глобально при запуске.</span><span class="sxs-lookup"><span data-stu-id="d133e-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d133e-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="d133e-574">The `TestController`:</span></span>

* <span data-ttu-id="d133e-575">Применяет `SampleActionFilterAttribute` (`[SampleActionFilter]`) для действия `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="d133e-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d133e-576">Переопределяет `OnActionExecuting` и `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="d133e-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="d133e-577">Переходит к `https://localhost:5001/Test/FilterTest2` для выполнения следующего кода:</span><span class="sxs-lookup"><span data-stu-id="d133e-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d133e-578">См. подробнее о [реализации фильтров страницы Razor путем переопределения методов фильтра](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="d133e-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d133e-579">Переопределение порядка по умолчанию</span><span class="sxs-lookup"><span data-stu-id="d133e-579">Overriding the default order</span></span>

<span data-ttu-id="d133e-580">Чтобы переопределить порядок выполнения по умолчанию, можно реализовать <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d133e-581">`IOrderedFilter` предоставляет свойство <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, которое имеет приоритет над областью и определяет порядок выполнения.</span><span class="sxs-lookup"><span data-stu-id="d133e-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d133e-582">Фильтр со значением меньше `Order`:</span><span class="sxs-lookup"><span data-stu-id="d133e-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d133e-583">Выполняется *перед* кодом, выполняемым до фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d133e-584">Выполняется *после* кода, выполняемого после фильтра с более высоким значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d133e-585">Свойство `Order` можно задать с помощью параметра конструктора:</span><span class="sxs-lookup"><span data-stu-id="d133e-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="d133e-586">Рассмотрим три фильтра действий, показанные в предыдущем примере.</span><span class="sxs-lookup"><span data-stu-id="d133e-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="d133e-587">Если свойство `Order` контроллера и глобальные фильтры имеют значения 1 и 2 соответственно, порядок выполнения инвертируется.</span><span class="sxs-lookup"><span data-stu-id="d133e-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="d133e-588">Последовательность</span><span class="sxs-lookup"><span data-stu-id="d133e-588">Sequence</span></span> | <span data-ttu-id="d133e-589">Область фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-589">Filter scope</span></span> | <span data-ttu-id="d133e-590">Свойство`Order`</span><span class="sxs-lookup"><span data-stu-id="d133e-590">`Order` property</span></span> | <span data-ttu-id="d133e-591">Метод фильтра</span><span class="sxs-lookup"><span data-stu-id="d133e-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="d133e-592">1</span><span class="sxs-lookup"><span data-stu-id="d133e-592">1</span></span> | <span data-ttu-id="d133e-593">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-593">Method</span></span> | <span data-ttu-id="d133e-594">0</span><span class="sxs-lookup"><span data-stu-id="d133e-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d133e-595">2</span><span class="sxs-lookup"><span data-stu-id="d133e-595">2</span></span> | <span data-ttu-id="d133e-596">Контроллер</span><span class="sxs-lookup"><span data-stu-id="d133e-596">Controller</span></span> | <span data-ttu-id="d133e-597">1</span><span class="sxs-lookup"><span data-stu-id="d133e-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d133e-598">3</span><span class="sxs-lookup"><span data-stu-id="d133e-598">3</span></span> | <span data-ttu-id="d133e-599">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-599">Global</span></span> | <span data-ttu-id="d133e-600">2</span><span class="sxs-lookup"><span data-stu-id="d133e-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d133e-601">4</span><span class="sxs-lookup"><span data-stu-id="d133e-601">4</span></span> | <span data-ttu-id="d133e-602">Global</span><span class="sxs-lookup"><span data-stu-id="d133e-602">Global</span></span> | <span data-ttu-id="d133e-603">2</span><span class="sxs-lookup"><span data-stu-id="d133e-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d133e-604">5</span><span class="sxs-lookup"><span data-stu-id="d133e-604">5</span></span> | <span data-ttu-id="d133e-605">Контроллер</span><span class="sxs-lookup"><span data-stu-id="d133e-605">Controller</span></span> | <span data-ttu-id="d133e-606">1</span><span class="sxs-lookup"><span data-stu-id="d133e-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d133e-607">6</span><span class="sxs-lookup"><span data-stu-id="d133e-607">6</span></span> | <span data-ttu-id="d133e-608">Метод</span><span class="sxs-lookup"><span data-stu-id="d133e-608">Method</span></span> | <span data-ttu-id="d133e-609">0</span><span class="sxs-lookup"><span data-stu-id="d133e-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="d133e-610">Свойство `Order` переопределяет область при определении порядка выполнения фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d133e-611">Фильтры сначала сортируются по порядку, а затем очередность окончательно определяется по области.</span><span class="sxs-lookup"><span data-stu-id="d133e-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d133e-612">Все встроенные фильтры реализуют `IOrderedFilter` и задают значение по умолчанию `Order`, равное 0.</span><span class="sxs-lookup"><span data-stu-id="d133e-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d133e-613">Во встроенных фильтрах область определяет порядок, если для `Order` не задано ненулевое значение.</span><span class="sxs-lookup"><span data-stu-id="d133e-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d133e-614">Отмена и сокращенное выполнение</span><span class="sxs-lookup"><span data-stu-id="d133e-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d133e-615">Вы можете настроить сокращенное выполнение конвейера фильтров, задав свойство <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> для параметра <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext>, передаваемого в метод фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d133e-616">Например, приведенный ниже фильтр ресурсов предотвращает выполнение остальной части конвейера:</span><span class="sxs-lookup"><span data-stu-id="d133e-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-617">В приведенном ниже коде как фильтр `ShortCircuitingResourceFilter`, так и фильтр `AddHeader` нацелены на метод действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="d133e-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d133e-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d133e-619">Выполняется первым, поскольку это фильтр ресурсов, а `AddHeader` — фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d133e-620">Замыкает оставшуюся часть конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d133e-621">Поэтому фильтр `AddHeader` никогда не выполняется для действия `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="d133e-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d133e-622">Поведение будет аналогичным при применении обоих фильтров на уровне метода действия при условии, что фильтр `ShortCircuitingResourceFilter` выполняется первым.</span><span class="sxs-lookup"><span data-stu-id="d133e-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d133e-623">Фильтр `ShortCircuitingResourceFilter` выполняется в первую очередь из-за его типа или в связи с явным указанием свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="d133e-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="d133e-624">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="d133e-624">Dependency injection</span></span>

<span data-ttu-id="d133e-625">Фильтры можно добавлять по типу или экземпляру.</span><span class="sxs-lookup"><span data-stu-id="d133e-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d133e-626">Добавляемый экземпляр используется для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d133e-627">Если добавляется тип, он является активированным.</span><span class="sxs-lookup"><span data-stu-id="d133e-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d133e-628">Активация фильтра означает, что:</span><span class="sxs-lookup"><span data-stu-id="d133e-628">A type-activated filter means:</span></span>

* <span data-ttu-id="d133e-629">Экземпляр создается для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="d133e-629">An instance is created for each request.</span></span>
* <span data-ttu-id="d133e-630">Зависимости конструктора заполняются путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d133e-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d133e-631">Фильтры, которые реализуются как атрибуты и добавляются непосредственно в классы контроллеров или методы действий, не могут иметь зависимости конструктора, предоставленные посредством [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d133e-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d133e-632">Зависимости конструктора не могут предоставляться путем внедрения зависимостей, так как:</span><span class="sxs-lookup"><span data-stu-id="d133e-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d133e-633">Параметры конструктора должны предоставляться для атрибутов в месте их применения.</span><span class="sxs-lookup"><span data-stu-id="d133e-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d133e-634">Это ограничение, налагаемое на использование атрибутов.</span><span class="sxs-lookup"><span data-stu-id="d133e-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d133e-635">Следующие фильтры поддерживают зависимости конструктора, предоставленные путем внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d133e-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализуется в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="d133e-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d133e-637">Предыдущие фильтры могут применяться к контроллеру или методу действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d133e-638">Средства ведения журнала доступны путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-638">Loggers are available from DI.</span></span> <span data-ttu-id="d133e-639">Но следует избегать создания и использования фильтров исключительно для целей ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d133e-640">[Встроенное средство ведения журнала](xref:fundamentals/logging/index) обычно предоставляет все необходимое для ведения журнала.</span><span class="sxs-lookup"><span data-stu-id="d133e-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d133e-641">При добавлении ведения журналов в фильтры следует учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="d133e-641">Logging added to filters:</span></span>

* <span data-ttu-id="d133e-642">Следует сосредоточиться на бизнес-среде или поведении в привязке к фильтру.</span><span class="sxs-lookup"><span data-stu-id="d133e-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d133e-643">**Не** следует регистрировать действия или другие события платформы,</span><span class="sxs-lookup"><span data-stu-id="d133e-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d133e-644">так как это делают встроенные фильтры.</span><span class="sxs-lookup"><span data-stu-id="d133e-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d133e-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d133e-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="d133e-646">Типы реализации фильтра службы регистрируются в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d133e-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d133e-647">Атрибут <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> извлекает экземпляр фильтра из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d133e-648">В следующем коде используется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d133e-649">В следующем коде в контейнер внедрения зависимостей добавляется `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="d133e-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d133e-650">В следующем коде атрибут `ServiceFilter` извлекает экземпляр фильтра `AddHeaderResultServiceFilter` из внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d133e-651">При использовании `ServiceFilterAttribute` задание [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d133e-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d133e-652">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="d133e-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d133e-653">Среда выполнения ASP.NET Core не гарантирует:</span><span class="sxs-lookup"><span data-stu-id="d133e-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d133e-654">что будет создан хотя бы один экземпляр фильтра;</span><span class="sxs-lookup"><span data-stu-id="d133e-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d133e-655">что фильтр не будет повторно запрошен из контейнера внедрения зависимостей на более позднем этапе.</span><span class="sxs-lookup"><span data-stu-id="d133e-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d133e-656">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="d133e-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d133e-657">Объект <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="d133e-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d133e-658">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d133e-659">`CreateInstance` загружает указанный тип из внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d133e-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d133e-660">TypeFilterAttribute</span></span>

<span data-ttu-id="d133e-661">Атрибут <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> похож на <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, но его тип не разрешается напрямую из контейнера внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d133e-662">Он создает экземпляр типа с помощью <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d133e-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d133e-663">Так как типы `TypeFilterAttribute` не разрешаются напрямую из контейнера внедрения зависимостей:</span><span class="sxs-lookup"><span data-stu-id="d133e-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d133e-664">Типы, на которые ссылаются с помощью `TypeFilterAttribute`, не нужно регистрировать в контейнере внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d133e-665">Их зависимости выполняются самим контейнером.</span><span class="sxs-lookup"><span data-stu-id="d133e-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d133e-666">Атрибут `TypeFilterAttribute` может также принимать аргументы конструктора для типа.</span><span class="sxs-lookup"><span data-stu-id="d133e-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d133e-667">При использовании `TypeFilterAttribute` задание [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="d133e-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d133e-668">Указывает, что экземпляр фильтра *можно* многократно использовать за пределами области запроса, в которой он был создан.</span><span class="sxs-lookup"><span data-stu-id="d133e-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d133e-669">Среда выполнения ASP.NET Core не предоставляет никаких гарантий, что будет создан хотя бы один экземпляр фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d133e-670">Не следует использовать с фильтром, который зависит от служб со временем существования, кроме элементов singleton.</span><span class="sxs-lookup"><span data-stu-id="d133e-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d133e-671">В следующем примере показано, как передавать аргументы в тип с помощью `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="d133e-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d133e-672">Фильтры авторизации</span><span class="sxs-lookup"><span data-stu-id="d133e-672">Authorization filters</span></span>

<span data-ttu-id="d133e-673">Фильтры авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-673">Authorization filters:</span></span>

* <span data-ttu-id="d133e-674">Выполняются в первую очередь в конвейере фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d133e-675">Контролируют доступ к методам действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-675">Control access to action methods.</span></span>
* <span data-ttu-id="d133e-676">Имеют предшествующий, но не последующий метод.</span><span class="sxs-lookup"><span data-stu-id="d133e-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="d133e-677">Для работы пользовательских фильтров авторизации требуется настраиваемая платформа авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d133e-678">Настройка политик авторизации или определение пользовательской политики авторизации предпочтительнее создания пользовательского фильтра.</span><span class="sxs-lookup"><span data-stu-id="d133e-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d133e-679">Встроенный фильтр авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="d133e-680">Вызывает систему авторизации.</span><span class="sxs-lookup"><span data-stu-id="d133e-680">Calls the authorization system.</span></span>
* <span data-ttu-id="d133e-681">Не выполняет авторизацию запросов.</span><span class="sxs-lookup"><span data-stu-id="d133e-681">Does not authorize requests.</span></span>

<span data-ttu-id="d133e-682">**Не** вызывайте исключения в фильтрах авторизации:</span><span class="sxs-lookup"><span data-stu-id="d133e-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d133e-683">Исключение не будет обработано.</span><span class="sxs-lookup"><span data-stu-id="d133e-683">The exception will not be handled.</span></span>
* <span data-ttu-id="d133e-684">Фильтры исключений не будут обрабатывать исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d133e-685">При возникновении исключения в фильтре авторизации попробуйте создать запрос.</span><span class="sxs-lookup"><span data-stu-id="d133e-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d133e-686">Дополнительные сведения об [авторизации](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="d133e-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d133e-687">Фильтры ресурсов</span><span class="sxs-lookup"><span data-stu-id="d133e-687">Resource filters</span></span>

<span data-ttu-id="d133e-688">Фильтры ресурсов:</span><span class="sxs-lookup"><span data-stu-id="d133e-688">Resource filters:</span></span>

* <span data-ttu-id="d133e-689">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d133e-690">Выполнение охватывает большую часть конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d133e-691">До фильтров ресурсов применяются только [фильтры авторизации](#authorization-filters).</span><span class="sxs-lookup"><span data-stu-id="d133e-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d133e-692">Фильтры ресурсов полезны для сокращенного выполнения большей части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d133e-693">Например, фильтр кэширования предотвращает выполнение остальной части конвейера при попадании в кэше.</span><span class="sxs-lookup"><span data-stu-id="d133e-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d133e-694">Примеры фильтров ресурсов:</span><span class="sxs-lookup"><span data-stu-id="d133e-694">Resource filter examples:</span></span>

* <span data-ttu-id="d133e-695">[Сокращенное выполнение фильтра ресурсов](#short-circuiting-resource-filter) показано ранее.</span><span class="sxs-lookup"><span data-stu-id="d133e-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d133e-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="d133e-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d133e-697">Предотвращает доступ привязки модели к данным формы.</span><span class="sxs-lookup"><span data-stu-id="d133e-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d133e-698">Используется для загрузки больших файлов, если необходимо предотвратить считывание данных формы в память.</span><span class="sxs-lookup"><span data-stu-id="d133e-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d133e-699">Фильтры действий</span><span class="sxs-lookup"><span data-stu-id="d133e-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d133e-700">Фильтры действий **не** применяются к Razor страницам.</span><span class="sxs-lookup"><span data-stu-id="d133e-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="d133e-701">Страницы поддерживают <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="d133e-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d133e-702">Дополнительные сведения см. в разделе [методы фильтров Razor для страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d133e-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d133e-703">Фильтры действий:</span><span class="sxs-lookup"><span data-stu-id="d133e-703">Action filters:</span></span>

* <span data-ttu-id="d133e-704">Реализуют либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter>, либо интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d133e-705">Их выполнение охватывает выполнение методов действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d133e-706">В следующем фрагменте кода показан пример фильтра действий:</span><span class="sxs-lookup"><span data-stu-id="d133e-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d133e-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="d133e-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d133e-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> позволяет считать входные данные в метод действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="d133e-709"><xref:Microsoft.AspNetCore.Mvc.Controller> позволяет управлять экземпляром контроллера.</span><span class="sxs-lookup"><span data-stu-id="d133e-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d133e-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> позволяет при задании свойства `Result` сократить выполнение метода действия и последующих фильтров действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d133e-711">Вызов исключения в методе действия:</span><span class="sxs-lookup"><span data-stu-id="d133e-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d133e-712">Предотвращает выполнение последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d133e-713">В отличие от параметра `Result` рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="d133e-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d133e-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> предоставляет `Controller` и `Result`, а также следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="d133e-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d133e-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d133e-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> будет иметь ненулевое значение, если предыдущее выполнение фильтра действий вызвало исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d133e-717">При присвоении этому свойству ненулевого значения:</span><span class="sxs-lookup"><span data-stu-id="d133e-717">Setting this property to null:</span></span>

  * <span data-ttu-id="d133e-718">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d133e-719">`Result` будет выполняться так, как при возвращении методом действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d133e-720">Для `IAsyncActionFilter` вызов <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="d133e-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d133e-721">Приводит к выполнению последующих фильтров действий и метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d133e-722">Возвращает `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="d133e-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d133e-723">Для сокращенного выполнения присвойте <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> экземпляру результата и не вызывайте `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="d133e-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d133e-724">Платформа предоставляет абстрактный класс <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="d133e-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d133e-725">Фильтр действий `OnActionExecuting` можно использовать:</span><span class="sxs-lookup"><span data-stu-id="d133e-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d133e-726">Для проверки состояния модели.</span><span class="sxs-lookup"><span data-stu-id="d133e-726">Validate model state.</span></span>
* <span data-ttu-id="d133e-727">Для получения ошибки, если состояние является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="d133e-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="d133e-728">Метод `OnActionExecuted` выполняется после метода действия:</span><span class="sxs-lookup"><span data-stu-id="d133e-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d133e-729">Он имеет доступ к результатам действия и может управлять ими с помощью свойства <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="d133e-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d133e-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> будет иметь значение true, если выполнение действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d133e-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> будет иметь ненулевое значение, если действие или последующий фильтр действий вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d133e-732">Если установить для `Exception` значение NULL:</span><span class="sxs-lookup"><span data-stu-id="d133e-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d133e-733">Будет эффективно обрабатываться исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d133e-734">`ActionExecutedContext.Result` будет выполняться так, как если бы метод действия вернул его обычным образом.</span><span class="sxs-lookup"><span data-stu-id="d133e-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d133e-735">Фильтры исключений</span><span class="sxs-lookup"><span data-stu-id="d133e-735">Exception filters</span></span>

<span data-ttu-id="d133e-736">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-736">Exception filters:</span></span>

* <span data-ttu-id="d133e-737">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="d133e-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="d133e-738">Можно использовать для реализации политик обработки стандартных ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d133e-739">В следующем примере фильтра исключений используется пользовательское представление ошибок для отображения подробных сведений об исключениях, которые происходят во время разработки приложения:</span><span class="sxs-lookup"><span data-stu-id="d133e-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d133e-740">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-740">Exception filters:</span></span>

* <span data-ttu-id="d133e-741">Не имеют предшествующих и последующих событий.</span><span class="sxs-lookup"><span data-stu-id="d133e-741">Don't have before and after events.</span></span>
* <span data-ttu-id="d133e-742">Реализуют <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="d133e-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d133e-743">Обрабатывать необработанные исключения, происходящие Razor при создании страницы или контроллера, [Привязка модели](xref:mvc/models/model-binding), фильтры действий или методы действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d133e-744">**Не** перехватывают исключения, которые возникают в фильтрах ресурсов, фильтрах результатов или при выполнении результата MVC.</span><span class="sxs-lookup"><span data-stu-id="d133e-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d133e-745">Для обработки исключения присвойте свойству <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> значение `true` или напишите ответ.</span><span class="sxs-lookup"><span data-stu-id="d133e-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d133e-746">Это предотвратит распространение исключения.</span><span class="sxs-lookup"><span data-stu-id="d133e-746">This stops propagation of the exception.</span></span> <span data-ttu-id="d133e-747">Фильтр исключений не может преобразовать исключение в успешное выполнение.</span><span class="sxs-lookup"><span data-stu-id="d133e-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d133e-748">Это может сделать только фильтр действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-748">Only an action filter can do that.</span></span>

<span data-ttu-id="d133e-749">Фильтры исключений:</span><span class="sxs-lookup"><span data-stu-id="d133e-749">Exception filters:</span></span>

* <span data-ttu-id="d133e-750">Хорошо подходят для перехвата исключений, возникающих в действиях.</span><span class="sxs-lookup"><span data-stu-id="d133e-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d133e-751">Не обладает такой гибкостью, как ПО промежуточного слоя обработки ошибок.</span><span class="sxs-lookup"><span data-stu-id="d133e-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d133e-752">ПО промежуточного слоя хорошо подходит для обработки исключений.</span><span class="sxs-lookup"><span data-stu-id="d133e-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d133e-753">Используйте фильтры исключений, только если обработка ошибок осуществляется *по-разному* с учетом вызванного метода действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d133e-754">Например, в приложении могут использоваться методы действий как для конечных точек API, так и для представлений или HTML.</span><span class="sxs-lookup"><span data-stu-id="d133e-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d133e-755">Конечные точки API могут возвращать сведения об ошибках в формате JSON, в то время как действия на основе представлений могут возвращать страницу ошибки в формате HTML.</span><span class="sxs-lookup"><span data-stu-id="d133e-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d133e-756">Фильтры результатов</span><span class="sxs-lookup"><span data-stu-id="d133e-756">Result filters</span></span>

<span data-ttu-id="d133e-757">Фильтры результатов:</span><span class="sxs-lookup"><span data-stu-id="d133e-757">Result filters:</span></span>

* <span data-ttu-id="d133e-758">Реализация интерфейса:</span><span class="sxs-lookup"><span data-stu-id="d133e-758">Implement an interface:</span></span>
  * <span data-ttu-id="d133e-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d133e-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d133e-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> или <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d133e-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d133e-761">Их выполнение охватывает выполнение результатов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d133e-762">IResultFilter и IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d133e-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d133e-763">В следующем фрагменте кода показан фильтр результатов, который добавляет заголовок HTTP:</span><span class="sxs-lookup"><span data-stu-id="d133e-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d133e-764">Тип выполняемого результата зависит от соответствующего действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d133e-765">Действие, возвращающее представление, будет включать всю обработку Razor в рамках выполняемого объекта <xref:Microsoft.AspNetCore.Mvc.ViewResult>.</span><span class="sxs-lookup"><span data-stu-id="d133e-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d133e-766">В процессе выполнения результата метод API может производить сериализацию.</span><span class="sxs-lookup"><span data-stu-id="d133e-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d133e-767">Дополнительные сведения о [результатах действий](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="d133e-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d133e-768">Фильтры результатов выполняются только в том случае, когда действие или фильтры действий предоставляют результат действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d133e-769">Фильтры результатов не выполняются в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="d133e-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="d133e-770">Фильтр авторизации или ресурсов выполняет сокращение конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d133e-771">Фильтр исключений обрабатывает исключение и выдает результат действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d133e-772">Метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> может сокращать выполнение результата действия и последующих фильтров результатов, присваивая свойству <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> значение `true`.</span><span class="sxs-lookup"><span data-stu-id="d133e-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d133e-773">При сокращении выполнения выполните запись в объект ответа, чтобы избежать формирования пустого ответа.</span><span class="sxs-lookup"><span data-stu-id="d133e-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d133e-774">Вызов исключения в `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="d133e-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="d133e-775">Предотвращает выполнение результата действия и последующих фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d133e-776">Рассматривается как сбой, а не успешный результат.</span><span class="sxs-lookup"><span data-stu-id="d133e-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d133e-777">Если запускается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>, ответ, скорее всего, уже был отправлен клиенту.</span><span class="sxs-lookup"><span data-stu-id="d133e-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="d133e-778">Если ответ уже был отправлен клиенту его больше нельзя изменить.</span><span class="sxs-lookup"><span data-stu-id="d133e-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="d133e-779">`ResultExecutedContext.Canceled` будет иметь значение `true`, если выполнение результата действия было сокращено другим фильтром.</span><span class="sxs-lookup"><span data-stu-id="d133e-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d133e-780">`ResultExecutedContext.Exception` будет иметь ненулевое значение, если результат действия или последующий фильтр результатов вызвали исключение.</span><span class="sxs-lookup"><span data-stu-id="d133e-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d133e-781">Присвоение `Exception` значения NULL приводит к обработке исключения и предотвращает его последующий вызов ASP.NET Core на дальнейших этапах конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="d133e-782">Надежный способ записи данных в ответ при обработке исключения в фильтре результатов отсутствует.</span><span class="sxs-lookup"><span data-stu-id="d133e-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d133e-783">Если результат действия вызывает исключение в процессе выполнения и заголовки уже были переданы в клиент, надежного механизма отправки кода сбоя не существует.</span><span class="sxs-lookup"><span data-stu-id="d133e-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d133e-784">Для <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> вызов к `await next` для <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> приводит к выполнению последующих фильтров результатов и результата действия.</span><span class="sxs-lookup"><span data-stu-id="d133e-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d133e-785">Для сокращения выполнения задайте [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) для `true` и не вызывайте `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="d133e-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d133e-786">Платформа предоставляет абстрактный класс `ResultFilterAttribute`, для которого можно создавать подклассы.</span><span class="sxs-lookup"><span data-stu-id="d133e-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d133e-787">Представленный ранее класс [AddHeaderAttribute](#add-header-attribute) — это пример атрибута фильтра результатов.</span><span class="sxs-lookup"><span data-stu-id="d133e-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d133e-788">IAlwaysRunResultFilter и IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d133e-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d133e-789">Интерфейсы <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> и <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> объявляют реализацию <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>, которая выполняется для всех результатов действий.</span><span class="sxs-lookup"><span data-stu-id="d133e-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d133e-790">Сюда включены результаты действий, созданные:</span><span class="sxs-lookup"><span data-stu-id="d133e-790">This includes action results produced by:</span></span>

* <span data-ttu-id="d133e-791">фильтрами авторизации и фильтрами ресурсов, которые сокращают ответ;</span><span class="sxs-lookup"><span data-stu-id="d133e-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d133e-792">фильтрами исключений.</span><span class="sxs-lookup"><span data-stu-id="d133e-792">Exception filters.</span></span>

<span data-ttu-id="d133e-793">Например, следующий фильтр всегда выполняется, задавая результат действия (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) с кодом состояния *422 Unprocessable Entity* при сбое согласования содержимого:</span><span class="sxs-lookup"><span data-stu-id="d133e-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="d133e-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d133e-794">IFilterFactory</span></span>

<span data-ttu-id="d133e-795">Объект <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d133e-796">Поэтому экземпляр `IFilterFactory` можно использовать в качестве экземпляра `IFilterMetadata` в любом месте конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d133e-797">Когда среда выполнения готовится вызвать фильтр, она пытается привести его к `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="d133e-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d133e-798">Если приведение проходит успешно, вызывается метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания вызываемого экземпляра `IFilterMetadata`.</span><span class="sxs-lookup"><span data-stu-id="d133e-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d133e-799">Это обеспечивает высокую степень гибкости, так как при запуске приложения нет необходимости в явном и точном определении конвейера фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d133e-800">Еще один подход к созданию фильтров заключается в реализации `IFilterFactory` с помощью настраиваемых атрибутов:</span><span class="sxs-lookup"><span data-stu-id="d133e-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d133e-801">Приведенный выше код можно проверить, выполнив [скачиваемый пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="d133e-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="d133e-802">Вызовите средства для разработчика (F12).</span><span class="sxs-lookup"><span data-stu-id="d133e-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d133e-803">Перейдите на страницу `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="d133e-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d133e-804">В средствах для разработчика (F12) отобразятся следующие заголовки ответа, добавленные примером кода:</span><span class="sxs-lookup"><span data-stu-id="d133e-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d133e-805">**Автор:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="d133e-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="d133e-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d133e-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d133e-807">**внутренний:**`My header`</span><span class="sxs-lookup"><span data-stu-id="d133e-807">**internal:** `My header`</span></span>

<span data-ttu-id="d133e-808">Приведенный выше код создает заголовок ответа **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="d133e-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d133e-809">Реализация IFilterFactory в атрибуте</span><span class="sxs-lookup"><span data-stu-id="d133e-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d133e-810">Фильтры, реализующие `IFilterFactory`, используются для фильтров, которые:</span><span class="sxs-lookup"><span data-stu-id="d133e-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d133e-811">Не требуют передачи параметров.</span><span class="sxs-lookup"><span data-stu-id="d133e-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="d133e-812">Содержат зависимости конструктора, которые должны заполняться путем внедрения зависимостей.</span><span class="sxs-lookup"><span data-stu-id="d133e-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d133e-813">Объект <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> реализует интерфейс <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="d133e-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d133e-814">`IFilterFactory` предоставляет метод <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> для создания экземпляра <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="d133e-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d133e-815">`CreateInstance` загружает указанный тип из контейнера служб (внедрение зависимостей).</span><span class="sxs-lookup"><span data-stu-id="d133e-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d133e-816">В коде ниже приведено три примера применения `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="d133e-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d133e-817">В приведенном выше коде декорирование метода с использованием `[SampleActionFilter]` является рекомендуемым способом применения `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="d133e-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d133e-818">Использование ПО промежуточного слоя в конвейере фильтров</span><span class="sxs-lookup"><span data-stu-id="d133e-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d133e-819">Фильтры ресурсов по принципу работы похожи на [ПО промежуточного слоя](xref:fundamentals/middleware/index) тем, что они заключают в себя выполнение всех объектов, находящихся далее в конвейере.</span><span class="sxs-lookup"><span data-stu-id="d133e-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d133e-820">При этом фильтры отличаются от ПО промежуточного слоя тем, что они являются частью среды выполнения ASP.NET Core, то есть они имеют доступ к контексту и конструкциям ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d133e-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="d133e-821">Чтобы использовать ПО промежуточного слоя в качестве фильтра, создайте тип с методом `Configure`, определяющим ПО промежуточного слоя, которое нужно внедрить в конвейер фильтров.</span><span class="sxs-lookup"><span data-stu-id="d133e-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d133e-822">В примере ниже ПО промежуточного слоя локализации применяется для определения текущих языка и региональных параметров для запроса:</span><span class="sxs-lookup"><span data-stu-id="d133e-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d133e-823">Используйте <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> для выполнения ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="d133e-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d133e-824">Фильтры ПО промежуточного слоя выполняются на том же этапе конвейера фильтров, что и фильтры ресурсов, перед привязкой модели и после остальной части конвейера.</span><span class="sxs-lookup"><span data-stu-id="d133e-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d133e-825">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="d133e-825">Next actions</span></span>

* <span data-ttu-id="d133e-826">См. раздел [методы Razor фильтров для страниц](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="d133e-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d133e-827">Чтобы поэкспериментировать с фильтрами, [скачайте, протестируйте и измените пример с GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="d133e-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
