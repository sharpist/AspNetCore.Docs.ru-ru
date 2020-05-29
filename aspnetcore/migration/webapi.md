---
<span data-ttu-id="68dcf-101">название: автор: описание: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="68dcf-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68dcf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68dcf-102">'Blazor'</span></span>
- <span data-ttu-id="68dcf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68dcf-103">'Identity'</span></span>
- <span data-ttu-id="68dcf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68dcf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="68dcf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68dcf-105">'Razor'</span></span>
- <span data-ttu-id="68dcf-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="68dcf-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="68dcf-107">Переход с веб-API ASP.NET на ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68dcf-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="68dcf-108">[Скотта Эдди (](https://twitter.com/scott_addie) и [Виктор Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="68dcf-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="68dcf-109">Веб-API ASP.NET 4. x — это служба HTTP, которая достигает широкого спектра клиентов, включая браузеры и мобильные устройства.</span><span class="sxs-lookup"><span data-stu-id="68dcf-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="68dcf-110">ASP.NET Core объединяет модели приложений MVC и веб-API ASP.NET 4. x в единую модель программирования, известную как ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="68dcf-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="68dcf-111">В этой статье описываются шаги, необходимые для перехода с веб-API ASP.NET 4. x на ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="68dcf-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="68dcf-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="68dcf-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="68dcf-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="68dcf-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="68dcf-114">Обзор проекта веб-API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="68dcf-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="68dcf-115">В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="68dcf-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="68dcf-116">В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="68dcf-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="68dcf-117">В *Global.asax.CS*выполняется вызов `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="68dcf-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="68dcf-118">`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:</span><span class="sxs-lookup"><span data-stu-id="68dcf-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="68dcf-119">Предыдущий класс:</span><span class="sxs-lookup"><span data-stu-id="68dcf-119">The preceding class:</span></span>

* <span data-ttu-id="68dcf-120">Настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя в действительности она не используется.</span><span class="sxs-lookup"><span data-stu-id="68dcf-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="68dcf-121">Настраивает таблицу маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="68dcf-121">Configures the routing table.</span></span>
<span data-ttu-id="68dcf-122">В примере кода требуется, чтобы URL-адреса совпадали с форматом `/api/{controller}/{id}` , а — `{id}` необязательным.</span><span class="sxs-lookup"><span data-stu-id="68dcf-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="68dcf-123">В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="68dcf-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="68dcf-124">Создание целевого проекта</span><span class="sxs-lookup"><span data-stu-id="68dcf-124">Create the destination project</span></span>

<span data-ttu-id="68dcf-125">Создайте новое пустое решение в Visual Studio и добавьте проект веб-API ASP.NET 4. x для миграции:</span><span class="sxs-lookup"><span data-stu-id="68dcf-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="68dcf-126">В меню **Файл** выберите пункт **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="68dcf-127">Выберите шаблон **пустое решение** и нажмите кнопку **Далее**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="68dcf-128">Назовите решение *вебапимигратион*.</span><span class="sxs-lookup"><span data-stu-id="68dcf-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="68dcf-129">Щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-129">Select **Create**.</span></span>
1. <span data-ttu-id="68dcf-130">Добавьте существующий проект *продуктсапп* в решение.</span><span class="sxs-lookup"><span data-stu-id="68dcf-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="68dcf-131">Добавьте новый проект API для переноса в:</span><span class="sxs-lookup"><span data-stu-id="68dcf-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="68dcf-132">Добавьте в решение новый проект **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="68dcf-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="68dcf-133">В диалоговом окне **Настройка нового проекта** назовите проект *Продуктскоре*и выберите **создать**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="68dcf-134">В диалоговом окне **Создание веб-приложения ASP.NET Core** убедитесь в том, что выбраны платформы **.NET Core** и **ASP.NET Core 3.1**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="68dcf-135">Выберите шаблон проекта **API** и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="68dcf-136">Удалите файлы примеров *WeatherForecast.CS* и *Controllers/веасерфорекастконтроллер. CS* из нового проекта *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="68dcf-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="68dcf-137">Решение теперь содержит два проекта.</span><span class="sxs-lookup"><span data-stu-id="68dcf-137">The solution now contains two projects.</span></span> <span data-ttu-id="68dcf-138">В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="68dcf-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="68dcf-139">Миграция конфигурации</span><span class="sxs-lookup"><span data-stu-id="68dcf-139">Migrate configuration</span></span>

<span data-ttu-id="68dcf-140">ASP.NET Core не использует папку *App_Start* или файл *Global. asax* .</span><span class="sxs-lookup"><span data-stu-id="68dcf-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="68dcf-141">Кроме того, файл *Web. config* добавляется во время публикации.</span><span class="sxs-lookup"><span data-stu-id="68dcf-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="68dcf-142">Класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="68dcf-142">The `Startup` class:</span></span>

* <span data-ttu-id="68dcf-143">Заменяет *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="68dcf-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="68dcf-144">Обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="68dcf-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="68dcf-145">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="68dcf-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="68dcf-146">Перенос моделей и контроллеров</span><span class="sxs-lookup"><span data-stu-id="68dcf-146">Migrate models and controllers</span></span>

<span data-ttu-id="68dcf-147">В следующем коде показано, что `ProductsController` нужно обновить для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68dcf-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="68dcf-148">Обновите `ProductsController` для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68dcf-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="68dcf-149">Скопируйте *Controllers/продуктсконтроллер. CS* и папку *Models* из исходного проекта в новый.</span><span class="sxs-lookup"><span data-stu-id="68dcf-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="68dcf-150">Измените корневое пространство имен скопированных файлов на `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="68dcf-151">Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="68dcf-152">Следующие компоненты не существуют в ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68dcf-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="68dcf-153">Класс `ApiController`</span><span class="sxs-lookup"><span data-stu-id="68dcf-153">`ApiController` class</span></span>
* <span data-ttu-id="68dcf-154">Пространство имен `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="68dcf-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="68dcf-155">Интерфейс `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="68dcf-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="68dcf-156">Выполните следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="68dcf-156">Make the following changes:</span></span>

1. <span data-ttu-id="68dcf-157">Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="68dcf-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="68dcf-158">Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.</span><span class="sxs-lookup"><span data-stu-id="68dcf-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="68dcf-159">Удалите `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="68dcf-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="68dcf-160">Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="68dcf-161">Упростите `GetProduct` `return` инструкцию действия следующим образом:</span><span class="sxs-lookup"><span data-stu-id="68dcf-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="68dcf-162">Настройка маршрутизации</span><span class="sxs-lookup"><span data-stu-id="68dcf-162">Configure routing</span></span>

<span data-ttu-id="68dcf-163">Шаблон проекта ASP.NET Core *API* содержит конфигурацию маршрутизации конечных точек в созданном коде.</span><span class="sxs-lookup"><span data-stu-id="68dcf-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="68dcf-164">Следующие <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> вызовы и <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> вызывают:</span><span class="sxs-lookup"><span data-stu-id="68dcf-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="68dcf-165">Регистрация сопоставления маршрута и выполнения конечной точки в конвейере по [промежуточного слоя](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="68dcf-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="68dcf-166">Замените файл *App_Start/вебапиконфиг.КС* проекта *продуктсапп* .</span><span class="sxs-lookup"><span data-stu-id="68dcf-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="68dcf-167">Настройте маршрутизацию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="68dcf-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="68dcf-168">Пометьте `ProductsController` класс следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="68dcf-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="68dcf-169">Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера.</span><span class="sxs-lookup"><span data-stu-id="68dcf-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="68dcf-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.</span><span class="sxs-lookup"><span data-stu-id="68dcf-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="68dcf-171">Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="68dcf-172">Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут.</span><span class="sxs-lookup"><span data-stu-id="68dcf-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="68dcf-173">Токены:</span><span class="sxs-lookup"><span data-stu-id="68dcf-173">The tokens:</span></span>
    * <span data-ttu-id="68dcf-174">Сократите число волшебных строк в проекте.</span><span class="sxs-lookup"><span data-stu-id="68dcf-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="68dcf-175">Убедитесь, что маршруты синхронизированы с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.</span><span class="sxs-lookup"><span data-stu-id="68dcf-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="68dcf-176">Включить HTTP-запросы GET к `ProductController` действиям:</span><span class="sxs-lookup"><span data-stu-id="68dcf-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="68dcf-177">Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.</span><span class="sxs-lookup"><span data-stu-id="68dcf-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="68dcf-178">Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.</span><span class="sxs-lookup"><span data-stu-id="68dcf-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="68dcf-179">Запустите перенесенный проект и перейдите к `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="68dcf-180">Появится полный список из трех продуктов.</span><span class="sxs-lookup"><span data-stu-id="68dcf-180">A full list of three products appears.</span></span> <span data-ttu-id="68dcf-181">Перейдите по адресу `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="68dcf-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="68dcf-182">Появится первый продукт.</span><span class="sxs-lookup"><span data-stu-id="68dcf-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68dcf-183">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="68dcf-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="68dcf-184">Предварительные условия</span><span class="sxs-lookup"><span data-stu-id="68dcf-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="68dcf-185">Обзор проекта веб-API ASP.NET 4. x</span><span class="sxs-lookup"><span data-stu-id="68dcf-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="68dcf-186">В этой статье используется проект *продуктсапп* , созданный в [Начало работы с веб-API ASP.NET 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span><span class="sxs-lookup"><span data-stu-id="68dcf-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="68dcf-187">В этом проекте базовый проект веб-API ASP.NET 4. x настраивается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="68dcf-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="68dcf-188">В *Global.asax.CS*выполняется вызов `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="68dcf-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="68dcf-189">`WebApiConfig`Класс находится в папке *App_Start* и имеет статический `Register` метод:</span><span class="sxs-lookup"><span data-stu-id="68dcf-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="68dcf-190">Этот класс настраивает [маршрутизацию атрибутов](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), хотя на самом деле она не используется в проекте.</span><span class="sxs-lookup"><span data-stu-id="68dcf-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="68dcf-191">Он также настраивает таблицу маршрутизации, используемую веб-API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="68dcf-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="68dcf-192">В этом случае ASP.NET 4. x Web API ждет, что URL-адреса будут соответствовать формату `/api/{controller}/{id}` , с `{id}` необязательным.</span><span class="sxs-lookup"><span data-stu-id="68dcf-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="68dcf-193">В следующих разделах демонстрируется перенос проекта веб-API в ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="68dcf-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="68dcf-194">Создание целевого проекта</span><span class="sxs-lookup"><span data-stu-id="68dcf-194">Create the destination project</span></span>

<span data-ttu-id="68dcf-195">В Visual Studio выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="68dcf-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="68dcf-196">Последовательно выберите **файл**  >  **создать**  >  **проект**  >  **другие типы проектов**  >  **решения Visual Studio**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="68dcf-197">Выберите **пустое решение**и назовите решение *вебапимигратион*.</span><span class="sxs-lookup"><span data-stu-id="68dcf-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="68dcf-198">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="68dcf-198">Click the **OK** button.</span></span>
* <span data-ttu-id="68dcf-199">Добавьте существующий проект *продуктсапп* в решение.</span><span class="sxs-lookup"><span data-stu-id="68dcf-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="68dcf-200">Добавьте в решение новый проект **веб-приложения ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="68dcf-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="68dcf-201">Выберите целевую платформу **.NET Core** в раскрывающемся списке и выберите шаблон проекта **API** .</span><span class="sxs-lookup"><span data-stu-id="68dcf-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="68dcf-202">Назовите проект *продуктскоре*и нажмите кнопку **ОК** .</span><span class="sxs-lookup"><span data-stu-id="68dcf-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="68dcf-203">Решение теперь содержит два проекта.</span><span class="sxs-lookup"><span data-stu-id="68dcf-203">The solution now contains two projects.</span></span> <span data-ttu-id="68dcf-204">В следующих разделах описывается перенос содержимого проекта *продуктсапп* в проект *продуктскоре* .</span><span class="sxs-lookup"><span data-stu-id="68dcf-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="68dcf-205">Миграция конфигурации</span><span class="sxs-lookup"><span data-stu-id="68dcf-205">Migrate configuration</span></span>

<span data-ttu-id="68dcf-206">ASP.NET Core не использует:</span><span class="sxs-lookup"><span data-stu-id="68dcf-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="68dcf-207">*App_Startная* папка или файл *Global. asax*</span><span class="sxs-lookup"><span data-stu-id="68dcf-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="68dcf-208">файл *Web. config* добавляется во время публикации.</span><span class="sxs-lookup"><span data-stu-id="68dcf-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="68dcf-209">Класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="68dcf-209">The `Startup` class:</span></span>

* <span data-ttu-id="68dcf-210">Заменяет *Global. asax*.</span><span class="sxs-lookup"><span data-stu-id="68dcf-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="68dcf-211">Обрабатывает все задачи запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="68dcf-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="68dcf-212">Для получения дополнительной информации см. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="68dcf-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="68dcf-213">В ASP.NET Core MVC маршрутизация атрибутов включается по умолчанию при <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> вызове в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="68dcf-214">Следующий `UseMvc` вызов заменяет файл *App_Start/Вебапиконфиг.КС* проекта *продуктсапп* :</span><span class="sxs-lookup"><span data-stu-id="68dcf-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="68dcf-215">Перенос моделей и контроллеров</span><span class="sxs-lookup"><span data-stu-id="68dcf-215">Migrate models and controllers</span></span>

<span data-ttu-id="68dcf-216">В следующем коде показано `ProductsController` Обновление для ASP.NET Core.[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="68dcf-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="68dcf-217">Обновите `ProductsController` для ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68dcf-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="68dcf-218">Скопируйте *Controllers/продуктсконтроллер. CS* из исходного проекта в новый.</span><span class="sxs-lookup"><span data-stu-id="68dcf-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="68dcf-219">Скопируйте папку *Models* из исходного проекта в новую.</span><span class="sxs-lookup"><span data-stu-id="68dcf-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="68dcf-220">Измените корневое пространство имен скопированных файлов на `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="68dcf-221">Обновите `using ProductsApp.Models;` инструкцию до `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="68dcf-222">Следующие компоненты не существуют в ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="68dcf-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="68dcf-223">Класс `ApiController`</span><span class="sxs-lookup"><span data-stu-id="68dcf-223">`ApiController` class</span></span>
* <span data-ttu-id="68dcf-224">Пространство имен `System.Web.Http`</span><span class="sxs-lookup"><span data-stu-id="68dcf-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="68dcf-225">Интерфейс `IHttpActionResult`</span><span class="sxs-lookup"><span data-stu-id="68dcf-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="68dcf-226">Выполните следующие изменения:</span><span class="sxs-lookup"><span data-stu-id="68dcf-226">Make the following changes:</span></span>

1. <span data-ttu-id="68dcf-227">Измените `ApiController` на <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span><span class="sxs-lookup"><span data-stu-id="68dcf-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="68dcf-228">Добавьте, `using Microsoft.AspNetCore.Mvc;` чтобы разрешить `ControllerBase` ссылку.</span><span class="sxs-lookup"><span data-stu-id="68dcf-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="68dcf-229">Удалите `using System.Web.Http;`.</span><span class="sxs-lookup"><span data-stu-id="68dcf-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="68dcf-230">Измените `GetProduct` тип возвращаемого значения действия с `IHttpActionResult` на `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="68dcf-231">Упростите `GetProduct` `return` инструкцию действия следующим образом:</span><span class="sxs-lookup"><span data-stu-id="68dcf-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="68dcf-232">Настройка маршрутизации</span><span class="sxs-lookup"><span data-stu-id="68dcf-232">Configure routing</span></span>

<span data-ttu-id="68dcf-233">Настройте маршрутизацию следующим образом.</span><span class="sxs-lookup"><span data-stu-id="68dcf-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="68dcf-234">Пометьте `ProductsController` класс следующими атрибутами:</span><span class="sxs-lookup"><span data-stu-id="68dcf-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="68dcf-235">Предыдущий [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) атрибут настраивает шаблон маршрутизации атрибутов контроллера.</span><span class="sxs-lookup"><span data-stu-id="68dcf-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="68dcf-236">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Атрибут делает маршрутизацию атрибутов требованием для всех действий в этом контроллере.</span><span class="sxs-lookup"><span data-stu-id="68dcf-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="68dcf-237">Маршрутизация атрибутов поддерживает маркеры, такие как `[controller]` и `[action]` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="68dcf-238">Во время выполнения каждый токен заменяется именем контроллера или действия соответственно, к которому был применен атрибут.</span><span class="sxs-lookup"><span data-stu-id="68dcf-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="68dcf-239">Токены уменьшают количество волшебных строк в проекте.</span><span class="sxs-lookup"><span data-stu-id="68dcf-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="68dcf-240">Кроме того, токены обеспечивают синхронизацию маршрутов с соответствующими контроллерами и действиями при применении рефакторинга автоматического переименования.</span><span class="sxs-lookup"><span data-stu-id="68dcf-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="68dcf-241">Задайте для режима совместимости проекта значение ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="68dcf-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="68dcf-242">Предыдущее изменение:</span><span class="sxs-lookup"><span data-stu-id="68dcf-242">The preceding change:</span></span>

    * <span data-ttu-id="68dcf-243">Требуется для использования `[ApiController]` атрибута на уровне контроллера.</span><span class="sxs-lookup"><span data-stu-id="68dcf-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="68dcf-244">Может привести к возможным нарушениям поведений, появившимся в ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="68dcf-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="68dcf-245">Включить HTTP-запросы GET к `ProductController` действиям:</span><span class="sxs-lookup"><span data-stu-id="68dcf-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="68dcf-246">Примените [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) атрибут к `GetAllProducts` действию.</span><span class="sxs-lookup"><span data-stu-id="68dcf-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="68dcf-247">Примените `[HttpGet("{id}")]` атрибут к `GetProduct` действию.</span><span class="sxs-lookup"><span data-stu-id="68dcf-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="68dcf-248">Запустите перенесенный проект и перейдите к `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="68dcf-249">Появится полный список из трех продуктов.</span><span class="sxs-lookup"><span data-stu-id="68dcf-249">A full list of three products appears.</span></span> <span data-ttu-id="68dcf-250">Перейдите по адресу `/api/products/1`.</span><span class="sxs-lookup"><span data-stu-id="68dcf-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="68dcf-251">Появится первый продукт.</span><span class="sxs-lookup"><span data-stu-id="68dcf-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="68dcf-252">Оболочка совместимости</span><span class="sxs-lookup"><span data-stu-id="68dcf-252">Compatibility shim</span></span>

<span data-ttu-id="68dcf-253">Библиотека [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) предоставляет оболочку совместимости для перемещения проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68dcf-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="68dcf-254">Оболочка совместимости расширяет ASP.NET Core для поддержки ряда соглашений от ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="68dcf-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="68dcf-255">Пример, переданный ранее в этом документе, достаточно прост в том, что оболочка совместимости не была нужна.</span><span class="sxs-lookup"><span data-stu-id="68dcf-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="68dcf-256">Для более крупных проектов использование оболочки совместимости может оказаться полезным для временного разделения зазора API между ASP.NET Core и ASP.NET 4. x Web API 2.</span><span class="sxs-lookup"><span data-stu-id="68dcf-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="68dcf-257">Оболочка совместимости веб-API предназначена для использования в качестве временной меры для поддержки переноса больших проектов веб-API ASP.NET 4. x в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="68dcf-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="68dcf-258">Со временем проекты должны быть обновлены для использования шаблонов ASP.NET Core, а не полагаться на оболочку совместимости.</span><span class="sxs-lookup"><span data-stu-id="68dcf-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="68dcf-259">Функции обеспечения совместимости, включенные в `Microsoft.AspNetCore.Mvc.WebApiCompatShim` :</span><span class="sxs-lookup"><span data-stu-id="68dcf-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="68dcf-260">Добавляет `ApiController` тип, поэтому не требуется обновлять базовые типы контроллеров.</span><span class="sxs-lookup"><span data-stu-id="68dcf-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="68dcf-261">Включает привязку модели в стиле веб-API.</span><span class="sxs-lookup"><span data-stu-id="68dcf-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="68dcf-262">ASP.NET Core функция привязки модели MVC аналогична ASP.NET 4. x MVC 5 по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="68dcf-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="68dcf-263">Оболочка совместимости изменяет привязку модели так, чтобы она была похожа на соглашения о привязке модели Web API 2 ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="68dcf-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="68dcf-264">Например, сложные типы автоматически привязываются из текста запроса.</span><span class="sxs-lookup"><span data-stu-id="68dcf-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="68dcf-265">Расширяет привязку модели, чтобы действия контроллера могли принимать параметры типа `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="68dcf-266">Добавляет модули форматирования сообщений, позволяя действиям возвращать результаты типа `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="68dcf-267">Добавляет дополнительные методы ответа, которые могут использоваться действиями веб-API 2 для обслуживания ответов:</span><span class="sxs-lookup"><span data-stu-id="68dcf-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="68dcf-268">`HttpResponseMessage`Каждый</span><span class="sxs-lookup"><span data-stu-id="68dcf-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="68dcf-269">Методы результата действия:</span><span class="sxs-lookup"><span data-stu-id="68dcf-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="68dcf-270">Добавляет экземпляр `IContentNegotiator` в контейнер внедрения зависимостей (DI) приложения и делает доступными типы, связанные с согласованием содержимого, из [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span><span class="sxs-lookup"><span data-stu-id="68dcf-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="68dcf-271">Примерами таких типов являются `DefaultContentNegotiator` и `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="68dcf-272">Чтобы использовать оболочку совместимости, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="68dcf-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="68dcf-273">Установите пакет NuGet [Microsoft. AspNetCore. MVC. вебапикомпатшим](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .</span><span class="sxs-lookup"><span data-stu-id="68dcf-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="68dcf-274">Зарегистрируйте службы оболочки совместимости с контейнером внедрения приложения, вызвав `services.AddMvc().AddWebApiConventions()` в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="68dcf-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="68dcf-275">Определите зависящие от веб-API маршруты с помощью в в `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` вызове приложения.</span><span class="sxs-lookup"><span data-stu-id="68dcf-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68dcf-276">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="68dcf-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
