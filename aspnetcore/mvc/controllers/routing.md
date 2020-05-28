---
<span data-ttu-id="67a5e-101">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-102">'Blazor'</span></span>
- <span data-ttu-id="67a5e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-103">'Identity'</span></span>
- <span data-ttu-id="67a5e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-105">'Razor'</span></span>
- <span data-ttu-id="67a5e-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="67a5e-107">Маршрутизация к действиям контроллера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67a5e-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="67a5e-108">Авторы: [Райан Новак](https://github.com/rynowak) (Ryan Nowak), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="67a5e-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="67a5e-109">Контроллеры ASP.NET Core используют по [промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов и сопоставления их с [действиями](#action).</span><span class="sxs-lookup"><span data-stu-id="67a5e-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="67a5e-110">Шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-110">Routes templates:</span></span>

* <span data-ttu-id="67a5e-111">Определяются в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="67a5e-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="67a5e-112">Описание способов сопоставления путей URL-адресов с [действиями](#action).</span><span class="sxs-lookup"><span data-stu-id="67a5e-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="67a5e-113">Используются для создания URL-адресов для ссылок.</span><span class="sxs-lookup"><span data-stu-id="67a5e-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="67a5e-114">Созданные ссылки обычно возвращаются в ответах.</span><span class="sxs-lookup"><span data-stu-id="67a5e-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="67a5e-115">Действия либо [направляются по соглашению](#cr) , либо [маршрутизируются по атрибуту](#ar).</span><span class="sxs-lookup"><span data-stu-id="67a5e-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="67a5e-116">При размещении маршрута на контроллере или [действии](#action) он пересылается по атрибуту.</span><span class="sxs-lookup"><span data-stu-id="67a5e-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="67a5e-117">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="67a5e-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="67a5e-118">Этот документ:</span><span class="sxs-lookup"><span data-stu-id="67a5e-118">This document:</span></span>

* <span data-ttu-id="67a5e-119">Объясняет взаимодействие между MVC и маршрутизацией.</span><span class="sxs-lookup"><span data-stu-id="67a5e-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="67a5e-120">Как обычные приложения MVC используют функции маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="67a5e-121">Рассматриваются оба:</span><span class="sxs-lookup"><span data-stu-id="67a5e-121">Covers both:</span></span>
    * <span data-ttu-id="67a5e-122">[Соглашение о маршрутизации](#cr) обычно используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="67a5e-123">*Маршрутизация атрибутов* , используемая с API-интерфейсами RESTful.</span><span class="sxs-lookup"><span data-stu-id="67a5e-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="67a5e-124">Если вы в основном заинтересованы в маршрутизации для API-интерфейсов RESTFUL, перейдите к разделу [Маршрутизация атрибутов для интерфейсов API для остальных](#ar) компонентов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="67a5e-125">Дополнительные сведения о маршрутизации см. в разделе [Маршрутизация](xref:fundamentals/routing) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="67a5e-126">Относится к системе маршрутизации по умолчанию, добавленной в ASP.NET Core 3,0, называемую маршрутизацией конечных точек.</span><span class="sxs-lookup"><span data-stu-id="67a5e-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="67a5e-127">В целях совместимости можно использовать контроллеры с предыдущей версией маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="67a5e-128">Инструкции см. в [руководству по миграции 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="67a5e-129">Справочные материалы по устаревшей системе маршрутизации см. в [версии 2,2 этого документа](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="67a5e-130">Настройка обычного маршрута</span><span class="sxs-lookup"><span data-stu-id="67a5e-130">Set up conventional route</span></span>

<span data-ttu-id="67a5e-131">`Startup.Configure`При использовании [обычной маршрутизации](#crd)обычно имеет код, аналогичный приведенному ниже.</span><span class="sxs-lookup"><span data-stu-id="67a5e-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="67a5e-132">Внутри вызова метод <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> используется для создания одного маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="67a5e-133">Единственный маршрут называется `default` Route.</span><span class="sxs-lookup"><span data-stu-id="67a5e-133">The single route is named `default` route.</span></span> <span data-ttu-id="67a5e-134">Большинство приложений с контроллерами и представлениями используют шаблон маршрута, аналогичный `default` маршруту.</span><span class="sxs-lookup"><span data-stu-id="67a5e-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="67a5e-135">Интерфейсы API-интерфейсов для интерфейса остальных должны использовать [маршрутизацию атрибутов](#ar).</span><span class="sxs-lookup"><span data-stu-id="67a5e-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="67a5e-136">Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="67a5e-137">Соответствует URL-пути, например`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="67a5e-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="67a5e-138">Извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем маркировки пути.</span><span class="sxs-lookup"><span data-stu-id="67a5e-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="67a5e-139">Извлечение значений маршрута приводит к совпадению, если у приложения есть контроллер с именем `ProductsController` и `Details` действие:</span><span class="sxs-lookup"><span data-stu-id="67a5e-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="67a5e-140">`/Products/Details/5`модель привязывает значение, `id = 5` чтобы задать `id` параметр `5` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="67a5e-141">Дополнительные сведения см. в разделе [Привязка модели](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="67a5e-142">`{controller=Home}`Определяет `Home` как значение по умолчанию `controller` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="67a5e-143">`{action=Index}`Определяет `Index` как значение по умолчанию `action` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="67a5e-144">`?`Символ в `{id?}` определяет `id` как необязательный.</span><span class="sxs-lookup"><span data-stu-id="67a5e-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="67a5e-145">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="67a5e-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="67a5e-146">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="67a5e-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="67a5e-147">Соответствует пути URL-адреса `/` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="67a5e-148">Создает значения маршрута `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="67a5e-149">Значения для `controller` и `action` используют значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="67a5e-150">`id`не создает значение, так как в URL-пути нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="67a5e-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="67a5e-151">`/`соответствует, только если существует `HomeController` действие и `Index` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="67a5e-152">Используя предыдущее определение контроллера и шаблон маршрута, `HomeController.Index` действие выполняется для следующих URL-путей:</span><span class="sxs-lookup"><span data-stu-id="67a5e-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="67a5e-153">URL-путь `/` использует `Home` контроллеры и действие шаблона маршрута по умолчанию `Index` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="67a5e-154">URL-путь `/Home` использует действие по умолчанию шаблона маршрута `Index` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="67a5e-155">Универсальный метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="67a5e-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="67a5e-156">Меняющей</span><span class="sxs-lookup"><span data-stu-id="67a5e-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="67a5e-157">Маршрутизация настраивается с помощью по <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> промежуточного слоя и.</span><span class="sxs-lookup"><span data-stu-id="67a5e-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="67a5e-158">Использование контроллеров:</span><span class="sxs-lookup"><span data-stu-id="67a5e-158">To use controllers:</span></span>
>
> * <span data-ttu-id="67a5e-159">Вызовите <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> внутри `UseEndpoints` , чтобы сопоставлять [атрибуты, перенаправляемые](#ar) контроллерам.</span><span class="sxs-lookup"><span data-stu-id="67a5e-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="67a5e-160">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> или <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> , чтобы связать как [соглашение с маршрутизацией](#cr) , так и [перенаправляемые](#ar) контроллеры.</span><span class="sxs-lookup"><span data-stu-id="67a5e-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="67a5e-161">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-161">Conventional routing</span></span>

<span data-ttu-id="67a5e-162">Стандартная маршрутизация используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="67a5e-163">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="67a5e-164">является примером *маршрутизации на основе соглашений*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="67a5e-165">Он называется *обычной маршрутизацией* , так как он устанавливает *соглашение* для URL-путей:</span><span class="sxs-lookup"><span data-stu-id="67a5e-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="67a5e-166">Первый сегмент пути, `{controller=Home}` сопоставляется с именем контроллера.</span><span class="sxs-lookup"><span data-stu-id="67a5e-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="67a5e-167">Второй сегмент, `{action=Index}` сопоставляется с именем [действия](#action) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="67a5e-168">Третий сегмент `{id?}` используется для необязательного `id` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="67a5e-169">`?`В `{id?}` делает его необязательным.</span><span class="sxs-lookup"><span data-stu-id="67a5e-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="67a5e-170">`id`используется для соотнесения с сущностью модели.</span><span class="sxs-lookup"><span data-stu-id="67a5e-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="67a5e-171">При использовании этого `default` маршрута путь URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="67a5e-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="67a5e-172">`/Products/List`сопоставляется с `ProductsController.List` действием.</span><span class="sxs-lookup"><span data-stu-id="67a5e-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="67a5e-173">`/Blog/Article/17`сопоставляется с `BlogController.Article` и, как правило, модель привязывает `id` параметр к 17.</span><span class="sxs-lookup"><span data-stu-id="67a5e-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="67a5e-174">Это сопоставление:</span><span class="sxs-lookup"><span data-stu-id="67a5e-174">This mapping:</span></span>

* <span data-ttu-id="67a5e-175">Основаны **только**на именах контроллера и [действий](#action) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="67a5e-176">Не основано на пространствах имен, расположениях исходных файлов или параметров метода.</span><span class="sxs-lookup"><span data-stu-id="67a5e-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="67a5e-177">Использование обычной маршрутизации с маршрутом по умолчанию позволяет создавать приложения без необходимости создания нового шаблона URL-адреса для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="67a5e-178">Для приложения с действиями в стиле [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) с согласованностью URL-адресов на контроллерах:</span><span class="sxs-lookup"><span data-stu-id="67a5e-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="67a5e-179">Помогает упростить код.</span><span class="sxs-lookup"><span data-stu-id="67a5e-179">Helps simplify the code.</span></span>
* <span data-ttu-id="67a5e-180">Делает пользовательский интерфейс более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="67a5e-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="67a5e-181">`id`В предыдущем коде шаблон маршрута определен как необязательный.</span><span class="sxs-lookup"><span data-stu-id="67a5e-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="67a5e-182">Действия могут выполняться без дополнительного идентификатора, предоставленного в качестве части URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="67a5e-183">Как правило, если `id` параметр опущен из URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="67a5e-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="67a5e-184">`id`задается `0` привязкой модели.</span><span class="sxs-lookup"><span data-stu-id="67a5e-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="67a5e-185">Сущность не найдена в соответствующей базе данных `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="67a5e-186">[Маршрутизация атрибутов](#ar) обеспечивает детальный контроль, чтобы сделать идентификатор необходимым для некоторых действий, а не для других.</span><span class="sxs-lookup"><span data-stu-id="67a5e-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="67a5e-187">По соглашению в документации содержатся необязательные параметры, например, `id` если они, скорее всего, будут отображаться в правильном использовании.</span><span class="sxs-lookup"><span data-stu-id="67a5e-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="67a5e-188">Для большинства приложений следует выбрать базовую описательную схему маршрутизации таким образом, чтобы URL-адреса были удобочитаемыми и осмысленными.</span><span class="sxs-lookup"><span data-stu-id="67a5e-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="67a5e-189">Традиционный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="67a5e-190">Поддерживает основную и описательную схемы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="67a5e-191">Является отправной точкой для приложений на базе пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="67a5e-192">— Единственный шаблон маршрута, необходимый для многих приложений пользовательского веб-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="67a5e-193">Для больших веб-приложений пользовательского интерфейса другой маршрут, использующий [области](#areas) , если это необходимо.</span><span class="sxs-lookup"><span data-stu-id="67a5e-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="67a5e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>и <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="67a5e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="67a5e-195">Автоматически назначить значение **заказа** своим конечным точкам в соответствии с порядком их вызова.</span><span class="sxs-lookup"><span data-stu-id="67a5e-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="67a5e-196">Маршрутизация конечных точек в ASP.NET Core 3.0 и более поздних версий</span><span class="sxs-lookup"><span data-stu-id="67a5e-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="67a5e-197">Не имеет концепции маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="67a5e-198">Не предоставляет гарантий упорядочения для выполнения расширяемости, все конечные точки обрабатываются одновременно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="67a5e-199">Чтобы увидеть, как встроенные реализации маршрутизации, такие как <xref:Microsoft.AspNetCore.Routing.Route>, сопоставляются с запросами, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="67a5e-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="67a5e-200">[Маршрутизация атрибутов](#ar) объясняется далее в этом документе.</span><span class="sxs-lookup"><span data-stu-id="67a5e-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="67a5e-201">Несколько обычных маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-201">Multiple conventional routes</span></span>

<span data-ttu-id="67a5e-202">В можно добавить несколько [обычных маршрутов](#cr) `UseEndpoints` , добавив дополнительные вызовы в <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="67a5e-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="67a5e-203">Это позволяет определить несколько соглашений или добавить традиционные маршруты, предназначенные для конкретного [действия](#action), например:</span><span class="sxs-lookup"><span data-stu-id="67a5e-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="67a5e-204">`blog`Маршрут в приведенном выше коде является **выделенным обычным маршрутом**.</span><span class="sxs-lookup"><span data-stu-id="67a5e-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="67a5e-205">Он называется выделенным обычным маршрутом по следующим причинам.</span><span class="sxs-lookup"><span data-stu-id="67a5e-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="67a5e-206">В нем используется [Обычная маршрутизация](#cr).</span><span class="sxs-lookup"><span data-stu-id="67a5e-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="67a5e-207">Он предназначен для конкретного [действия](#action).</span><span class="sxs-lookup"><span data-stu-id="67a5e-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="67a5e-208">Поскольку `controller` и `action` не отображаются в шаблоне маршрута в `"blog/{*article}"` качестве параметров:</span><span class="sxs-lookup"><span data-stu-id="67a5e-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="67a5e-209">Они могут иметь только значения по умолчанию `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="67a5e-210">Этот маршрут всегда сопоставлен с действием `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="67a5e-211">`/Blog`, `/Blog/Article` и `/Blog/{any-string}` — это единственные URL-пути, соответствующие маршруту блога.</span><span class="sxs-lookup"><span data-stu-id="67a5e-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="67a5e-212">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-212">The preceding example:</span></span>

* <span data-ttu-id="67a5e-213">`blog`маршрут имеет более высокий приоритет для совпадений, чем `default` маршрут, так как он добавляется первым.</span><span class="sxs-lookup"><span data-stu-id="67a5e-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="67a5e-214">— И пример маршрутизации [по стилю](https://developer.mozilla.org/docs/Glossary/Slug) , в которой в качестве части URL-адреса обычно используется имя статьи.</span><span class="sxs-lookup"><span data-stu-id="67a5e-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="67a5e-215">В ASP.NET Core 3,0 и более поздних версиях маршрутизация не выполняет:</span><span class="sxs-lookup"><span data-stu-id="67a5e-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="67a5e-216">Определите концепцию, называемую *маршрутом*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-216">Define a concept called a *route*.</span></span> <span data-ttu-id="67a5e-217">`UseRouting` добавляет соответствие маршрута в конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="67a5e-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="67a5e-218">По `UseRouting` промежуточного слоя просматривает набор конечных точек, определенных в приложении, и выбирает наилучшее совпадение конечных точек на основе запроса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="67a5e-219">Предоставьте гарантии порядка выполнения расширяемости, например <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> или <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="67a5e-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="67a5e-220">См. раздел о [маршрутизации](xref:fundamentals/routing) для справочных материалов по маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="67a5e-221">Стандартный порядок маршрутизации</span><span class="sxs-lookup"><span data-stu-id="67a5e-221">Conventional routing order</span></span>

<span data-ttu-id="67a5e-222">Обычная маршрутизация соответствует только комбинации действий и контроллера, определенных приложением.</span><span class="sxs-lookup"><span data-stu-id="67a5e-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="67a5e-223">Это предназначено для упрощения случаев, когда обычные маршруты перекрываются.</span><span class="sxs-lookup"><span data-stu-id="67a5e-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="67a5e-224">Добавление маршрутов с помощью <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> автоматически назначает значение порядка своим конечным точкам в соответствии с порядком их вызова.</span><span class="sxs-lookup"><span data-stu-id="67a5e-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="67a5e-225">Совпадает с более высоким приоритетом на маршруте, который отображается ранее.</span><span class="sxs-lookup"><span data-stu-id="67a5e-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="67a5e-226">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="67a5e-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="67a5e-227">Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="67a5e-228">[Выделенные традиционные маршруты](#dcr) с перекрестными параметрами маршрутов `{*article}` , например, могут сделать маршрут слишком [жадным](xref:fundamentals/routing#greedy), то есть сопоставлять URL-адреса, которые вы хотели бы сопоставить с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="67a5e-229">Помещайте жадные маршруты позже в таблице маршрутов, чтобы предотвратить жадные соответствия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="67a5e-230">Разрешение неоднозначных действий</span><span class="sxs-lookup"><span data-stu-id="67a5e-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="67a5e-231">При совпадении двух конечных точек через маршрутизацию маршрутизация должна выполнить одно из следующих действий:</span><span class="sxs-lookup"><span data-stu-id="67a5e-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="67a5e-232">Выберите лучший кандидат.</span><span class="sxs-lookup"><span data-stu-id="67a5e-232">Choose the best candidate.</span></span>
* <span data-ttu-id="67a5e-233">Создание исключения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-233">Throw an exception.</span></span>

<span data-ttu-id="67a5e-234">Пример:</span><span class="sxs-lookup"><span data-stu-id="67a5e-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="67a5e-235">Предыдущий контроллер определяет два соответствующих действия:</span><span class="sxs-lookup"><span data-stu-id="67a5e-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="67a5e-236">URL-путь`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="67a5e-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="67a5e-237">Перенаправление данных `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="67a5e-238">Это типичный шаблон для контроллеров MVC:</span><span class="sxs-lookup"><span data-stu-id="67a5e-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="67a5e-239">`Edit(int)`Отображает форму для изменения продукта.</span><span class="sxs-lookup"><span data-stu-id="67a5e-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="67a5e-240">`Edit(int, Product)`обрабатывает опубликованную форму.</span><span class="sxs-lookup"><span data-stu-id="67a5e-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="67a5e-241">Чтобы разрешить правильный маршрут, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-241">To resolve the correct route:</span></span>

* <span data-ttu-id="67a5e-242">`Edit(int, Product)`выбирается, если запрос является HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="67a5e-243">`Edit(int)`выбран, если [команда HTTP](#verb) является любым другим.</span><span class="sxs-lookup"><span data-stu-id="67a5e-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="67a5e-244">`Edit(int)`обычно вызывается через `GET` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="67a5e-245">Объект <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , предоставляется для маршрутизации, чтобы его можно было выбрать в зависимости от метода HTTP запроса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="67a5e-246">`HttpPostAttribute` `Edit(int, Product)` Чем больше, тем лучше соответствует `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="67a5e-247">Важно понимать роль таких атрибутов, как `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="67a5e-248">Аналогичные атрибуты определяются для других [HTTP-команд](#verb).</span><span class="sxs-lookup"><span data-stu-id="67a5e-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="67a5e-249">В [обычной маршрутизации](#cr)для действий используется одно и то же имя действия, когда они входят в форму отображения, Рабочий процесс отправки формы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="67a5e-250">Например, см. раздел [изучение двух методов действия Edit](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="67a5e-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="67a5e-251">Если службе маршрутизации не удается выбрать лучший вариант, <xref:System.Reflection.AmbiguousMatchException> создается исключение, в котором перечисляются несколько совпадающих конечных точек.</span><span class="sxs-lookup"><span data-stu-id="67a5e-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="67a5e-252">Имена обычных маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-252">Conventional route names</span></span>

<span data-ttu-id="67a5e-253">Строки `"blog"` и `"default"` в следующих примерах являются обычными именами маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="67a5e-254">Имена маршрутов придают маршруту логическое имя.</span><span class="sxs-lookup"><span data-stu-id="67a5e-254">The route names give the route a logical name.</span></span> <span data-ttu-id="67a5e-255">Именованный маршрут можно использовать для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="67a5e-256">Использование именованного маршрута упрощает создание URL-адресов, когда порядок маршрутов может усложнить создание URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="67a5e-257">Имена маршрутов должны быть уникальными для всего приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="67a5e-258">Имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-258">Route names:</span></span>

* <span data-ttu-id="67a5e-259">Не влияют на сопоставление URL-адресов или обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="67a5e-260">Используются только для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-260">Are used only for URL generation.</span></span>

<span data-ttu-id="67a5e-261">Концепция имени маршрута представлена в маршрутизации как [иендпоинтнамеметадата](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="67a5e-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="67a5e-262">Термины **имя маршрута** и **имя конечной точки**:</span><span class="sxs-lookup"><span data-stu-id="67a5e-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="67a5e-263">Являются взаимозаменяемыми.</span><span class="sxs-lookup"><span data-stu-id="67a5e-263">Are interchangeable.</span></span>
* <span data-ttu-id="67a5e-264">То, какой из них используется в документации и коде, зависит от описываемого API.</span><span class="sxs-lookup"><span data-stu-id="67a5e-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="67a5e-265">Маршрутизация атрибутов для интерфейсов API RESTFUL</span><span class="sxs-lookup"><span data-stu-id="67a5e-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="67a5e-266">Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены [HTTP-командами](#verb), интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="67a5e-267">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="67a5e-268">Следующий `StartUp.Configure` код является типичным для REST API и используется в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="67a5e-269">В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> вызывается внутри, `UseEndpoints` чтобы сопоставлять атрибуты, перенаправляемые контроллерами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="67a5e-270">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-270">In the following example:</span></span>

* <span data-ttu-id="67a5e-271">Используется предыдущий `Configure` метод.</span><span class="sxs-lookup"><span data-stu-id="67a5e-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="67a5e-272">`HomeController`совпадает с набором URL-адресов, похожим на стандартный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="67a5e-273">`HomeController.Index`Действие выполняется для любого из URL-путей,, `/` `/Home` `/Home/Index` или `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="67a5e-274">В этом примере демонстрируется ключевое различие в программировании между маршрутизацией атрибутов и [обычной маршрутизацией](#cr).</span><span class="sxs-lookup"><span data-stu-id="67a5e-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="67a5e-275">Маршрутизация атрибутов требует больше входных данных для указания маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="67a5e-276">Стандартный маршрут по умолчанию обрабатывает маршруты более кратко.</span><span class="sxs-lookup"><span data-stu-id="67a5e-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="67a5e-277">Однако маршрутизация атрибутов разрешает и требует точного контроля над тем, какие шаблоны маршрутов применяются к каждому [действию](#action).</span><span class="sxs-lookup"><span data-stu-id="67a5e-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="67a5e-278">При использовании маршрутизации атрибутов контроллер и имена действий воспроизводятся, если не используется [замена токенов](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-278">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="67a5e-279">В следующем примере совпадают те же URL-адреса, что и в предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-279">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="67a5e-280">В следующем коде используется замена токенов для `action` и `controller` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-280">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="67a5e-281">Следующий код применяется `[Route("[controller]/[action]")]` к контроллеру:</span><span class="sxs-lookup"><span data-stu-id="67a5e-281">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="67a5e-282">В приведенном выше коде `Index` шаблоны методов должны находиться в начале `/` или `~/` в шаблонах маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-282">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="67a5e-283">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="67a5e-283">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="67a5e-284">Сведения о выборе шаблона маршрутов см. в разделе [приоритет шаблона маршрута](xref:fundamentals/routing#rtp) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-284">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="67a5e-285">Зарезервированные имена маршрутизации</span><span class="sxs-lookup"><span data-stu-id="67a5e-285">Reserved routing names</span></span>

<span data-ttu-id="67a5e-286">Следующие ключевые слова являются зарезервированными именами параметров маршрута при использовании контроллеров или Razor страниц:</span><span class="sxs-lookup"><span data-stu-id="67a5e-286">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="67a5e-287">Использование `page` в качестве параметра маршрута с маршрутизацией атрибутов является распространенной ошибкой.</span><span class="sxs-lookup"><span data-stu-id="67a5e-287">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="67a5e-288">Это приводит к несогласованности и путанице при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-288">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="67a5e-289">Специальные имена параметров используются при формировании URL-адресов, чтобы определить, относится ли операция создания URL-адреса к Razor странице или к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="67a5e-289">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="67a5e-290">Шаблоны HTTP-команд</span><span class="sxs-lookup"><span data-stu-id="67a5e-290">HTTP verb templates</span></span>

<span data-ttu-id="67a5e-291">ASP.NET Core содержит следующие шаблоны HTTP-команд:</span><span class="sxs-lookup"><span data-stu-id="67a5e-291">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="67a5e-292">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-292">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="67a5e-293">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-293">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="67a5e-294">[[Хттппут]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-294">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="67a5e-295">[[Хттпделете]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-295">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="67a5e-296">[[Хттфеад]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-296">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="67a5e-297">[[Хттппатч]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-297">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="67a5e-298">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-298">Route templates</span></span>

<span data-ttu-id="67a5e-299">ASP.NET Core имеет следующие шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-299">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="67a5e-300">Все [шаблоны HTTP-команд](#verb) являются шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-300">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="67a5e-301">[Направлены](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="67a5e-301">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="67a5e-302">Маршрутизация атрибутов с помощью атрибутов глагола HTTP</span><span class="sxs-lookup"><span data-stu-id="67a5e-302">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="67a5e-303">Рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="67a5e-303">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="67a5e-304">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="67a5e-304">In the preceding code:</span></span>

* <span data-ttu-id="67a5e-305">Каждое действие содержит `[HttpGet]` атрибут, который ограничивает сопоставление только запросами HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="67a5e-305">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="67a5e-306">`GetProduct`Действие включает `"{id}"` шаблон, поэтому `id` добавляется к `"api/[controller]"` шаблону на контроллере.</span><span class="sxs-lookup"><span data-stu-id="67a5e-306">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="67a5e-307">Шаблон методов — `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-307">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="67a5e-308">Поэтому это действие соответствует только запросам Get для формы `/api/test2/xyz` ,, `/api/test2/123` `/api/test2/{any string}` и т. д.</span><span class="sxs-lookup"><span data-stu-id="67a5e-308">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="67a5e-309">`GetIntProduct`Действие содержит `"int/{id:int}")` шаблон.</span><span class="sxs-lookup"><span data-stu-id="67a5e-309">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="67a5e-310">`:int`Часть шаблона ограничивает `id` значения маршрута строками, которые могут быть преобразованы в целые числа.</span><span class="sxs-lookup"><span data-stu-id="67a5e-310">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="67a5e-311">Запрос GET к `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-311">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="67a5e-312">Не соответствует этому действию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-312">Doesn't match this action.</span></span>
  * <span data-ttu-id="67a5e-313">Возвращает ошибку [404 не найден](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-313">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="67a5e-314">`GetInt2Product`Действие содержит `{id}` в шаблоне, но не ограничивает `id` значения, которые можно преобразовать в целое число.</span><span class="sxs-lookup"><span data-stu-id="67a5e-314">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="67a5e-315">Запрос GET к `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-315">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="67a5e-316">Соответствует этому маршруту.</span><span class="sxs-lookup"><span data-stu-id="67a5e-316">Matches this route.</span></span>
  * <span data-ttu-id="67a5e-317">Привязка модели не может преобразовать `abc` в целое число.</span><span class="sxs-lookup"><span data-stu-id="67a5e-317">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="67a5e-318">`id`Параметр метода имеет тип Integer.</span><span class="sxs-lookup"><span data-stu-id="67a5e-318">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="67a5e-319">Возвращает [400 Неверный запрос](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , так как привязка модели не смогла преобразовать `abc` в целое число.</span><span class="sxs-lookup"><span data-stu-id="67a5e-319">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="67a5e-320">Маршрутизация атрибутов может использовать <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> такие атрибуты <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , как, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> и <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="67a5e-320">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="67a5e-321">Все атрибуты [HTTP-команды](#verb) принимают шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-321">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="67a5e-322">В следующем примере показаны два действия, которые соответствуют одному шаблону маршрута:</span><span class="sxs-lookup"><span data-stu-id="67a5e-322">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="67a5e-323">Использование URL-пути `/products3` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-323">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="67a5e-324">`MyProductsController.ListProducts`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `GET` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-324">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="67a5e-325">`MyProductsController.CreateProduct`Действие выполняется, когда [HTTP-команда](#verb) имеет значение `POST` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-325">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="67a5e-326">При создании REST API в редких случаях необходимо использовать `[Route(...)]` метод действия, поскольку действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="67a5e-326">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="67a5e-327">Лучше использовать более конкретный [атрибут HTTP-команды](#verb) , чтобы точно определить, что поддерживает API.</span><span class="sxs-lookup"><span data-stu-id="67a5e-327">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="67a5e-328">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-328">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="67a5e-329">Для моделирования функциональных возможностей приложения в качестве набора ресурсов, в которых операции представлены HTTP-командами, интерфейсы API-интерфейса должны использовать маршрутизацию атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-329">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="67a5e-330">Это означает, что многие операции, например GET и POST для одного и того же логического ресурса, используют один и тот же URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="67a5e-330">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="67a5e-331">Маршрутизация с помощью атрибутов обеспечивает необходимый уровень контроля, позволяющий тщательно разработать схему общедоступных конечных точек API-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-331">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="67a5e-332">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-332">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="67a5e-333">В следующем примере требуется в `id` качестве части URL-пути:</span><span class="sxs-lookup"><span data-stu-id="67a5e-333">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="67a5e-334">`Products2ApiController.GetProduct(int)`Действие:</span><span class="sxs-lookup"><span data-stu-id="67a5e-334">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="67a5e-335">Выполняется с URL-путем, например`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="67a5e-335">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="67a5e-336">Не выполняется с URL-адресом `/products2` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-336">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="67a5e-337">Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-337">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="67a5e-338">Дополнительные сведения см. [в разделе Определение поддерживаемых типов содержимого запросов с помощью атрибута использования](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="67a5e-338">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="67a5e-339">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="67a5e-339">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="67a5e-340">Дополнительные сведения о см `[ApiController]` . в разделе [атрибут ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="67a5e-340">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="67a5e-341">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="67a5e-341">Route name</span></span>

<span data-ttu-id="67a5e-342">В следующем коде определяется имя маршрута`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-342">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="67a5e-343">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-343">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="67a5e-344">Имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-344">Route names:</span></span>

* <span data-ttu-id="67a5e-345">Не влияют на поведение маршрутизации в соответствии с URL-адресом.</span><span class="sxs-lookup"><span data-stu-id="67a5e-345">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="67a5e-346">Используются только для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-346">Are only used for URL generation.</span></span>

<span data-ttu-id="67a5e-347">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-347">Route names must be unique application-wide.</span></span>

<span data-ttu-id="67a5e-348">Сравните предыдущий код с обычным маршрутом по умолчанию, который определяет `id` параметр как необязательный ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="67a5e-348">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="67a5e-349">Возможность точного указания интерфейсов API имеет свои преимущества, такие как разрешение `/products` и `/products/5` возможность отправки в различные действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-349">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="67a5e-350">Объединение маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-350">Combining attribute routes</span></span>

<span data-ttu-id="67a5e-351">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-351">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="67a5e-352">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-352">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="67a5e-353">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-353">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="67a5e-354">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-354">In the preceding example:</span></span>

* <span data-ttu-id="67a5e-355">Путь URL-адреса `/products` может совпадать`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="67a5e-355">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="67a5e-356">Путь URL-адреса `/products/5` может совпадать `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-356">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="67a5e-357">Оба эти действия соответствуют только HTTP `GET` , так как они помечены `[HttpGet]` атрибутом.</span><span class="sxs-lookup"><span data-stu-id="67a5e-357">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="67a5e-358">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="67a5e-358">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="67a5e-359">Следующий пример соответствует набору URL-путей, аналогичному маршруту по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-359">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="67a5e-360">В следующей таблице описаны `[Route]` атрибуты в приведенном выше коде.</span><span class="sxs-lookup"><span data-stu-id="67a5e-360">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="67a5e-361">attribute</span><span class="sxs-lookup"><span data-stu-id="67a5e-361">Attribute</span></span>               | <span data-ttu-id="67a5e-362">Объединяет с`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="67a5e-362">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="67a5e-363">Определение шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="67a5e-363">Defines route template</span></span> |
| ---
<span data-ttu-id="67a5e-364">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-364">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-365">'Blazor'</span></span>
- <span data-ttu-id="67a5e-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-366">'Identity'</span></span>
- <span data-ttu-id="67a5e-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-368">'Razor'</span></span>
- <span data-ttu-id="67a5e-369">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-370">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-370">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-371">'Blazor'</span></span>
- <span data-ttu-id="67a5e-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-372">'Identity'</span></span>
- <span data-ttu-id="67a5e-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-374">'Razor'</span></span>
- <span data-ttu-id="67a5e-375">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-376">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-376">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-377">'Blazor'</span></span>
- <span data-ttu-id="67a5e-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-378">'Identity'</span></span>
- <span data-ttu-id="67a5e-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-380">'Razor'</span></span>
- <span data-ttu-id="67a5e-381">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-382">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-382">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-383">'Blazor'</span></span>
- <span data-ttu-id="67a5e-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-384">'Identity'</span></span>
- <span data-ttu-id="67a5e-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-386">'Razor'</span></span>
- <span data-ttu-id="67a5e-387">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-388">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-388">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-389">'Blazor'</span></span>
- <span data-ttu-id="67a5e-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-390">'Identity'</span></span>
- <span data-ttu-id="67a5e-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-392">'Razor'</span></span>
- <span data-ttu-id="67a5e-393">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-394">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-394">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-395">'Blazor'</span></span>
- <span data-ttu-id="67a5e-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-396">'Identity'</span></span>
- <span data-ttu-id="67a5e-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-398">'Razor'</span></span>
- <span data-ttu-id="67a5e-399">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-399">'SignalR' uid:</span></span> 

<span data-ttu-id="67a5e-400">--------- | Заголовок---: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-400">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-401">'Blazor'</span></span>
- <span data-ttu-id="67a5e-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-402">'Identity'</span></span>
- <span data-ttu-id="67a5e-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-404">'Razor'</span></span>
- <span data-ttu-id="67a5e-405">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-406">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-406">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-407">'Blazor'</span></span>
- <span data-ttu-id="67a5e-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-408">'Identity'</span></span>
- <span data-ttu-id="67a5e-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-410">'Razor'</span></span>
- <span data-ttu-id="67a5e-411">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-412">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-412">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-413">'Blazor'</span></span>
- <span data-ttu-id="67a5e-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-414">'Identity'</span></span>
- <span data-ttu-id="67a5e-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-416">'Razor'</span></span>
- <span data-ttu-id="67a5e-417">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-418">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-418">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-419">'Blazor'</span></span>
- <span data-ttu-id="67a5e-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-420">'Identity'</span></span>
- <span data-ttu-id="67a5e-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-422">'Razor'</span></span>
- <span data-ttu-id="67a5e-423">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-423">'SignalR' uid:</span></span> 

<span data-ttu-id="67a5e-424">------ | Заголовок---: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-424">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-425">'Blazor'</span></span>
- <span data-ttu-id="67a5e-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-426">'Identity'</span></span>
- <span data-ttu-id="67a5e-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-428">'Razor'</span></span>
- <span data-ttu-id="67a5e-429">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="67a5e-430">Заголовок: Автор: описание: MS. author: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="67a5e-430">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="67a5e-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-431">'Blazor'</span></span>
- <span data-ttu-id="67a5e-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67a5e-432">'Identity'</span></span>
- <span data-ttu-id="67a5e-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67a5e-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="67a5e-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67a5e-434">'Razor'</span></span>
- <span data-ttu-id="67a5e-435">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="67a5e-435">'SignalR' uid:</span></span> 

<span data-ttu-id="67a5e-436">----- | | `[Route("")]` | Да | `"Home"` |
| `[Route("Index")]` | Да | `"Home/Index"` |
| `[Route("/")]` | **Нет** | `""` |
 | `[Route("About")]` | Да | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="67a5e-436">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="67a5e-437">Порядок маршрута атрибута</span><span class="sxs-lookup"><span data-stu-id="67a5e-437">Attribute route order</span></span>

<span data-ttu-id="67a5e-438">Маршрутизация создает дерево и сопоставляет все конечные точки одновременно:</span><span class="sxs-lookup"><span data-stu-id="67a5e-438">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="67a5e-439">Записи маршрутов ведут себя так, как если бы они были размещены в идеальном порядке.</span><span class="sxs-lookup"><span data-stu-id="67a5e-439">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="67a5e-440">Наиболее конкретные маршруты могут выполняться до более общих маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-440">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="67a5e-441">Например, маршрут атрибута, например, `blog/search/{topic}` более специфичен, чем маршрут атрибута, например `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-441">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="67a5e-442">`blog/search/{topic}`Маршрут имеет более высокий приоритет, чем по умолчанию, так как он является более конкретным.</span><span class="sxs-lookup"><span data-stu-id="67a5e-442">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="67a5e-443">При использовании [обычной маршрутизации](#cr)разработчик несет ответственность за размещение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="67a5e-443">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="67a5e-444">Маршруты атрибутов могут настраивать порядок с помощью <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Свойства.</span><span class="sxs-lookup"><span data-stu-id="67a5e-444">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="67a5e-445">Все указанные в платформе [атрибуты маршрутов](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) включают `Order` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-445">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="67a5e-446">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-446">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="67a5e-447">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-447">The default order is `0`.</span></span> <span data-ttu-id="67a5e-448">Настройка маршрута с помощью `Order = -1` запусков перед маршрутами, которые не задают порядок.</span><span class="sxs-lookup"><span data-stu-id="67a5e-448">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="67a5e-449">Настройка маршрута с помощью команды `Order = 1` выполняется после упорядочения маршрутов по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-449">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="67a5e-450">**Избегайте** в зависимости от `Order` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-450">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="67a5e-451">Если для правильной маршрутизации URL-адресу приложения требуются явные значения порядка, скорее всего, это приведет к путанице с клиентами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-451">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="67a5e-452">Как правило, маршрутизация атрибутов выбирает правильный маршрут с сопоставлением URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-452">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="67a5e-453">Если порядок по умолчанию, используемый для создания URL-адреса, не работает, использование имени маршрута в качестве переопределения обычно проще, чем применение `Order` Свойства.</span><span class="sxs-lookup"><span data-stu-id="67a5e-453">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="67a5e-454">Рассмотрим следующие два контроллера, которые определяют сопоставление маршрутов `/home` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-454">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="67a5e-455">`/home`При запросе с помощью приведенного выше кода создается исключение, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="67a5e-455">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="67a5e-456">Добавление `Order` к одному из атрибутов маршрута разрешает неоднозначность:</span><span class="sxs-lookup"><span data-stu-id="67a5e-456">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="67a5e-457">В приведенном выше коде `/home` запускает `HomeController.Index` конечную точку.</span><span class="sxs-lookup"><span data-stu-id="67a5e-457">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="67a5e-458">Для получения `MyDemoController.MyIndex` запроса `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-458">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="67a5e-459">**Примечание.**</span><span class="sxs-lookup"><span data-stu-id="67a5e-459">**Note**:</span></span>

* <span data-ttu-id="67a5e-460">Приведенный выше код представляет собой пример или низкую структуру маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-460">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="67a5e-461">Он использовался для иллюстрации `Order` Свойства.</span><span class="sxs-lookup"><span data-stu-id="67a5e-461">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="67a5e-462">`Order`Свойство разрешает неоднозначность, но этот шаблон не может быть сопоставлен.</span><span class="sxs-lookup"><span data-stu-id="67a5e-462">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="67a5e-463">Лучше удалить `[Route("Home")]` шаблон.</span><span class="sxs-lookup"><span data-stu-id="67a5e-463">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="67a5e-464">См. страницы соглашения о маршрутах [ Razor и приложениях. порядок](xref:razor-pages/razor-pages-conventions#route-order) маршрута для получения сведений о порядке маршрутов со Razor страницами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-464">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="67a5e-465">В некоторых случаях возвращается ошибка HTTP 500 с неоднозначными маршрутами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-465">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="67a5e-466">Используйте [ведение журнала](xref:fundamentals/logging/index) , чтобы узнать, какие конечные точки вызывают `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-466">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="67a5e-467">Замена токенов в шаблонах маршрутов [контроллер], [действие], [область]</span><span class="sxs-lookup"><span data-stu-id="67a5e-467">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="67a5e-468">Для удобства маршруты к атрибутам поддерживают замену маркера для зарезервированных параметров маршрута путем заключения маркера в один из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-468">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="67a5e-469">Квадратные скобки:`[]`</span><span class="sxs-lookup"><span data-stu-id="67a5e-469">Square brackets: `[]`</span></span>
* <span data-ttu-id="67a5e-470">Фигурные скобки:`{}`</span><span class="sxs-lookup"><span data-stu-id="67a5e-470">Curly braces: `{}`</span></span>

<span data-ttu-id="67a5e-471">Маркеры `[action]` , `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.</span><span class="sxs-lookup"><span data-stu-id="67a5e-471">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="67a5e-472">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="67a5e-472">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="67a5e-473">Отвечающ`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="67a5e-473">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="67a5e-474">Отвечающ`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="67a5e-474">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="67a5e-475">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-475">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="67a5e-476">Предыдущий пример ведет себя так же, как и следующий код:</span><span class="sxs-lookup"><span data-stu-id="67a5e-476">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="67a5e-477">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="67a5e-477">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="67a5e-478">Это мощное сочетание с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-478">This is powerful combined with token replacement.</span></span> <span data-ttu-id="67a5e-479">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-479">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="67a5e-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`создает уникальное имя маршрута для каждого действия:</span><span class="sxs-lookup"><span data-stu-id="67a5e-480">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="67a5e-481">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-481">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="67a5e-482"> создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-482">generates a unique route name for each action.</span></span>

<span data-ttu-id="67a5e-483">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-483">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="67a5e-484">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="67a5e-484">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="67a5e-485">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="67a5e-485">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="67a5e-486">Преобразователь параметров реализует <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="67a5e-486">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="67a5e-487">Например, `SlugifyParameterTransformer` преобразователь настраиваемых параметров изменяет `SubscriptionManagement` значение маршрута на `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-487">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="67a5e-488"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="67a5e-488">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="67a5e-489">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="67a5e-489">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="67a5e-490">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="67a5e-490">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="67a5e-491">Предыдущий `ListAll` метод соответствует `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-491">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="67a5e-492">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-492">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="67a5e-493">Определение служебной версии см. в [веб-документах MDN в служебной системе](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-493">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="67a5e-494">Несколько маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-494">Multiple attribute routes</span></span>

<span data-ttu-id="67a5e-495">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-495">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="67a5e-496">Наиболее распространенный случай использования этой возможности — имитация поведения маршрута по умолчанию на основе соглашения, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-496">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="67a5e-497">Размещение нескольких атрибутов маршрута на контроллере означает, что каждый из них объединяется с каждым атрибутом маршрута в методах действия:</span><span class="sxs-lookup"><span data-stu-id="67a5e-497">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="67a5e-498">Все ограничения маршрута [HTTP-команд](#verb) реализуют `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-498">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="67a5e-499">Когда в действие помещаются несколько атрибутов маршрута, реализующих, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-499">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="67a5e-500">Каждое ограничение действия объединяется с шаблоном маршрута, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="67a5e-500">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="67a5e-501">Использование нескольких маршрутов в действиях может показаться полезным и мощным, поэтому лучше обеспечить базовое и четкое определение пространства URL-адресов вашего приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-501">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="67a5e-502">Используйте несколько маршрутов **в действиях** , если это необходимо, например, для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-502">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="67a5e-503">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-503">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="67a5e-504">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-504">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="67a5e-505">В приведенном выше коде `[HttpPost("product/{id:int}")]` применяет ограничение маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-505">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="67a5e-506">`ProductsController.ShowProduct`Действие сопоставляется только с URL-путями, такими как `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-506">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="67a5e-507">Часть шаблона маршрута `{id:int}` ограничивает этот сегмент только целыми числами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-507">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="67a5e-508">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="67a5e-508">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="67a5e-509">Настраиваемые атрибуты маршрута с помощью Ираутетемплатепровидер</span><span class="sxs-lookup"><span data-stu-id="67a5e-509">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="67a5e-510">Все [атрибуты маршрута](#rt) реализуют <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="67a5e-510">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="67a5e-511">Среда выполнения ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="67a5e-511">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="67a5e-512">Ищет атрибуты классов контроллеров и методов действий при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-512">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="67a5e-513">Использует атрибуты, реализующие `IRouteTemplateProvider` для создания начального набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-513">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="67a5e-514">Реализуйте `IRouteTemplateProvider` , чтобы определить пользовательские атрибуты маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-514">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="67a5e-515">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="67a5e-515">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="67a5e-516">Предыдущий `Get` метод возвращает значение `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-516">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="67a5e-517">Использование модели приложения для настройки маршрутов к атрибутам</span><span class="sxs-lookup"><span data-stu-id="67a5e-517">Use application model to customize attribute routes</span></span>

<span data-ttu-id="67a5e-518">Модель приложения:</span><span class="sxs-lookup"><span data-stu-id="67a5e-518">The application model:</span></span>

* <span data-ttu-id="67a5e-519">Объектная модель, создаваемая при запуске.</span><span class="sxs-lookup"><span data-stu-id="67a5e-519">Is an object model created at startup.</span></span>
* <span data-ttu-id="67a5e-520">Содержит все метаданные, используемые ASP.NET Core для маршрутизации и выполнения действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="67a5e-520">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="67a5e-521">Модель приложения включает все данные, собранные из атрибутов маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-521">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="67a5e-522">Данные из атрибутов маршрута предоставляются `IRouteTemplateProvider` реализацией.</span><span class="sxs-lookup"><span data-stu-id="67a5e-522">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="67a5e-523">Именован</span><span class="sxs-lookup"><span data-stu-id="67a5e-523">Conventions:</span></span>

* <span data-ttu-id="67a5e-524">Можно написать, чтобы изменить модель приложения, чтобы настроить маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-524">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="67a5e-525">Считываются при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-525">Are read at app startup.</span></span>

<span data-ttu-id="67a5e-526">В этом разделе показан простой пример настройки маршрутизации с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-526">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="67a5e-527">Следующий код делает маршруты примерно в соответствии со структурой папок проекта.</span><span class="sxs-lookup"><span data-stu-id="67a5e-527">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="67a5e-528">Следующий код предотвращает `namespace` применение соглашения к контроллерам, которые передаются по атрибуту:</span><span class="sxs-lookup"><span data-stu-id="67a5e-528">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="67a5e-529">Например, следующий контроллер не использует `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-529">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="67a5e-530">Метод `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-530">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="67a5e-531">Не выполняет никаких действий, если контроллер является перенаправляемым атрибутом.</span><span class="sxs-lookup"><span data-stu-id="67a5e-531">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="67a5e-532">Задает шаблон контроллеров, основанный на `namespace` , с `namespace` удалением базы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-532">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="67a5e-533">`NamespaceRoutingConvention`Можно применять в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-533">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="67a5e-534">Например, рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="67a5e-534">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="67a5e-535">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="67a5e-535">In the preceding code:</span></span>

* <span data-ttu-id="67a5e-536">Основанием `namespace` является `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-536">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="67a5e-537">Полное имя предыдущего контроллера — `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-537">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="67a5e-538">`NamespaceRoutingConvention`Задает шаблон контроллеров `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-538">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="67a5e-539">`NamespaceRoutingConvention`Также можно применить в качестве атрибута в контроллере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-539">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="67a5e-540">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-540">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="67a5e-541">ASP.NET Core приложения могут сочетать использование обычной маршрутизации и маршрутизации атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-541">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="67a5e-542">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="67a5e-542">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="67a5e-543">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-543">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="67a5e-544">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-544">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="67a5e-545">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="67a5e-545">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="67a5e-546">**Любой** атрибут маршрута на контроллере делает **все** действия в атрибуте Controller перенаправлены.</span><span class="sxs-lookup"><span data-stu-id="67a5e-546">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="67a5e-547">Маршрутизация атрибутов и Обычная маршрутизация используют один и тот же механизм маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-547">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="67a5e-548">Создание URL-адресов и значения окружения</span><span class="sxs-lookup"><span data-stu-id="67a5e-548">URL Generation and ambient values</span></span>

<span data-ttu-id="67a5e-549">Приложения могут использовать функции создания URL-адресов маршрутизации для создания URL-ссылок на действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-549">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="67a5e-550">Создание URL-адресов устраняет прописано URL-адреса, делая код более надежным и сопровождаемым.</span><span class="sxs-lookup"><span data-stu-id="67a5e-550">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="67a5e-551">В этом разделе рассматриваются функции создания URL-адресов, предоставляемые MVC, и описываются только основные принципы работы формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-551">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="67a5e-552">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="67a5e-552">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="67a5e-553"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>Интерфейс является базовым элементом инфраструктуры между MVC и маршрутизацией для создания URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-553">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="67a5e-554">Экземпляр `IUrlHelper` доступен через `Url` свойство в контроллерах, представлениях и компонентах представления.</span><span class="sxs-lookup"><span data-stu-id="67a5e-554">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="67a5e-555">В следующем примере `IUrlHelper` интерфейс используется с помощью `Controller.Url` свойства для создания URL-адреса для другого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-555">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="67a5e-556">Если приложение использует стандартный маршрут по умолчанию, значением `url` переменной является строка URL-пути `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-556">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="67a5e-557">Этот путь URL-адреса создается при маршрутизации путем объединения:</span><span class="sxs-lookup"><span data-stu-id="67a5e-557">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="67a5e-558">Значения маршрута из текущего запроса, которые называются **внешними значениями**.</span><span class="sxs-lookup"><span data-stu-id="67a5e-558">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="67a5e-559">Значения, передаваемые в `Url.Action` шаблон маршрута и подставляемые этими значениями:</span><span class="sxs-lookup"><span data-stu-id="67a5e-559">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="67a5e-560">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-560">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="67a5e-561">Параметр маршрута, который не имеет значения, может:</span><span class="sxs-lookup"><span data-stu-id="67a5e-561">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="67a5e-562">Используйте значение по умолчанию, если таковое имеется.</span><span class="sxs-lookup"><span data-stu-id="67a5e-562">Use a default value if it has one.</span></span>
* <span data-ttu-id="67a5e-563">Пропускается, если он необязателен.</span><span class="sxs-lookup"><span data-stu-id="67a5e-563">Be skipped if it's optional.</span></span> <span data-ttu-id="67a5e-564">Например, `id` из шаблона маршрута `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-564">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="67a5e-565">Создание URL-адреса завершается ошибкой, если ни один из обязательных параметров маршрута не имеет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-565">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="67a5e-566">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="67a5e-566">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="67a5e-567">В предыдущем примере `Url.Action` предполагается [Обычная маршрутизация](#cr).</span><span class="sxs-lookup"><span data-stu-id="67a5e-567">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="67a5e-568">Создание URL-адреса работает аналогично [маршрутизации атрибутов](#ar), хотя понятия различны.</span><span class="sxs-lookup"><span data-stu-id="67a5e-568">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="67a5e-569">С обычной маршрутизацией:</span><span class="sxs-lookup"><span data-stu-id="67a5e-569">With conventional routing:</span></span>

* <span data-ttu-id="67a5e-570">Значения маршрута используются для расширения шаблона.</span><span class="sxs-lookup"><span data-stu-id="67a5e-570">The route values are used to expand a template.</span></span>
* <span data-ttu-id="67a5e-571">Значения маршрута для `controller` и `action` обычно отображаются в этом шаблоне.</span><span class="sxs-lookup"><span data-stu-id="67a5e-571">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="67a5e-572">Это работает потому, что URL-адреса, соответствующие маршрутизации, соответствуют соглашению.</span><span class="sxs-lookup"><span data-stu-id="67a5e-572">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="67a5e-573">В следующем примере используется маршрутизация атрибутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-573">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="67a5e-574">`Source`Действие в предыдущем коде создает `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-574">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="67a5e-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>был добавлен в ASP.NET Core 3,0 в качестве альтернативы `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-575"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="67a5e-576">`LinkGenerator`предлагает аналогичные, но более гибкие функции.</span><span class="sxs-lookup"><span data-stu-id="67a5e-576">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="67a5e-577">Каждый метод в `IUrlHelper` имеет также соответствующее семейство методов `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-577">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="67a5e-578">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="67a5e-578">Generating URLs by action name</span></span>

<span data-ttu-id="67a5e-579">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [линкженератор. жетпасбяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)и все связанные перегрузки предназначены для создания целевой конечной точки путем указания имени контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-579">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="67a5e-580">При использовании `Url.Action` значения текущего маршрута для `controller` и `action` предоставляются средой выполнения:</span><span class="sxs-lookup"><span data-stu-id="67a5e-580">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="67a5e-581">Значение `controller` и `action` являются частью как [значений окружающих](#ambient) элементов, так и значений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-581">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="67a5e-582">Метод `Url.Action` всегда использует текущие значения `action` и `controller` и создает URL-путь, который направляет в текущее действие.</span><span class="sxs-lookup"><span data-stu-id="67a5e-582">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="67a5e-583">Маршрутизация пытается использовать значения во внешних значениях для заполнения сведений, которые не были предоставлены при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-583">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="67a5e-584">Рассмотрим маршрут, аналогичный `{a}/{b}/{c}/{d}` значениям окружения `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-584">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="67a5e-585">Маршрутизация содержит достаточно информации для создания URL-адреса без дополнительных значений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-585">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="67a5e-586">Маршрутизация содержит достаточно информации, так как все параметры маршрута имеют значение.</span><span class="sxs-lookup"><span data-stu-id="67a5e-586">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="67a5e-587">Если значение `{ d = Donovan }` Добавлено:</span><span class="sxs-lookup"><span data-stu-id="67a5e-587">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="67a5e-588">Значение `{ d = David }` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="67a5e-588">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="67a5e-589">Путь к созданному URL-адресу — `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-589">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="67a5e-590">**Предупреждение**: URL-пути являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="67a5e-590">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="67a5e-591">В предыдущем примере, если `{ c = Cheryl }` добавляется значение:</span><span class="sxs-lookup"><span data-stu-id="67a5e-591">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="67a5e-592">Оба значения `{ c = Carol, d = David }` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="67a5e-592">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="67a5e-593">Больше нет значения для `d` , и создание URL-адреса завершается неудачей.</span><span class="sxs-lookup"><span data-stu-id="67a5e-593">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="67a5e-594">Необходимо указать требуемые значения `c` и `d` для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-594">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="67a5e-595">Возможно, вы намерены столкнуться с этой проблемой с маршрутом по умолчанию `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-595">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="67a5e-596">Эта проблема возникает редко, поскольку `Url.Action` всегда явно указывает `controller` `action` значение и.</span><span class="sxs-lookup"><span data-stu-id="67a5e-596">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="67a5e-597">Несколько перегрузок [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) принимают объект значений маршрута, чтобы предоставить значения для параметров маршрута, отличных `controller` от `action` и.</span><span class="sxs-lookup"><span data-stu-id="67a5e-597">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="67a5e-598">Объект значений маршрута часто используется с `id` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-598">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="67a5e-599">Например, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-599">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="67a5e-600">Объект значений маршрута:</span><span class="sxs-lookup"><span data-stu-id="67a5e-600">The route values object:</span></span>

* <span data-ttu-id="67a5e-601">По соглашению обычно является объектом анонимного типа.</span><span class="sxs-lookup"><span data-stu-id="67a5e-601">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="67a5e-602">Может быть `IDictionary<>` или [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="67a5e-602">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="67a5e-603">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-603">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="67a5e-604">Приведенный выше код создает `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-604">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="67a5e-605">Следующий код создает абсолютный URL-адрес:</span><span class="sxs-lookup"><span data-stu-id="67a5e-605">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="67a5e-606">Чтобы создать абсолютный URL-адрес, используйте один из следующих способов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-606">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="67a5e-607">Перегрузка, принимающая `protocol` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-607">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="67a5e-608">Например, приведенный выше код.</span><span class="sxs-lookup"><span data-stu-id="67a5e-608">For example, the preceding code.</span></span>
* <span data-ttu-id="67a5e-609">[Линкженератор. жетурибяктион](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), который по умолчанию создает абсолютные URI.</span><span class="sxs-lookup"><span data-stu-id="67a5e-609">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="67a5e-610">Создание URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="67a5e-610">Generate URLs by route</span></span>

<span data-ttu-id="67a5e-611">Приведенный выше код демонстрирует создание URL-адреса путем передачи контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-611">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="67a5e-612">`IUrlHelper`также предоставляет семейство методов [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-612">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="67a5e-613">Эти методы похожи на [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), но не копируют текущие значения `action` и `controller` в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-613">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="67a5e-614">Наиболее распространенное использование `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-614">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="67a5e-615">Указывает имя маршрута для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-615">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="67a5e-616">Обычно не указывает имя контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-616">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="67a5e-617">Следующий Razor файл создает ссылку HTML на `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-617">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="67a5e-618">Создание URL-адресов в HTML иRazor</span><span class="sxs-lookup"><span data-stu-id="67a5e-618">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="67a5e-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>предоставляет <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> методы [HTML. Бегинформ](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) и [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) для создания `<form>` элементов и `<a>` соответственно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-619"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="67a5e-620">Эти методы используют метод [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) для создания URL-адреса и принимают аналогичные аргументы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-620">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="67a5e-621">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="67a5e-621">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="67a5e-622">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-622">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="67a5e-623">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-623">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="67a5e-624">Дополнительные сведения см. [в разделе вспомогательные функции тегов в формах](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-624">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="67a5e-625">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="67a5e-625">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="67a5e-626">Создание URL-адресов в результатах действий</span><span class="sxs-lookup"><span data-stu-id="67a5e-626">URL generation in Action Results</span></span>

<span data-ttu-id="67a5e-627">В предыдущих примерах было показано использование `IUrlHelper` в контроллере.</span><span class="sxs-lookup"><span data-stu-id="67a5e-627">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="67a5e-628">Наиболее распространенным использованием контроллера является создание URL-адреса как части результата действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-628">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="67a5e-629">Базовые классы <xref:Microsoft.AspNetCore.Mvc.ControllerBase> и <xref:Microsoft.AspNetCore.Mvc.Controller> предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="67a5e-629">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="67a5e-630">Один из типичных способов использования — перенаправление после приема входных данных пользователя:</span><span class="sxs-lookup"><span data-stu-id="67a5e-630">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="67a5e-631">Действия, выполняемые фабриками, такие как <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> и, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> следуют аналогичному шаблону для методов в `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-631">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="67a5e-632">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-632">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="67a5e-633">В [обычной маршрутизации](#cr) может использоваться особый тип определения маршрута, называемый [выделенным обычным маршрутом](#dcr).</span><span class="sxs-lookup"><span data-stu-id="67a5e-633">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="67a5e-634">В следующем примере маршрут с именем `blog` является выделенным обычным маршрутом:</span><span class="sxs-lookup"><span data-stu-id="67a5e-634">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="67a5e-635">Используя приведенные выше определения маршрутов, `Url.Action("Index", "Home")` создает URL-путь `/` с помощью `default` маршрута, но почему?</span><span class="sxs-lookup"><span data-stu-id="67a5e-635">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="67a5e-636">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-636">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="67a5e-637">[Выделенные традиционные маршруты](#dcr) полагаются на особое поведение значений по умолчанию, которые не имеют соответствующего параметра маршрута, который предотвращает слишком [жадную](xref:fundamentals/routing#greedy) маршрутизацию при формировании URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-637">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="67a5e-638">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="67a5e-638">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="67a5e-639">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-639">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="67a5e-640">Создание URL-адреса с помощью `blog` завершается ошибкой, так как значения `{ controller = Home, action = Index }` не совпадают `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-640">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="67a5e-641">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-641">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="67a5e-642">Области</span><span class="sxs-lookup"><span data-stu-id="67a5e-642">Areas</span></span>

<span data-ttu-id="67a5e-643">[Области](xref:mvc/controllers/areas) — это функция MVC, используемая для упорядочивания связанных функций в группе в виде отдельных:</span><span class="sxs-lookup"><span data-stu-id="67a5e-643">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="67a5e-644">Пространство имен маршрутизации для действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="67a5e-644">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="67a5e-645">Структура папок для представлений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-645">Folder structure for views.</span></span>

<span data-ttu-id="67a5e-646">Использование областей позволяет приложению иметь несколько контроллеров с одинаковым именем, если они имеют разные области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-646">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="67a5e-647">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-647">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="67a5e-648">В этом разделе обсуждается взаимодействие маршрутизации с областями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-648">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="67a5e-649">Сведения об использовании областей с представлениями см. в разделе [области](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="67a5e-649">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="67a5e-650">В следующем примере MVC настраивается для использования стандартного маршрута по умолчанию и `area` маршрута для `area` именованного `Blog` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-650">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="67a5e-651">В приведенном выше коде <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> вызывается для создания `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-651">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="67a5e-652">Вторым параметром `"Blog"` является имя области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-652">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="67a5e-653">При сопоставлении URL-пути `/Manage/Users/AddUser` , например, `"blog_route"` маршрут формирует значения маршрута `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-653">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="67a5e-654">`area`Значение маршрута создается значением по умолчанию для `area` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-654">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="67a5e-655">Маршрут, созданный с помощью, `MapAreaControllerRoute` эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="67a5e-655">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="67a5e-656">Метод `MapAreaControllerRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-656">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="67a5e-657">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-657">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="67a5e-658">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="67a5e-658">Conventional routing is order-dependent.</span></span> <span data-ttu-id="67a5e-659">Как правило, маршруты с областями следует размещать раньше, так как они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-659">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="67a5e-660">Используя предыдущий пример, значения маршрута `{ area = Blog, controller = Users, action = AddUser }` соответствуют следующему действию:</span><span class="sxs-lookup"><span data-stu-id="67a5e-660">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="67a5e-661">Атрибут [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) указывает, что контроллер является частью области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-661">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="67a5e-662">Этот контроллер находится в `Blog` области.</span><span class="sxs-lookup"><span data-stu-id="67a5e-662">This controller is in the `Blog` area.</span></span> <span data-ttu-id="67a5e-663">Контроллеры без `[Area]` атрибута не являются членами какой-либо области и не **not** совпадают, если `area` значение маршрута предоставляется службой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-663">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="67a5e-664">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-664">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="67a5e-665">Пространство имен каждого контроллера показано здесь для полноты.</span><span class="sxs-lookup"><span data-stu-id="67a5e-665">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="67a5e-666">Если предыдущие контроллеры используют одно и то же пространство имен, создается ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="67a5e-666">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="67a5e-667">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="67a5e-667">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="67a5e-668">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-668">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="67a5e-669">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-669">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="67a5e-670">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="67a5e-670">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="67a5e-671">При выполнении действия внутри области значение маршрута для `area` будет доступно как [значение окружения](#ambient) для маршрутизации, используемой для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-671">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="67a5e-672">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="67a5e-672">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="67a5e-673">Следующий код создает URL-адрес для `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="67a5e-673">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="67a5e-674">Определение действия</span><span class="sxs-lookup"><span data-stu-id="67a5e-674">Action definition</span></span>

<span data-ttu-id="67a5e-675">Открытые методы на контроллере, за исключением тех, которые имеют атрибут [недействия](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , являются действиями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-675">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="67a5e-676">Образец кода</span><span class="sxs-lookup"><span data-stu-id="67a5e-676">Sample code</span></span>

 * <span data-ttu-id="67a5e-677">Метод [мидисплайраутеинфо](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) включен в [Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) и используется для вывода сведений о маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-677">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="67a5e-678">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67a5e-678">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="67a5e-679">В ASP.NET Core MVC используется [ПО промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов с действиями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-679">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="67a5e-680">Маршруты определяются в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="67a5e-680">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="67a5e-681">Они описывают то, как пути URL-адресов должны сопоставляться с действиями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-681">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="67a5e-682">С помощью маршрутов также формируются URL-адреса (для ссылок), отправляемые в ответах.</span><span class="sxs-lookup"><span data-stu-id="67a5e-682">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="67a5e-683">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-683">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="67a5e-684">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-684">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="67a5e-685">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="67a5e-685">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="67a5e-686">В этом документе описывается взаимодействие между MVC и маршрутизацией и использование возможностей маршрутизации в типичных приложениях MVC.</span><span class="sxs-lookup"><span data-stu-id="67a5e-686">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="67a5e-687">Подробные сведения о расширенной маршрутизации см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="67a5e-687">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="67a5e-688">Настройка ПО промежуточного слоя маршрутизации</span><span class="sxs-lookup"><span data-stu-id="67a5e-688">Setting up Routing Middleware</span></span>

<span data-ttu-id="67a5e-689">Метод *Configure* содержит код, который выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="67a5e-689">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="67a5e-690">В вызове `UseMvc` метод `MapRoute` используется для создания одного маршрута, который называется маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-690">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="67a5e-691">В большинстве приложений MVC используется маршрут с шаблоном, сходным с маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-691">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="67a5e-692">Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` может сопоставлять путь URL-адреса, например `/Products/Details/5`, и извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем разбивки пути на лексемы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-692">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="67a5e-693">MVC попытается найти контроллер с именем `ProductsController` и выполнить действие `Details`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-693">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="67a5e-694">Обратите внимание на то, что в этом примере привязка модели будет использовать значение `id = 5`, чтобы присвоить параметру `id` значение `5` при вызове действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-694">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="67a5e-695">Дополнительные сведения см. в разделе [Привязка модели](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="67a5e-695">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="67a5e-696">Использование маршрута `default`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-696">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="67a5e-697">Шаблон маршрута:</span><span class="sxs-lookup"><span data-stu-id="67a5e-697">The route template:</span></span>

* <span data-ttu-id="67a5e-698">`{controller=Home}` определяет `Home` в качестве объекта `controller` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-698">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="67a5e-699">`{action=Index}` определяет `Index` в качестве объекта `action` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-699">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="67a5e-700">`{id?}` определяет необязательный параметр `id`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-700">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="67a5e-701">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="67a5e-701">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="67a5e-702">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="67a5e-702">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="67a5e-703">`"{controller=Home}/{action=Index}/{id?}"` может сопоставляться с путем URL-адреса `/` и выдает значения маршрута `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-703">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="67a5e-704">Для объектов `controller` и `action` используются значения по умолчанию. `id` не имеет значения, так как в пути URL-адреса нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="67a5e-704">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="67a5e-705">MVC будет использовать эти значения маршрута для выбора действия `HomeController` и `Index`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-705">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="67a5e-706">При использовании этого определения контроллера и шаблона маршрута действие `HomeController.Index` будет выполняться для любых из следующих путей URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-706">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="67a5e-707">Универсальный метод `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-707">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="67a5e-708">Можно использовать вместо следующего метода:</span><span class="sxs-lookup"><span data-stu-id="67a5e-708">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="67a5e-709">`UseMvc` и `UseMvcWithDefaultRoute` добавляют экземпляр `RouterMiddleware` в конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="67a5e-709">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="67a5e-710">MVC не взаимодействует с ПО промежуточного слоя напрямую, а использует маршрутизацию для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-710">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="67a5e-711">MVC подключается к маршрутам посредством экземпляра `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-711">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="67a5e-712">Код метода `UseMvc` имеет примерно следующий вид:</span><span class="sxs-lookup"><span data-stu-id="67a5e-712">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="67a5e-713">Метод `UseMvc` не определяет маршруты напрямую, а добавляет заполнитель в коллекцию маршрутов для маршрута `attribute`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-713">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="67a5e-714">Перегрузка `UseMvc(Action<IRouteBuilder>)` позволяет добавлять собственные маршруты, а также поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-714">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="67a5e-715">Метод `UseMvc` и все его варианты добавляют заполнитель для маршрута на основе атрибутов. Маршрутизация с помощью атрибутов доступна всегда вне зависимости от того, как настроен метод `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-715">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="67a5e-716">Метод `UseMvcWithDefaultRoute` определяет маршрут по умолчанию и поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-716">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="67a5e-717">В разделе [Маршрутизация с помощью атрибутов](#attribute-routing-ref-label) приводятся более подробные сведения о маршрутизации с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-717">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="67a5e-718">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-718">Conventional routing</span></span>

<span data-ttu-id="67a5e-719">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-719">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="67a5e-720">Приведенный выше код является примером обычной маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-720">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="67a5e-721">Этот стиль называется обычной маршрутизацией, так как он устанавливает *соглашение* для URL-путей:</span><span class="sxs-lookup"><span data-stu-id="67a5e-721">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="67a5e-722">Первый сегмент пути сопоставляется с именем контроллера.</span><span class="sxs-lookup"><span data-stu-id="67a5e-722">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="67a5e-723">Второй сопоставляется с именем действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-723">The second maps to the action name.</span></span>
* <span data-ttu-id="67a5e-724">Третий сегмент используется для необязательного `id` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-724">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="67a5e-725">`id`сопоставляется с сущностью модели.</span><span class="sxs-lookup"><span data-stu-id="67a5e-725">`id` maps to a model entity.</span></span>

<span data-ttu-id="67a5e-726">При использовании этого маршрута `default` путь URL-адреса `/Products/List` сопоставляется с действием `ProductsController.List`, а путь `/Blog/Article/17` сопоставляется с `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-726">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="67a5e-727">Такое сопоставление основывается **только** на именах контроллера и действия, но не на пространствах имен, расположениях исходных файлов или параметрах метода.</span><span class="sxs-lookup"><span data-stu-id="67a5e-727">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="67a5e-728">Использование маршрутизации на основе соглашений с маршрутом по умолчанию позволяет быстро создавать приложение, не придумывая новый шаблон URL-адреса для каждого определяемого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-728">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="67a5e-729">В случае с приложением с действиями в стиле CRUD единообразие URL-адресов для всех контроллеров позволяет упростить код и сделать пользовательский интерфейс более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="67a5e-729">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="67a5e-730">Параметр `id` определяется в шаблоне маршрута как необязательный. Это означает, что действия могут выполняться, даже если идентификатор не указан в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="67a5e-730">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="67a5e-731">Как правило, если параметр `id` отсутствует в URL-адресе, привязка модели присваивает ему значение `0`, и в результате в базе данных не будет найдена сущность, соответствующая `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-731">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="67a5e-732">Маршрутизация с помощью атрибутов обеспечивает детальный контроль, позволяя настраивать идентификатор как обязательный лишь для некоторых действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-732">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="67a5e-733">В документации необязательные параметры, такие как `id`, будут включаться, только если они, скорее всего, могут использоваться в соответствующей ситуации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-733">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="67a5e-734">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-734">Multiple routes</span></span>

<span data-ttu-id="67a5e-735">В метод `UseMvc` можно добавить несколько маршрутов, добавив дополнительные вызовы `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-735">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="67a5e-736">Таким образом можно определить несколько соглашений или добавить маршруты на основе соглашений, предназначенные для определенного действия, например:</span><span class="sxs-lookup"><span data-stu-id="67a5e-736">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="67a5e-737">Маршрут `blog` здесь — это *выделенный маршрут на основе соглашения*. Это означает, что он использует систему маршрутизации на основе соглашений, но предназначен для определенного действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-737">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="67a5e-738">Так как параметры `controller` и `action` отсутствуют в шаблоне маршрута, они могут иметь только значения по умолчанию, поэтому этот маршрут всегда будет сопоставляться с действием `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-738">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="67a5e-739">Маршруты в коллекции маршрутов упорядочены и обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="67a5e-739">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="67a5e-740">Поэтому в этом примере маршрут `blog` будет проверяться перед маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-740">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-741">*Выделенные традиционные маршруты* часто используют **перекрестные** параметры маршрута `{*article}` , например для записи оставшейся части пути URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-741">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="67a5e-742">Из-за этого маршрут может оказаться слишком универсальным, то есть он будет соответствовать URL-адресам, которые должны сопоставляться с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="67a5e-742">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="67a5e-743">Чтобы избежать этой проблемы, такие универсальные маршруты следует помещать в конце таблицы маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-743">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="67a5e-744">Резервирование</span><span class="sxs-lookup"><span data-stu-id="67a5e-744">Fallback</span></span>

<span data-ttu-id="67a5e-745">В ходе обработки запроса MVC проверяет, можно ли найти контроллер и действие в приложении с помощью предоставленных значений маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-745">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="67a5e-746">Если нет действия, соответствующего значениям, маршрут считается не сопоставленным, и проверяется следующий маршрут.</span><span class="sxs-lookup"><span data-stu-id="67a5e-746">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="67a5e-747">Этот процесс называется *откатом* и призван упростить обработку случаев, когда маршруты на основе соглашений перекрываются.</span><span class="sxs-lookup"><span data-stu-id="67a5e-747">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="67a5e-748">Разрешение неоднозначности действий</span><span class="sxs-lookup"><span data-stu-id="67a5e-748">Disambiguating actions</span></span>

<span data-ttu-id="67a5e-749">Если при маршрутизации найдены два соответствующих действия, платформа MVC должна устранить неоднозначность, выбрав наиболее подходящее из них, или создать исключение.</span><span class="sxs-lookup"><span data-stu-id="67a5e-749">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="67a5e-750">Пример:</span><span class="sxs-lookup"><span data-stu-id="67a5e-750">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="67a5e-751">Этот контроллер определяет два действия, которые соответствуют пути URL-адреса `/Products/Edit/17` и маршрутизируют данные `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-751">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="67a5e-752">Это типичный шаблон для контроллеров MVC, в котором метод `Edit(int)` отображает форму для изменения сведений о продукте, а метод `Edit(int, Product)` обрабатывает отправленную форму.</span><span class="sxs-lookup"><span data-stu-id="67a5e-752">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="67a5e-753">Чтобы это было возможным, платформа MVC должна выбрать `Edit(int, Product)` для HTTP-запроса `POST` и `Edit(int)` для любой другой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="67a5e-753">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="67a5e-754">`HttpPostAttribute` (`[HttpPost]`) — это реализация интерфейса `IActionConstraint`, которая позволяет выбрать действие, только если HTTP-команда — `POST`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-754">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="67a5e-755">Наличие интерфейса `IActionConstraint` делает метод `Edit(int, Product)` более подходящим вариантом, чем `Edit(int)`, поэтому `Edit(int, Product)` будет проверяться первым.</span><span class="sxs-lookup"><span data-stu-id="67a5e-755">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="67a5e-756">Пользовательские реализации `IActionConstraint` потребуется создавать только в особых ситуациях, однако важно понимать роль таких атрибутов, как `HttpPostAttribute`, — аналогичные атрибуты определены для других HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="67a5e-756">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="67a5e-757">При маршрутизации на основе соглашений для действий часто используются одинаковые имена в рамках рабочего процесса `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-757">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="67a5e-758">Удобство такого шаблона станет очевидным после ознакомления с разделом [Сведения об интерфейсе IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="67a5e-758">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="67a5e-759">Если найдено несколько совпадений и MVC не может определить наиболее подходящий маршрут, создается исключение `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-759">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="67a5e-760">Имена маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-760">Route names</span></span>

<span data-ttu-id="67a5e-761">Строки `"blog"` и `"default"` в следующих примерах представляют собой имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-761">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="67a5e-762">Имя маршрута — это логическое имя, которое позволяет использовать именованный маршрут для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-762">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="67a5e-763">Имена значительно упрощают создание URL-адресов, если оно представляет сложность из-за порядка маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-763">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="67a5e-764">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-764">Route names must be unique application-wide.</span></span>

<span data-ttu-id="67a5e-765">Имена маршрутов не влияют на сопоставление URL-адресов или обработку запросов; они служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-765">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="67a5e-766">В статье [Маршрутизация](xref:fundamentals/routing) приводятся более подробные сведения о формировании URL-адресов, в том числе во вспомогательных объектах MVC.</span><span class="sxs-lookup"><span data-stu-id="67a5e-766">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="67a5e-767">Маршрутизация с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-767">Attribute routing</span></span>

<span data-ttu-id="67a5e-768">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-768">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="67a5e-769">В приведенном ниже примере в методе `Configure` используется `app.UseMvc();`, и маршрут не передается.</span><span class="sxs-lookup"><span data-stu-id="67a5e-769">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="67a5e-770">`HomeController` будет соответствовать набору URL-адресов, аналогичных тем, которым соответствует маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-770">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="67a5e-771">Действие `HomeController.Index()` будет выполняться для любого из путей URL-адресов `/`, `/Home` или `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-771">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-772">В этом примере показано ключевое различие между маршрутизацией с помощью атрибутов и маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-772">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="67a5e-773">При использовании маршрутизации с помощью атрибутов для указания маршрута требуется больше входных данных; маршрут по умолчанию на основе соглашения более лаконичен.</span><span class="sxs-lookup"><span data-stu-id="67a5e-773">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="67a5e-774">Однако маршрутизация с помощью атрибутов обеспечивает более точный контроль над тем, какие шаблоны маршрутов применяются к каждому действию, и требует такого контроля.</span><span class="sxs-lookup"><span data-stu-id="67a5e-774">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="67a5e-775">В случае с маршрутизацией с помощью атрибутов имя контроллера и имена действий **не** имеют значения при выборе действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-775">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="67a5e-776">В этом примере сопоставление будет производиться с теми же URL-адресами, что и в предыдущем.</span><span class="sxs-lookup"><span data-stu-id="67a5e-776">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="67a5e-777">Приведенные выше шаблоны маршрутов не определяют параметры маршрутов для `action`, `area` и `controller`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-777">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="67a5e-778">По сути, эти параметры маршрутов не разрешены в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-778">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="67a5e-779">Так как шаблон маршрута уже связан с действием, не имеет смысла анализировать имя действия в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="67a5e-779">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="67a5e-780">Маршрутизация с помощью атрибутов Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="67a5e-780">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="67a5e-781">При маршрутизации с помощью атрибутов также могут использоваться атрибуты `Http[Verb]`, такие как `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-781">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="67a5e-782">Все эти атрибуты могут принимать шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-782">All of these attributes can accept a route template.</span></span> <span data-ttu-id="67a5e-783">В этом примере показаны два действия, которые совпадают с одним шаблоном маршрута:</span><span class="sxs-lookup"><span data-stu-id="67a5e-783">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="67a5e-784">Для такого пути URL-адреса, как `/products`, действие `ProductsApi.ListProducts` будет выполнено, если HTTP-командой является `GET`, а действие `ProductsApi.CreateProduct` будет выполнено, если HTTP-командой является `POST`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-784">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="67a5e-785">При маршрутизации с помощью атрибутов URL-адрес сначала сопоставляется с набором шаблоном маршрутов, определяемых атрибутами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-785">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="67a5e-786">После того как найден соответствующий шаблон маршрута, применяются ограничения `IActionConstraint` для определения действий, которые могут быть выполнены.</span><span class="sxs-lookup"><span data-stu-id="67a5e-786">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="67a5e-787">При разработке REST API редко приходится использовать `[Route(...)]` для метода действия, так как действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="67a5e-787">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="67a5e-788">Предпочтительнее применять более конкретные атрибуты `Http*Verb*Attributes`, чтобы точно определить поддерживаемые API возможности.</span><span class="sxs-lookup"><span data-stu-id="67a5e-788">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="67a5e-789">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="67a5e-789">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="67a5e-790">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-790">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="67a5e-791">В этом примере параметр `id` является обязательным в пути URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-791">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="67a5e-792">Действие `ProductsApi.GetProduct(int)` будет выполнено для такого пути URL-адреса, как `/products/3`, но не для такого пути URL-адреса, как `/products`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-792">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="67a5e-793">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="67a5e-793">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="67a5e-794">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="67a5e-794">Route Name</span></span>

<span data-ttu-id="67a5e-795">В следующем коде определяется *имя маршрута*`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-795">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="67a5e-796">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-796">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="67a5e-797">Они не влияют на то, как производится сопоставление URL-адресов при маршрутизации, и служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-797">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="67a5e-798">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-798">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-799">Сравните это поведение с *маршрутом по умолчанию* на основе соглашения, в котором параметр `id` определяется как необязательный (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-799">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="67a5e-800">Такая возможность точно указывать интерфейсы API имеет преимущества. Например, она позволяет направлять `/products` и `/products/5` в разные действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-800">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="67a5e-801">Объединение маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-801">Combining routes</span></span>

<span data-ttu-id="67a5e-802">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-802">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="67a5e-803">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-803">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="67a5e-804">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-804">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="67a5e-805">В этом примере путь URL-адреса `/products` может соответствовать `ProductsApi.ListProducts`, а путь URL-адреса `/products/5` — `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-805">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="67a5e-806">Оба эти действия соответствуют только HTTP-запросу `GET`, так как они помечены атрибутом `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-806">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="67a5e-807">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="67a5e-807">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="67a5e-808">Этот пример соответствует такому же набору путей URL-адресов, что и *маршрут по умолчанию*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-808">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="67a5e-809">Упорядочение маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-809">Ordering attribute routes</span></span>

<span data-ttu-id="67a5e-810">В отличие от обычных маршрутов, которые выполняются в определенном порядке, маршрутизация атрибутов создает дерево и сопоставляет все маршруты одновременно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-810">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="67a5e-811">Это равносильно тому, как если бы записи маршрутов находились в идеальном порядке: наиболее конкретные маршруты имеют возможность выполнения перед более общими.</span><span class="sxs-lookup"><span data-stu-id="67a5e-811">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="67a5e-812">Например, такой маршрут, как `blog/search/{topic}`, является более конкретным по сравнению с `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-812">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="67a5e-813">С логической точки зрения, маршрут `blog/search/{topic}` по умолчанию выполняется первым, так как это единственная разумная очередность.</span><span class="sxs-lookup"><span data-stu-id="67a5e-813">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="67a5e-814">При использовании маршрутизации на основе соглашений разработчик отвечает за расположение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="67a5e-814">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="67a5e-815">При маршрутизации с помощью атрибутов порядок может настраиваться с помощью свойства `Order` всех атрибутов маршрутов, предоставляемых платформой.</span><span class="sxs-lookup"><span data-stu-id="67a5e-815">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="67a5e-816">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-816">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="67a5e-817">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-817">The default order is `0`.</span></span> <span data-ttu-id="67a5e-818">Маршрут, для которого задано значение `Order = -1`, будет выполняться перед маршрутами, для которых порядок не задан.</span><span class="sxs-lookup"><span data-stu-id="67a5e-818">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="67a5e-819">Маршрут, для которого задано значение `Order = 1`, будет выполняться после маршрутов с порядком по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-819">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="67a5e-820">Старайтесь не использовать свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-820">Avoid depending on `Order`.</span></span> <span data-ttu-id="67a5e-821">Если для правильной маршрутизации в пространстве URL-адресов требуются явно заданные значения порядка, скорее всего, это будет вызывать путаницу и в среде клиентов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-821">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="67a5e-822">Как правило, при маршрутизации с помощью атрибутов правильный маршрут выбирается посредством сопоставления URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-822">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="67a5e-823">Если порядок по умолчанию для формирования URL-адресов не работает, использовать имя маршрута в качестве переопределения, как правило, проще, чем применять свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-823">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="67a5e-824">Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-824">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="67a5e-825">Сведения о порядке маршрутов в Razor разделах страницы см. в разделе [ Razor Маршрутизация и соглашения о приложениях: порядок маршрутов](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="67a5e-825">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="67a5e-826">Замена токенов в шаблонах маршрутов ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="67a5e-826">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="67a5e-827">Для удобства маршруты к атрибутам поддерживают *замену маркера* путем заключения маркера в квадратные скобки ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="67a5e-827">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="67a5e-828">Токены `[action]`, `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.</span><span class="sxs-lookup"><span data-stu-id="67a5e-828">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="67a5e-829">В следующем примере действия могут соответствовать путям URL-адресов, как описано в комментариях:</span><span class="sxs-lookup"><span data-stu-id="67a5e-829">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="67a5e-830">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-830">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="67a5e-831">Приведенный выше пример будет работать так же, как следующий код:</span><span class="sxs-lookup"><span data-stu-id="67a5e-831">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="67a5e-832">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="67a5e-832">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="67a5e-833">Эта возможность особенно эффективна при использовании вместе с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-833">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="67a5e-834">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-834">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="67a5e-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]` создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-835">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="67a5e-836">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-836">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="67a5e-837">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="67a5e-837">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="67a5e-838">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="67a5e-838">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="67a5e-839">Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="67a5e-839">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="67a5e-840">Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-840">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="67a5e-841">`RouteTokenTransformerConvention` является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="67a5e-841">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="67a5e-842">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="67a5e-842">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="67a5e-843">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="67a5e-843">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="67a5e-844">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-844">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="67a5e-845">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-845">Multiple Routes</span></span>

<span data-ttu-id="67a5e-846">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-846">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="67a5e-847">Наиболее распространенный случай использования этой возможности — имитация поведения *маршрута по умолчанию на основе соглашения*, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="67a5e-847">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="67a5e-848">Добавление нескольких атрибутов маршрута для контроллера означает, что каждый из них будет объединяться с каждым из атрибутов маршрута, определенных для методов действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-848">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="67a5e-849">Если несколько атрибутов маршрута (реализующих интерфейс `IActionConstraint`) добавлены для действия, каждое ограничение действия объединяется с шаблоном маршрута из атрибута, в котором оно определено.</span><span class="sxs-lookup"><span data-stu-id="67a5e-849">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="67a5e-850">Хотя использование нескольких маршрутов к действиям может показаться очень эффективной возможностью, лучше, чтобы пространство URL-адресов приложения оставалось простым и четко организованным.</span><span class="sxs-lookup"><span data-stu-id="67a5e-850">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="67a5e-851">Используйте несколько маршрутов к действиям только там, где это необходимо, например для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-851">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="67a5e-852">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="67a5e-852">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="67a5e-853">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-853">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="67a5e-854">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="67a5e-854">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="67a5e-855">Пользовательские атрибуты маршрута с использованием `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="67a5e-855">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="67a5e-856">Все атрибуты маршрутов, предоставляемые платформой ( `[Route(...)]`, `[HttpGet(...)]` и т. д.), реализуют интерфейс `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-856">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="67a5e-857">MVC ищет атрибуты в классах контроллеров и методах действий при запуске приложения и использует те из них, которые реализуют интерфейс `IRouteTemplateProvider`, для формирования начального набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-857">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="67a5e-858">Вы можете реализовать интерфейс `IRouteTemplateProvider` для определения собственных атрибутов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-858">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="67a5e-859">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="67a5e-859">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="67a5e-860">Атрибут в приведенном выше примере автоматически присваивает шаблону `Template` значение `"api/[controller]"` при применении `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-860">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="67a5e-861">Настройка маршрутов на основе атрибутов с помощью модели приложения</span><span class="sxs-lookup"><span data-stu-id="67a5e-861">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="67a5e-862">*Модель приложения* — это объектная модель, которая создается при запуске со всеми метаданными, используемыми платформой MVC для маршрутизации и выполнения действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-862">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="67a5e-863">*Модель приложения* включает в себя все данные, собранные из атрибутов маршрутов (посредством интерфейса `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-863">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="67a5e-864">Вы можете создать *соглашения*, чтобы изменить модель приложения при запуске с целью настройки маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-864">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="67a5e-865">В этом разделе приводится простой пример настройки маршрутизации с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-865">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="67a5e-866">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-866">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="67a5e-867">В приложениях MVC маршрутизация с помощью атрибутов и маршрутизация на основе соглашений могут использоваться вместе.</span><span class="sxs-lookup"><span data-stu-id="67a5e-867">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="67a5e-868">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="67a5e-868">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="67a5e-869">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-869">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="67a5e-870">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-870">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="67a5e-871">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="67a5e-871">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="67a5e-872">**Любой** атрибут маршрута контроллера делает все действия в атрибуте контроллера маршрутизируемыми.</span><span class="sxs-lookup"><span data-stu-id="67a5e-872">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-873">Два типа систем маршрутизации отличает процесс, выполняемый после нахождения URL-адреса, соответствующего шаблону маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-873">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="67a5e-874">При маршрутизации на основе соглашений значения маршрута из соответствия используются для выбора действия и контроллера из таблицы подстановки, содержащей все действия с маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-874">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="67a5e-875">При маршрутизации с помощью атрибутов каждый шаблон уже связан с действием, и дальнейшая подстановка не требуется.</span><span class="sxs-lookup"><span data-stu-id="67a5e-875">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="67a5e-876">Сложные сегменты</span><span class="sxs-lookup"><span data-stu-id="67a5e-876">Complex segments</span></span>

<span data-ttu-id="67a5e-877">Сложные сегменты (например, `[Route("/dog{token}cat")]`) обрабатываются путем "нежадного" сопоставления литералов справа налево.</span><span class="sxs-lookup"><span data-stu-id="67a5e-877">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="67a5e-878">Описание см. в [исходном коде](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="67a5e-878">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="67a5e-879">Дополнительные сведения см. в [этой проблеме](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="67a5e-879">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="67a5e-880">Формирование URL-адреса</span><span class="sxs-lookup"><span data-stu-id="67a5e-880">URL Generation</span></span>

<span data-ttu-id="67a5e-881">Приложения MVC могут использовать функции формирования URL-адреса, предоставляемые системой маршрутизации, для создания URL-ссылок на действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-881">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="67a5e-882">Формирование URL-адресов устраняет необходимость в их жестком задании, что делает код надежнее и проще в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="67a5e-882">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="67a5e-883">В этом разделе рассматриваются функции формирования URL-адреса, предоставляемые платформой MVC, и описываются лишь базовые принципы их работы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-883">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="67a5e-884">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="67a5e-884">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="67a5e-885">Интерфейс `IUrlHelper` — это базовый компонент инфраструктуры, обеспечивающий взаимодействие между MVC и системой маршрутизации для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-885">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="67a5e-886">Доступ к экземпляру `IUrlHelper` в контроллерах, представлениях и компонентах представлений можно получить посредством свойства `Url`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-886">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="67a5e-887">В этом примере интерфейс `IUrlHelper` используется посредством свойства `Controller.Url` для формирования URL-адреса другого действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-887">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="67a5e-888">Если в приложении применяется маршрут по умолчанию на основе соглашения, значением переменной `url` будет строка с путем URL-адреса `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-888">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="67a5e-889">Этот путь URL-адреса создается системой маршрутизации путем объединения значений маршрута из текущего запроса (значения окружения) со значениями, переданными в `Url.Action`, и подстановки этих значений в шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-889">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="67a5e-890">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-890">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="67a5e-891">Параметр маршрута, у которого нет значения, может принимать значение по умолчанию, если оно имеется, или пропускаться, если он является необязательным (как в случае с параметром `id` в этом примере).</span><span class="sxs-lookup"><span data-stu-id="67a5e-891">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="67a5e-892">Сформировать URL-адрес не удастся, если у любого из обязательных параметров маршрута не будет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-892">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="67a5e-893">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="67a5e-893">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="67a5e-894">В примере `Url.Action` выше предполагается использование маршрутизации на основе соглашений, однако формирование URL-адреса происходит аналогичным образом и в случае с маршрутизацией с помощью атрибутов, хотя принципы иные.</span><span class="sxs-lookup"><span data-stu-id="67a5e-894">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="67a5e-895">При применении маршрутизации на основе соглашений шаблон расширяется с помощью значений маршрута, и значения маршрута для `controller` и `action` обычно находятся в этом шаблоне. Это возможно по той причине, что URL-адреса, сопоставленные системой маршрутизации, следуют *соглашению*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-895">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="67a5e-896">При маршрутизации с помощью атрибутов значения маршрута для `controller` и `action` не могут находиться в шаблоне. Вместо этого они применяются для поиска нужного шаблона.</span><span class="sxs-lookup"><span data-stu-id="67a5e-896">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="67a5e-897">В этом примере используется маршрутизация с помощью атрибутов:</span><span class="sxs-lookup"><span data-stu-id="67a5e-897">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="67a5e-898">MVC создает таблицу подстановки, содержащую все действия с маршрутизацией с помощью атрибутов, и сопоставляет значения `controller` и `action` для выбора шаблона маршрута, с помощью которого должен формироваться URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="67a5e-898">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="67a5e-899">В приведенном выше примере создается URL-адрес `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-899">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="67a5e-900">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="67a5e-900">Generating URLs by action name</span></span>

<span data-ttu-id="67a5e-901">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="67a5e-901">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="67a5e-902">`Action`) и все связанные перегрузки предполагают необходимость указания целевого объекта путем задания имени контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-902">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-903">При использовании метода `Url.Action` текущие значения маршрута для `controller` и `action` уже определены: значения `controller` и `action` включены как в \*значения окружения, так и в \* **обычные** *значения*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-903">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="67a5e-904">Метод `Url.Action` всегда использует текущие значения `action` и `controller` и формирует путь URL-адреса, ведущий к текущему действию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-904">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="67a5e-905">При формировании URL-адреса система маршрутизации пытается использовать значения окружения для заполнения сведений, которые вы не предоставили.</span><span class="sxs-lookup"><span data-stu-id="67a5e-905">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="67a5e-906">При использовании такого маршрута, как `{a}/{b}/{c}/{d}`, и значений окружения `{ a = Alice, b = Bob, c = Carol, d = David }` система маршрутизации имеет достаточно информации для формирования URL-адреса без дополнительных значений, так как все параметры маршрута имеют значения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-906">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="67a5e-907">Если добавить значение `{ d = Donovan }`, значение `{ d = David }` не будет учитываться, и будет сформирован путь URL-адреса `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-907">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="67a5e-908">Пути URL-адресов являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="67a5e-908">URL paths are hierarchical.</span></span> <span data-ttu-id="67a5e-909">Если в приведенном выше примере добавить значение `{ c = Cheryl }`, пропущены будут оба значения `{ c = Carol, d = David }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-909">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="67a5e-910">В этом случае параметр `d` больше не имеет значения и сформировать URL-адрес не удастся.</span><span class="sxs-lookup"><span data-stu-id="67a5e-910">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="67a5e-911">Потребуется указать нужные значения для параметров `c` и `d`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-911">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="67a5e-912">Эта проблема может возникнуть с маршрутом по умолчанию (`{controller}/{action}/{id?}`), но на практике она встречается редко, так как `Url.Action` всегда явным образом задает значения `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-912">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="67a5e-913">Более длинные перегрузки `Url.Action` также принимают дополнительный объект *значений маршрута* для предоставления значений параметров маршрута, отличных от `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-913">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="67a5e-914">Чаще всего он применяется с параметром `id`, например `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-914">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="67a5e-915">В соответствии с соглашением объект *значений маршрута* обычно имеет анонимный тип, но это может быть также экземпляр `IDictionary<>` или *обычный объект .NET*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-915">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="67a5e-916">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-916">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="67a5e-917">Чтобы создать абсолютный URL-адрес, используйте перегрузку, принимающую `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="67a5e-917">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="67a5e-918">Формирование URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="67a5e-918">Generating URLs by route</span></span>

<span data-ttu-id="67a5e-919">В приведенном выше коде демонстрировалось формирование URL-адреса путем передачи имен контроллера и действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-919">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="67a5e-920">Интерфейс `IUrlHelper` также предоставляет семейство методов `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-920">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="67a5e-921">Эти методы похожи на `Url.Action`, но они не копируют текущие значения `action` и `controller` в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-921">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="67a5e-922">Наиболее распространенный способ их применения — указание имени определенного маршрута, который должен использоваться для формирования URL-адреса, как правило, *без* указания имени контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-922">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="67a5e-923">Формирование URL-адресов в HTML</span><span class="sxs-lookup"><span data-stu-id="67a5e-923">Generating URLs in HTML</span></span>

<span data-ttu-id="67a5e-924">Интерфейс `IHtmlHelper` предоставляет методы `HtmlHelper``Html.BeginForm` и `Html.ActionLink` для создания элементов `<form>` и `<a>` соответственно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-924">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="67a5e-925">Эти методы используют метод `Url.Action` для формирования URL-адреса и принимают одинаковые аргументы.</span><span class="sxs-lookup"><span data-stu-id="67a5e-925">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="67a5e-926">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="67a5e-926">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="67a5e-927">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-927">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="67a5e-928">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-928">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="67a5e-929">Дополнительные сведения см. в статье [Работа с формами](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="67a5e-929">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="67a5e-930">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="67a5e-930">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="67a5e-931">Формирование URL-адресов в результатах действий</span><span class="sxs-lookup"><span data-stu-id="67a5e-931">Generating URLS in Action Results</span></span>

<span data-ttu-id="67a5e-932">В предыдущих примерах было продемонстрировано использование интерфейса `IUrlHelper` в контроллере, хотя чаще всего URL-адреса формируются в контроллерах в рамках результата действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-932">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="67a5e-933">Базовые классы `ControllerBase` и `Controller` предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="67a5e-933">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="67a5e-934">Одним из типичных способов их применения является перенаправление после принятия входных данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="67a5e-934">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="67a5e-935">Фабричные методы результатов действий по своей структуре похожи на методы интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-935">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="67a5e-936">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="67a5e-936">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="67a5e-937">При маршрутизации на основе соглашений может использоваться особый тип определения маршрута — *выделенный маршрут на основе соглашения*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-937">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="67a5e-938">В приведенном ниже примере маршрут с именем `blog` является выделенным маршрутом на основе соглашения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-938">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="67a5e-939">При использовании таких определений маршрутов метод `Url.Action("Index", "Home")` создаст путь URL-адреса `/` с маршрутом `default`. В чем же причина?</span><span class="sxs-lookup"><span data-stu-id="67a5e-939">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="67a5e-940">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-940">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="67a5e-941">В случае с выделенными маршрутами на основе соглашений используется особое поведение значений по умолчанию, для которых нет соответствующих параметров маршрута. Благодаря ему маршрут не может быть слишком универсальным при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-941">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="67a5e-942">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="67a5e-942">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="67a5e-943">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="67a5e-943">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="67a5e-944">Сформировать URL-адрес с помощью `blog` не удастся, так как значения `{ controller = Home, action = Index }` не соответствуют `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-944">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="67a5e-945">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="67a5e-945">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="67a5e-946">Области</span><span class="sxs-lookup"><span data-stu-id="67a5e-946">Areas</span></span>

<span data-ttu-id="67a5e-947">[Области](areas.md) — это возможность MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен маршрутизации (для действий контроллеров) и структуры папок (для представлений).</span><span class="sxs-lookup"><span data-stu-id="67a5e-947">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="67a5e-948">Благодаря областям приложение может иметь несколько контроллеров с одинаковыми именами, если у них будут разные *области*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-948">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="67a5e-949">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-949">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="67a5e-950">В этом разделе рассматривается взаимодействие системы маршрутизации с областями. Подробные сведения об использовании областей с представлениями см. в статье [Области](areas.md).</span><span class="sxs-lookup"><span data-stu-id="67a5e-950">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="67a5e-951">В следующем примере в MVC настраивается использование маршрута на основе соглашения по умолчанию и *маршрута области* для области с именем `Blog`:</span><span class="sxs-lookup"><span data-stu-id="67a5e-951">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="67a5e-952">Результатом сопоставления первого маршрута с таким путем URL-адреса, как `/Manage/Users/AddUser`, будут значения маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-952">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="67a5e-953">Значение маршрута `area` получается на основе значения по умолчанию для параметра `area`. По сути, маршрут, создаваемый методом `MapAreaRoute`, эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="67a5e-953">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="67a5e-954">Метод `MapAreaRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="67a5e-954">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="67a5e-955">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-955">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="67a5e-956">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="67a5e-956">Conventional routing is order-dependent.</span></span> <span data-ttu-id="67a5e-957">Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.</span><span class="sxs-lookup"><span data-stu-id="67a5e-957">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="67a5e-958">В предыдущем примере значения маршрута будут соответствовать следующему действию:</span><span class="sxs-lookup"><span data-stu-id="67a5e-958">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="67a5e-959">`AreaAttribute` обозначает контроллер в рамках области. В таком случае говорят, что контроллер находится в области `Blog`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-959">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="67a5e-960">Контроллеры без атрибута `[Area]` не входят ни в одну область и **не** будут соответствовать маршруту, если система маршрутизации предоставляет значение маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-960">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="67a5e-961">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-961">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="67a5e-962">Пространство имен каждого контроллера приведено здесь для полноты. В противном случае между контроллерами возник бы конфликт именования, и произошла бы ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="67a5e-962">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="67a5e-963">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="67a5e-963">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="67a5e-964">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-964">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="67a5e-965">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="67a5e-965">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-966">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="67a5e-966">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="67a5e-967">При выполнении действия внутри области значение маршрута для `area` будет доступно как *значение окружения*, которое система маршрутизации использует для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="67a5e-967">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="67a5e-968">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="67a5e-968">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="67a5e-969">Основные сведения об интерфейсе IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="67a5e-969">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="67a5e-970">В этом разделе описываются внутренние принципы работы платформы и то, как MVC выбирает действие, которое необходимо выполнить.</span><span class="sxs-lookup"><span data-stu-id="67a5e-970">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="67a5e-971">Типичному приложению пользовательская реализация `IActionConstraint` не требуется.</span><span class="sxs-lookup"><span data-stu-id="67a5e-971">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="67a5e-972">Скорее всего, вы уже пользовались интерфейсом `IActionConstraint`, даже если не знакомы с ним.</span><span class="sxs-lookup"><span data-stu-id="67a5e-972">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="67a5e-973">Атрибут `[HttpGet]` и сходные атрибуты `[Http-VERB]` реализуют интерфейс `IActionConstraint`, чтобы ограничить выполнение метода действия.</span><span class="sxs-lookup"><span data-stu-id="67a5e-973">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="67a5e-974">В случае с маршрутом по умолчанию на основе соглашения путь URL-адреса `/Products/Edit` даст значения `{ controller = Products, action = Edit }`, которые будут соответствовать **обоим** приведенным здесь действиям.</span><span class="sxs-lookup"><span data-stu-id="67a5e-974">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="67a5e-975">В терминологии `IActionConstraint` можно сказать, что оба действия считаются кандидатами, так как они оба соответствуют данным маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-975">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="67a5e-976">Когда метод `HttpGetAttribute` выполняется, он сообщает, что *Edit()* является соответствием для запроса *GET*, но не является соответствием для каких-либо иных HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="67a5e-976">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="67a5e-977">Для действия `Edit(...)` ограничения не определены, поэтому оно соответствует любой HTTP-команде.</span><span class="sxs-lookup"><span data-stu-id="67a5e-977">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="67a5e-978">Поэтому если рассматривать запрос `POST`, соответствием будет только `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-978">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="67a5e-979">Однако в случае с запросом `GET` соответствовать могут оба действия, хотя действие с `IActionConstraint` всегда считается *имеющим приоритет*.</span><span class="sxs-lookup"><span data-stu-id="67a5e-979">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="67a5e-980">Таким образом, поскольку действие `Edit()` имеет атрибут `[HttpGet]`, оно считается более конкретным и будет выбрано в случае соответствия обоих действий.</span><span class="sxs-lookup"><span data-stu-id="67a5e-980">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="67a5e-981">По сути, интерфейс `IActionConstraint` представляет собой своего рода *перегрузку*, но вместо перегрузки методов с одинаковыми именами он перегружает действия, соответствующие одному и тому же URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="67a5e-981">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="67a5e-982">При маршрутизации с помощью атрибутов также применяется интерфейс `IActionConstraint`, в результате чего кандидатами могут считаться действия из разных контроллеров.</span><span class="sxs-lookup"><span data-stu-id="67a5e-982">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="67a5e-983">Реализация IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="67a5e-983">Implementing IActionConstraint</span></span>

<span data-ttu-id="67a5e-984">Самый простой способ реализовать интерфейс `IActionConstraint` — создать класс, производный от `System.Attribute`, и добавить его в действия и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="67a5e-984">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="67a5e-985">MVC автоматически обнаруживает экземпляры `IActionConstraint`, применяемые как атрибуты.</span><span class="sxs-lookup"><span data-stu-id="67a5e-985">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="67a5e-986">Вы можете использовать модель приложения для применения ограничений, и это, пожалуй, самый гибкий подход, так как он позволяет метапрограммировать способ их применения.</span><span class="sxs-lookup"><span data-stu-id="67a5e-986">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="67a5e-987">В следующем примере ограничение выбирает действие на основе *кода страны* из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="67a5e-987">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="67a5e-988">[Полный пример можно найти в GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="67a5e-988">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="67a5e-989">Вы отвечаете за реализацию метода `Accept` и определение очередности применения ограничений.</span><span class="sxs-lookup"><span data-stu-id="67a5e-989">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="67a5e-990">В этом случае метод `Accept` возвращает значение `true`, означающее, что действие является соответствующим при соответствии значения маршрута `country`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-990">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="67a5e-991">Таким образом, он позволяет возвращаться к действию без атрибута, чем отличается от метода `RouteValueAttribute`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-991">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="67a5e-992">В примере показано, что если определено действие `en-US`, то для кода страны `fr-FR` будет выбран более общий контроллер, к которому не применяется ограничение `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-992">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="67a5e-993">Свойство `Order` определяет, к какому *этапу* относится ограничение.</span><span class="sxs-lookup"><span data-stu-id="67a5e-993">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="67a5e-994">Ограничения действий применяются группами в соответствии со значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="67a5e-994">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="67a5e-995">Например, все предоставляемые платформой атрибуты HTTP-методов имеют одинаковое значение `Order`, благодаря чему они выполняются на одном этапе.</span><span class="sxs-lookup"><span data-stu-id="67a5e-995">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="67a5e-996">Чтобы реализовать требуемые политики, можно использовать любое количество этапов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-996">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="67a5e-997">Чтобы выбрать значение `Order`, подумайте, должно ли ограничение применяться перед выполнением HTTP-методов.</span><span class="sxs-lookup"><span data-stu-id="67a5e-997">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="67a5e-998">Чем меньше значение, тем раньше применяется ограничение.</span><span class="sxs-lookup"><span data-stu-id="67a5e-998">Lower numbers run first.</span></span>

::: moniker-end
