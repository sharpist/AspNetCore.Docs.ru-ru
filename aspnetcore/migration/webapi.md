---
title: Переход с веб-API ASP.NET на ASP.NET Core
author: ardalis
description: Узнайте, как перенести реализацию веб-API с веб-API ASP.NET 4. x на ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766593"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="876ca-103">Переход с веб-API ASP.NET на ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="876ca-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="876ca-104">[Скотта Эдди (](https://twitter.com/scott_addie) и [Виктор Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="876ca-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="876ca-105">Веб-API ASP.NET 4. x — это служба HTTP, которая достигает широкого спектра клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="876ca-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="876ca-106">ASP.NET Core объединяет модели приложений MVC и веб-API ASP.NET 4. x в более простую модель программирования, известную как ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="876ca-106">ASP.NET Core unifies ASP.NET 4.x's MVC and Web API app models into a simpler programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="876ca-107">В этой статье описываются шаги, необходимые для перехода с веб-API ASP.NET 4. x на ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="876ca-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="876ca-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="876ca-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="876ca-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="876ca-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="876ca-110">Обзор проекта веб-API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="876ca-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="876ca-111">В качестве отправной точки в этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="876ca-111">As a starting point, this article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="876ca-112">В этом проекте простой проект веб-API ASP.NET 4. x настраивается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="876ca-112">In that project, a simple ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="876ca-113">В *Global.asax.CS*выполняется вызов `WebApiConfig.Register`:</span><span class="sxs-lookup"><span data-stu-id="876ca-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="876ca-114">Класс находится в папке *App_Start* и имеет статический `Register` метод: `WebApiConfig`</span><span class="sxs-lookup"><span data-stu-id="876ca-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="876ca-115">Этот класс настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя на самом деле она не используется в проекте.</span><span class="sxs-lookup"><span data-stu-id="876ca-115">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="876ca-116">Он также настраивает таблицу маршрутизации, используемую веб-API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="876ca-116">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="876ca-117">В этом случае ASP.NET 4. x Web API ждет, что URL-адреса будут соответствовать `/api/{controller}/{id}`формату `{id}` , с необязательным.</span><span class="sxs-lookup"><span data-stu-id="876ca-117">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="876ca-118">Проект *продуктсапп* включает один контроллер.</span><span class="sxs-lookup"><span data-stu-id="876ca-118">The *ProductsApp* project includes one controller.</span></span> <span data-ttu-id="876ca-119">Контроллер наследуется от `ApiController` и содержит два действия:</span><span class="sxs-lookup"><span data-stu-id="876ca-119">The controller inherits from `ApiController` and contains two actions:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

<span data-ttu-id="876ca-120">`Product` Модель, используемая `ProductsController` , является простым классом:</span><span class="sxs-lookup"><span data-stu-id="876ca-120">The `Product` model used by `ProductsController` is a simple class:</span></span>

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

<span data-ttu-id="876ca-121">В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="876ca-121">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-destination-project"></a><span data-ttu-id="876ca-122">Создать целевой проект</span><span class="sxs-lookup"><span data-stu-id="876ca-122">Create destination project</span></span>

<span data-ttu-id="876ca-123">В Visual Studio выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="876ca-123">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="876ca-124">Последовательно выберите **файл** > **создать** > **проект** > **другие типы** > проектов**решения Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="876ca-124">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="876ca-125">Выберите **пустое решение**и назовите решение *вебапимигратион*.</span><span class="sxs-lookup"><span data-stu-id="876ca-125">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="876ca-126">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="876ca-126">Click the **OK** button.</span></span>
* <span data-ttu-id="876ca-127">Добавьте существующий проект *продуктсапп* в решение.</span><span class="sxs-lookup"><span data-stu-id="876ca-127">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="876ca-128">Добавьте в решение новый проект **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="876ca-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="876ca-129">Выберите целевую платформу **.NET Core** в раскрывающемся списке и выберите шаблон проекта **API** .</span><span class="sxs-lookup"><span data-stu-id="876ca-129">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="876ca-130">Назовите проект *продуктскоре*и нажмите кнопку **ОК** .</span><span class="sxs-lookup"><span data-stu-id="876ca-130">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="876ca-131">Решение теперь содержит два проекта.</span><span class="sxs-lookup"><span data-stu-id="876ca-131">The solution now contains two projects.</span></span> <span data-ttu-id="876ca-132">В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="876ca-132">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="876ca-133">Миграция конфигурации</span><span class="sxs-lookup"><span data-stu-id="876ca-133">Migrate configuration</span></span>

<span data-ttu-id="876ca-134">ASP.NET Core не использует папку *App_Start* или файл *Global. asax* , а файл *Web. config* добавляется во время публикации.</span><span class="sxs-lookup"><span data-stu-id="876ca-134">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file, and the *web.config* file is added at publish time.</span></span> <span data-ttu-id="876ca-135">*Startup.CS* является заменой для *Global. asax* и находится в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="876ca-135">*Startup.cs* is the replacement for *Global.asax* and is located in the project root.</span></span> <span data-ttu-id="876ca-136">`Startup` Класс обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="876ca-136">The `Startup` class handles all app startup tasks.</span></span> <span data-ttu-id="876ca-137">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="876ca-137">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="876ca-138">В ASP.NET Core MVC маршрутизация атрибутов включается по умолчанию <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> при вызове `Startup.Configure`в.</span><span class="sxs-lookup"><span data-stu-id="876ca-138">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="876ca-139">Следующий `UseMvc` вызов заменяет файл *App_Start/вебапиконфиг.КС* проекта *продуктсапп* :</span><span class="sxs-lookup"><span data-stu-id="876ca-139">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="876ca-140">Перенос моделей и контроллеров</span><span class="sxs-lookup"><span data-stu-id="876ca-140">Migrate models and controllers</span></span>

<span data-ttu-id="876ca-141">Скопируйте контроллер проекта *продуктапп* и модель, которую он использует.</span><span class="sxs-lookup"><span data-stu-id="876ca-141">Copy over the *ProductApp* project's controller and the model it uses.</span></span> <span data-ttu-id="876ca-142">Выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="876ca-142">Follow these steps:</span></span>

1. <span data-ttu-id="876ca-143">Скопируйте *Controllers/продуктсконтроллер. CS* из исходного проекта в новый.</span><span class="sxs-lookup"><span data-stu-id="876ca-143">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="876ca-144">Скопируйте всю папку *Models* из исходного проекта в новую.</span><span class="sxs-lookup"><span data-stu-id="876ca-144">Copy the entire *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="876ca-145">Измените пространства имен скопированных файлов в соответствии с новым именем проекта (*продуктскоре*).</span><span class="sxs-lookup"><span data-stu-id="876ca-145">Change the copied files' namespaces to match the new project name (*ProductsCore*).</span></span> <span data-ttu-id="876ca-146">Измените `using ProductsApp.Models;` инструкцию в *ProductsController.CS* .</span><span class="sxs-lookup"><span data-stu-id="876ca-146">Adjust the `using ProductsApp.Models;` statement in *ProductsController.cs* too.</span></span>

<span data-ttu-id="876ca-147">На этом этапе сборка приложения приводит к ряду ошибок компиляции.</span><span class="sxs-lookup"><span data-stu-id="876ca-147">At this point, building the app results in a number of compilation errors.</span></span> <span data-ttu-id="876ca-148">Ошибки возникают из-за отсутствия в ASP.NET Core следующих компонентов:</span><span class="sxs-lookup"><span data-stu-id="876ca-148">The errors occur because the following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="876ca-149">Класс `ApiController`</span><span class="sxs-lookup"><span data-stu-id="876ca-149">`ApiController` class</span></span>
* <span data-ttu-id="876ca-150">Пространство имен `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="876ca-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="876ca-151">Интерфейс `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="876ca-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="876ca-152">Исправьте ошибки следующим образом:</span><span class="sxs-lookup"><span data-stu-id="876ca-152">Fix the errors as follows:</span></span>

1. <span data-ttu-id="876ca-153">Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="876ca-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="876ca-154">Добавьте `using Microsoft.AspNetCore.Mvc;` , чтобы разрешить `ControllerBase` ссылку.</span><span class="sxs-lookup"><span data-stu-id="876ca-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="876ca-155">Удалите `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="876ca-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="876ca-156">Измените тип `GetProduct` возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>`.</span><span class="sxs-lookup"><span data-stu-id="876ca-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>

<span data-ttu-id="876ca-157">Упростите `GetProduct` `return` инструкцию действия следующим образом:</span><span class="sxs-lookup"><span data-stu-id="876ca-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

```csharp
return product;
```

## <a name="configure-routing"></a><span data-ttu-id="876ca-158">Настройка маршрутизации</span><span class="sxs-lookup"><span data-stu-id="876ca-158">Configure routing</span></span>

<span data-ttu-id="876ca-159">Настройте маршрутизацию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="876ca-159">Configure routing as follows:</span></span>

1. <span data-ttu-id="876ca-160">Пометьте `ProductsController` класс следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="876ca-160">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="876ca-161">Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера.</span><span class="sxs-lookup"><span data-stu-id="876ca-161">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="876ca-162">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.</span><span class="sxs-lookup"><span data-stu-id="876ca-162">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="876ca-163">Маршрутизация атрибутов поддерживает маркеры, такие `[controller]` как `[action]`и.</span><span class="sxs-lookup"><span data-stu-id="876ca-163">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="876ca-164">Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут.</span><span class="sxs-lookup"><span data-stu-id="876ca-164">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="876ca-165">Токены уменьшают количество волшебных строк в проекте.</span><span class="sxs-lookup"><span data-stu-id="876ca-165">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="876ca-166">Кроме того, токены обеспечивают синхронизацию маршрутов с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.</span><span class="sxs-lookup"><span data-stu-id="876ca-166">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="876ca-167">Задайте для режима совместимости проекта значение ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="876ca-167">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="876ca-168">Предыдущее изменение:</span><span class="sxs-lookup"><span data-stu-id="876ca-168">The preceding change:</span></span>

    * <span data-ttu-id="876ca-169">Требуется для использования `[ApiController]` атрибута на уровне контроллера.</span><span class="sxs-lookup"><span data-stu-id="876ca-169">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="876ca-170">Может привести к возможным нарушениям поведений, появившимся в ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="876ca-170">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="876ca-171">Включить HTTP-запросы GET к `ProductController` действиям:</span><span class="sxs-lookup"><span data-stu-id="876ca-171">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="876ca-172">Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.</span><span class="sxs-lookup"><span data-stu-id="876ca-172">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="876ca-173">Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.</span><span class="sxs-lookup"><span data-stu-id="876ca-173">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="876ca-174">После внесения предыдущих изменений и удаления неиспользуемых `using` инструкций файл *ProductsController.CS* выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="876ca-174">After the preceding changes and the removal of unused `using` statements, *ProductsController.cs* file looks like this:</span></span>

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

<span data-ttu-id="876ca-175">Запустите перенесенный проект и перейдите к `/api/products`.</span><span class="sxs-lookup"><span data-stu-id="876ca-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="876ca-176">Появится полный список из трех продуктов.</span><span class="sxs-lookup"><span data-stu-id="876ca-176">A full list of three products appears.</span></span> <span data-ttu-id="876ca-177">Перейдите по адресу `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="876ca-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="876ca-178">Появится первый продукт.</span><span class="sxs-lookup"><span data-stu-id="876ca-178">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="876ca-179">Оболочка совместимости</span><span class="sxs-lookup"><span data-stu-id="876ca-179">Compatibility shim</span></span>

<span data-ttu-id="876ca-180">Библиотека [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) предоставляет оболочку совместимости для перемещения проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="876ca-180">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="876ca-181">Оболочка совместимости расширяет ASP.NET Core для поддержки ряда соглашений от ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="876ca-181">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="876ca-182">Пример, переданный ранее в этом документе, достаточно прост в том, что оболочка совместимости не была нужна.</span><span class="sxs-lookup"><span data-stu-id="876ca-182">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="876ca-183">Для более крупных проектов использование оболочки совместимости может оказаться полезным для временного разделения зазора API между ASP.NET Core и ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="876ca-183">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="876ca-184">Оболочка совместимости веб-API предназначена для использования в качестве временной меры для поддержки переноса больших проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="876ca-184">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="876ca-185">Со временем проекты должны быть обновлены для использования шаблонов ASP.NET Core, а не полагаться на оболочку совместимости.</span><span class="sxs-lookup"><span data-stu-id="876ca-185">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="876ca-186">Функции обеспечения совместимости, `Microsoft.AspNetCore.Mvc.WebApiCompatShim` включенные в:</span><span class="sxs-lookup"><span data-stu-id="876ca-186">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="876ca-187">Добавляет `ApiController` тип, поэтому не требуется обновлять базовые типы контроллеров.</span><span class="sxs-lookup"><span data-stu-id="876ca-187">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="876ca-188">Включает привязку модели в стиле веб-API.</span><span class="sxs-lookup"><span data-stu-id="876ca-188">Enables Web API-style model binding.</span></span> <span data-ttu-id="876ca-189">ASP.NET Core функция привязки модели MVC аналогична ASP.NET 4. x MVC 5 по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="876ca-189">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="876ca-190">Оболочка совместимости изменяет привязку модели так, чтобы она была похожа на соглашения о привязке модели Web API 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="876ca-190">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="876ca-191">Например, сложные типы автоматически привязываются из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="876ca-191">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="876ca-192">Расширяет привязку модели, чтобы действия контроллера могли принимать параметры типа `HttpRequestMessage`.</span><span class="sxs-lookup"><span data-stu-id="876ca-192">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="876ca-193">Добавляет модули форматирования сообщений, позволяя действиям возвращать `HttpResponseMessage`результаты типа.</span><span class="sxs-lookup"><span data-stu-id="876ca-193">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="876ca-194">Добавляет дополнительные методы ответа, которые могут использоваться действиями веб-API 2 для обслуживания ответов:</span><span class="sxs-lookup"><span data-stu-id="876ca-194">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="876ca-195">`HttpResponseMessage`Каждый</span><span class="sxs-lookup"><span data-stu-id="876ca-195">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="876ca-196">Методы результата действия:</span><span class="sxs-lookup"><span data-stu-id="876ca-196">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="876ca-197">Добавляет экземпляр `IContentNegotiator` в контейнер внедрения зависимостей (DI) приложения и делает доступными типы, связанные с согласованием содержимого, из [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="876ca-197">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="876ca-198">Примерами таких типов являются `DefaultContentNegotiator` и `MediaTypeFormatter`.</span><span class="sxs-lookup"><span data-stu-id="876ca-198">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="876ca-199">Чтобы использовать оболочку совместимости, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="876ca-199">To use the compatibility shim:</span></span>

1. <span data-ttu-id="876ca-200">Установите пакет NuGet [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="876ca-200">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="876ca-201">Зарегистрируйте службы оболочки совместимости с контейнером внедрения приложения, вызвав `services.AddMvc().AddWebApiConventions()` в. `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="876ca-201">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="876ca-202">Определите зависящие от веб-API `MapWebApiRoute` маршруты с `IRouteBuilder` помощью в в `IApplicationBuilder.UseMvc` вызове приложения.</span><span class="sxs-lookup"><span data-stu-id="876ca-202">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="876ca-203">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="876ca-203">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
