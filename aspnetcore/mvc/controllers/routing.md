---
title: Маршрутизация к действиям контроллера в ASP.NET Core
author: rick-anderson
description: Узнайте, как в MVC ASP.NET Core используется ПО промежуточного слоя маршрутизации для сопоставления URL-адресов входящих запросов с действиями.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277136"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="02433-103">Маршрутизация к действиям контроллера в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="02433-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="02433-104">Авторы: [Райан Новак](https://github.com/rynowak) (Ryan Nowak), [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin) и [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="02433-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02433-105">ASP.NET контроллеры Core используют [промежуточное программное обеспечение](xref:fundamentals/middleware/index) Routing для сопоставления URL-адресов входящих запросов и сопометки их к [действиям.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="02433-106">Шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-106">Routes templates:</span></span>

* <span data-ttu-id="02433-107">Определены в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="02433-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="02433-108">Опишите, как пути URL сопоставляются с [действиями.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="02433-109">Используются для создания URL-адресов для ссылок.</span><span class="sxs-lookup"><span data-stu-id="02433-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="02433-110">Генерируемые ссылки обычно возвращаются в ответах.</span><span class="sxs-lookup"><span data-stu-id="02433-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="02433-111">Действия либо [условно-маршрутизируются,](#cr) либо [направляются атрибутами.](#ar)</span><span class="sxs-lookup"><span data-stu-id="02433-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="02433-112">Размещение маршрута на контроллере или [действии](#action) делает его атрибут-маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="02433-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="02433-113">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="02433-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="02433-114">Этот документ:</span><span class="sxs-lookup"><span data-stu-id="02433-114">This document:</span></span>

* <span data-ttu-id="02433-115">Объясняет взаимодействие между MVC и routing:</span><span class="sxs-lookup"><span data-stu-id="02433-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="02433-116">Как типичные приложения MVC используют функции конечной вымысания.</span><span class="sxs-lookup"><span data-stu-id="02433-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="02433-117">Покрывает оба:</span><span class="sxs-lookup"><span data-stu-id="02433-117">Covers both:</span></span>
    * <span data-ttu-id="02433-118">[Условно йаутинг](#cr) обычно используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="02433-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="02433-119">*Атрибутная направление* используется с помощью aIS REST.</span><span class="sxs-lookup"><span data-stu-id="02433-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="02433-120">Если вы в первую очередь заинтересованы в области routing для REST AIS, перейти к [атрибуту трассировки для раздела REST AIS.](#ar)</span><span class="sxs-lookup"><span data-stu-id="02433-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="02433-121">Посмотреть [Routing](xref:fundamentals/routing) для расширенных деталей разгрома.</span><span class="sxs-lookup"><span data-stu-id="02433-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="02433-122">Относится к системе по умолчанию, добавленной в ASP.NET Core 3.0, называемой конечным пунктом.</span><span class="sxs-lookup"><span data-stu-id="02433-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="02433-123">Можно использовать контроллеры с предыдущей версией направления для целей совместимости.</span><span class="sxs-lookup"><span data-stu-id="02433-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="02433-124">Для получения инструкций смотрите [руководство по миграции 2.2-3.0.](xref:migration/22-to-30)</span><span class="sxs-lookup"><span data-stu-id="02433-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="02433-125">Ссылайтесь на [версию 2.2 этого документа](xref:mvc/controllers/routing?view=aspnetcore-2.2) для справочного материала по устаревшей системе конечной выдвижения.</span><span class="sxs-lookup"><span data-stu-id="02433-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="02433-126">Настройка обычного маршрута</span><span class="sxs-lookup"><span data-stu-id="02433-126">Set up conventional route</span></span>

<span data-ttu-id="02433-127">`Startup.Configure`обычно имеет код, похожий на следующий при [использовании обычной реукторе:](#crd)</span><span class="sxs-lookup"><span data-stu-id="02433-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="02433-128">Внутри <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>вызова, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> используется для создания единого маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="02433-129">Единый маршрут `default` называется маршрут.</span><span class="sxs-lookup"><span data-stu-id="02433-129">The single route is named `default` route.</span></span> <span data-ttu-id="02433-130">Большинство приложений с контроллерами и представлениями `default` используют шаблон маршрута, похожий на маршрут.</span><span class="sxs-lookup"><span data-stu-id="02433-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="02433-131">REST AIS должны использовать [атрибуты routing.](#ar)</span><span class="sxs-lookup"><span data-stu-id="02433-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="02433-132">Шаблон `"{controller=Home}/{action=Index}/{id?}"`маршрута :</span><span class="sxs-lookup"><span data-stu-id="02433-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="02433-133">Соответствует траектории URL-`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="02433-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="02433-134">Извлекает значения `{ controller = Products, action = Details, id = 5 }` маршрута, токенизазовав путь.</span><span class="sxs-lookup"><span data-stu-id="02433-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="02433-135">Извлечение значений маршрута приводит к совпадению, если приложение имеет имя `ProductsController` контроллера `Details` и действие:</span><span class="sxs-lookup"><span data-stu-id="02433-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="02433-136">`/Products/Details/5`модель связывает значение `id = 5` для установки `id` `5`параметра.</span><span class="sxs-lookup"><span data-stu-id="02433-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="02433-137">Подробнее о [см.](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="02433-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="02433-138">`{controller=Home}`определяет `Home` как значение `controller`по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="02433-139">`{action=Index}`определяет `Index` как значение `action`по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="02433-140">Символ `?` в `{id?}` определяет `id` как необязательный.</span><span class="sxs-lookup"><span data-stu-id="02433-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="02433-141">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="02433-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="02433-142">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="02433-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="02433-143">Соответствует траектории `/`URL.</span><span class="sxs-lookup"><span data-stu-id="02433-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="02433-144">Производит значения `{ controller = Home, action = Index }`маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="02433-145">Значения для `controller` значений по умолчанию используются и `action` используются.</span><span class="sxs-lookup"><span data-stu-id="02433-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="02433-146">`id`не производит значение, так как в пути URL нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="02433-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="02433-147">`/`только совпадает, `HomeController` если `Index` существует действие:</span><span class="sxs-lookup"><span data-stu-id="02433-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="02433-148">Используя предыдущее определение контроллера `HomeController.Index` и шаблон маршрута, действие выполняется для следующих путей URL:</span><span class="sxs-lookup"><span data-stu-id="02433-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="02433-149">Путь `/` URL использует контроллеры `Home` и `Index` действия шаблона маршрута по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="02433-150">Путь `/Home` URL использует действие `Index` шаблона маршрута по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="02433-151">Универсальный метод <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="02433-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="02433-152">Заменяет:</span><span class="sxs-lookup"><span data-stu-id="02433-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="02433-153">Routing настроен с <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> помощью и <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> промежуточного посуды.</span><span class="sxs-lookup"><span data-stu-id="02433-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="02433-154">Для использования контроллеров:</span><span class="sxs-lookup"><span data-stu-id="02433-154">To use controllers:</span></span>
>
> * <span data-ttu-id="02433-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> Звоните `UseEndpoints` внутрь, чтобы [отобрадить приписываемые](#ar) контроллеры.</span><span class="sxs-lookup"><span data-stu-id="02433-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="02433-156"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Позвоните <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>или , чтобы составить карту [условно маршрутистирных](#cr) контроллеров.</span><span class="sxs-lookup"><span data-stu-id="02433-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="02433-157">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-157">Conventional routing</span></span>

<span data-ttu-id="02433-158">Обычная реуктора используется с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="02433-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="02433-159">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="02433-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="02433-160">является примером *маршрутизации на основе соглашений*.</span><span class="sxs-lookup"><span data-stu-id="02433-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="02433-161">Это называется *обычной маршрутизирующей сяре,* поскольку она устанавливает *конвенцию* для путей URL:</span><span class="sxs-lookup"><span data-stu-id="02433-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="02433-162">Первый сегмент `{controller=Home}`пути, карты к имени контроллера.</span><span class="sxs-lookup"><span data-stu-id="02433-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="02433-163">Второй сегмент, `{action=Index}`карты к названию [действия.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="02433-164">Третий сегмент, `{id?}` используется для `id`факультативного .</span><span class="sxs-lookup"><span data-stu-id="02433-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="02433-165">`?` В `{id?}` делает его необязательным.</span><span class="sxs-lookup"><span data-stu-id="02433-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="02433-166">`id`используется для картирования модели сущности.</span><span class="sxs-lookup"><span data-stu-id="02433-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="02433-167">Используя `default` этот маршрут, путь URL:</span><span class="sxs-lookup"><span data-stu-id="02433-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="02433-168">`/Products/List`карты к `ProductsController.List` действию.</span><span class="sxs-lookup"><span data-stu-id="02433-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="02433-169">`/Blog/Article/17`карты `BlogController.Article` и, как правило, модель связывает `id` параметр с 17.</span><span class="sxs-lookup"><span data-stu-id="02433-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="02433-170">Это отображение:</span><span class="sxs-lookup"><span data-stu-id="02433-170">This mapping:</span></span>

* <span data-ttu-id="02433-171">Основано только на **контроллере**и именах [действий.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="02433-172">Не основано на пространствах имен, местоположении исходного файла или параметрах метода.</span><span class="sxs-lookup"><span data-stu-id="02433-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="02433-173">Использование обычной маршрутики с маршрутом по умолчанию позволяет создать приложение без необходимости придумывать новый шаблон URL для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="02433-174">Для приложения с действиями стиля [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) имеющего согласованность для URL-адресов между контроллерами:</span><span class="sxs-lookup"><span data-stu-id="02433-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="02433-175">Помогает упростить код.</span><span class="sxs-lookup"><span data-stu-id="02433-175">Helps simplify the code.</span></span>
* <span data-ttu-id="02433-176">Делает uI более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="02433-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="02433-177">В `id` предыдущем коде определяется как необязательный шаблоном маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="02433-178">Действия могут выполняться без дополнительного идентификатора, предоставленного как часть URL-.</span><span class="sxs-lookup"><span data-stu-id="02433-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="02433-179">Как правило, при`id` исключении из URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="02433-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="02433-180">`id`устанавливается `0` по привязке модели.</span><span class="sxs-lookup"><span data-stu-id="02433-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="02433-181">В сопоставлении `id == 0`базы данных не найдено сущность.</span><span class="sxs-lookup"><span data-stu-id="02433-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="02433-182">[Ацентная routing](#ar) обеспечивает мелкозернистый контроль, чтобы сделать идентификатор, необходимый для некоторых действий, а не для других.</span><span class="sxs-lookup"><span data-stu-id="02433-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="02433-183">По конвенции документация включает `id` в себя дополнительные параметры, например, когда они могут отображаться при правильном использовании.</span><span class="sxs-lookup"><span data-stu-id="02433-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="02433-184">Для большинства приложений следует выбрать базовую описательную схему маршрутизации таким образом, чтобы URL-адреса были удобочитаемыми и осмысленными.</span><span class="sxs-lookup"><span data-stu-id="02433-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="02433-185">Традиционный маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="02433-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="02433-186">Поддерживает основную и описательную схемы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="02433-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="02433-187">Является отправной точкой для приложений на базе пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="02433-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="02433-188">Является единственным шаблоном маршрута, необходимым для многих приложений веб-uI.</span><span class="sxs-lookup"><span data-stu-id="02433-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="02433-189">Для более крупных приложений веб-uI, другой маршрут с использованием [области,](#areas) если часто все, что нужно.</span><span class="sxs-lookup"><span data-stu-id="02433-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="02433-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>и: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*></span><span class="sxs-lookup"><span data-stu-id="02433-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="02433-191">Автоматически присваивай значение **заказа** конечным точкам в зависимости от заказа, на который они ссылаются.</span><span class="sxs-lookup"><span data-stu-id="02433-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="02433-192">Конечная точка в ASP.NET Core 3.0 и позже:</span><span class="sxs-lookup"><span data-stu-id="02433-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="02433-193">Не имеет понятия маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="02433-194">Не дает гарантий заказа на выполнение расширяемости, все конечные точки обрабатываются сразу.</span><span class="sxs-lookup"><span data-stu-id="02433-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="02433-195">Чтобы увидеть, как встроенные реализации маршрутизации, такие как <xref:Microsoft.AspNetCore.Routing.Route>, сопоставляются с запросами, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="02433-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="02433-196">В этом документе [позже](#ar) объясняется разгром атрибута.</span><span class="sxs-lookup"><span data-stu-id="02433-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="02433-197">Несколько обычных маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-197">Multiple conventional routes</span></span>

<span data-ttu-id="02433-198">Несколько [обычных маршрутов](#cr) могут быть добавлены внутри, `UseEndpoints` добавив больше звонков <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> и <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="02433-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="02433-199">Это позволяет определить несколько конвенций или добавить обычные маршруты, предназначенные для конкретного [действия,](#action)такие как:</span><span class="sxs-lookup"><span data-stu-id="02433-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="02433-200">Маршрут `blog` в предыдущем коде представляет собой **выделенный обычный маршрут.**</span><span class="sxs-lookup"><span data-stu-id="02433-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="02433-201">Это называется выделенным обычным маршрутом, потому что:</span><span class="sxs-lookup"><span data-stu-id="02433-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="02433-202">Он использует [обычные области.](#cr)</span><span class="sxs-lookup"><span data-stu-id="02433-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="02433-203">Он посвящен конкретному [действию.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="02433-204">Потому что `controller` и `action` не отображаются в шаблоне `"blog/{*article}"` маршрута в качестве параметров:</span><span class="sxs-lookup"><span data-stu-id="02433-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="02433-205">Они могут иметь только `{ controller = "Blog", action = "Article" }`значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="02433-206">Этот маршрут всегда отображает `BlogController.Article`сява к действию.</span><span class="sxs-lookup"><span data-stu-id="02433-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="02433-207">`/Blog`, `/Blog/Article`и `/Blog/{any-string}` являются единственными url-маршрутами, которые соответствуют маршруту блога.</span><span class="sxs-lookup"><span data-stu-id="02433-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="02433-208">Предыдущий пример:</span><span class="sxs-lookup"><span data-stu-id="02433-208">The preceding example:</span></span>

* <span data-ttu-id="02433-209">`blog`маршрут имеет более высокий `default` приоритет для совпадений, чем маршрут, потому что он добавляется первым.</span><span class="sxs-lookup"><span data-stu-id="02433-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="02433-210">Является и примером [slug](https://developer.mozilla.org/docs/Glossary/Slug) стиль разгрома, где это типично иметь имя статьи как часть URL.</span><span class="sxs-lookup"><span data-stu-id="02433-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="02433-211">В ASP.NET Core 3.0 и позже, направление не:</span><span class="sxs-lookup"><span data-stu-id="02433-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="02433-212">Определите концепцию, называемую *маршрутом.*</span><span class="sxs-lookup"><span data-stu-id="02433-212">Define a concept called a *route*.</span></span> <span data-ttu-id="02433-213">`UseRouting`добавляет маршрут, соответствующий конвейеру промежуточного посуды.</span><span class="sxs-lookup"><span data-stu-id="02433-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="02433-214">Промежуточное `UseRouting` программное обеспечение рассматривает набор конечных точек, определенных в приложении, и выбирает наилучший матч конечной точки на основе запроса.</span><span class="sxs-lookup"><span data-stu-id="02433-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="02433-215">Предоставьте гарантии о порядке исполнения <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> расширяемости как или <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="02433-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="02433-216">Смотрите [Routing](xref:fundamentals/routing) для справочного материала по реудалировке.</span><span class="sxs-lookup"><span data-stu-id="02433-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="02433-217">Обычный порядок вхомливания</span><span class="sxs-lookup"><span data-stu-id="02433-217">Conventional routing order</span></span>

<span data-ttu-id="02433-218">Обычная routing только соответствует комбинации действий и контроллера, которые определяются приложением.</span><span class="sxs-lookup"><span data-stu-id="02433-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="02433-219">Это призвано упростить случаи перекрытия обычных маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="02433-220">Добавление маршрутов <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>с <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> использованием, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>и автоматически присваиваете значение заказа своим конечным точкам в зависимости от заказа, на который они ссылаются.</span><span class="sxs-lookup"><span data-stu-id="02433-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="02433-221">Совпадения с маршрута, который появляется ранее, имеют более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="02433-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="02433-222">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="02433-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="02433-223">В общем, маршруты с областями должны быть размещены раньше, поскольку они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="02433-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="02433-224">[Выделенные обычные маршруты](#dcr) с `{*article}` уловом всех параметров маршрута, как может сделать маршрут слишком [жадным,](xref:fundamentals/routing#greedy)это означает, что он соответствует URL-адреса, которые вы намеревались быть сопоставлены с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="02433-224">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="02433-225">Поместите жадные маршруты позже в таблице маршрутов, чтобы предотвратить жадные матчи.</span><span class="sxs-lookup"><span data-stu-id="02433-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="02433-226">Разрешение неоднозначных действий</span><span class="sxs-lookup"><span data-stu-id="02433-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="02433-227">Когда две конечные точки совпадают с помощью реуктора, разгром должен сделать одну из следующих:</span><span class="sxs-lookup"><span data-stu-id="02433-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="02433-228">Выберите лучшего кандидата.</span><span class="sxs-lookup"><span data-stu-id="02433-228">Choose the best candidate.</span></span>
* <span data-ttu-id="02433-229">Создание исключения.</span><span class="sxs-lookup"><span data-stu-id="02433-229">Throw an exception.</span></span>

<span data-ttu-id="02433-230">Пример:</span><span class="sxs-lookup"><span data-stu-id="02433-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="02433-231">Предыдущий контроллер определяет два действия, которые совпадают:</span><span class="sxs-lookup"><span data-stu-id="02433-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="02433-232">Путь URL-адреса`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="02433-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="02433-233">Данные `{ controller = Products33, action = Edit, id = 17 }`маршрута .</span><span class="sxs-lookup"><span data-stu-id="02433-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="02433-234">Это типичная схема для контроллеров MVC:</span><span class="sxs-lookup"><span data-stu-id="02433-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="02433-235">`Edit(int)`отображает форму для отправления продукта.</span><span class="sxs-lookup"><span data-stu-id="02433-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="02433-236">`Edit(int, Product)`обрабатывает размещенную форму.</span><span class="sxs-lookup"><span data-stu-id="02433-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="02433-237">Для решения правильного маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-237">To resolve the correct route:</span></span>

* <span data-ttu-id="02433-238">`Edit(int, Product)`выбирается, когда запрос является `POST`HTTP.</span><span class="sxs-lookup"><span data-stu-id="02433-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="02433-239">`Edit(int)`выбирается, когда [глагол HTTP](#verb) является чем-то еще.</span><span class="sxs-lookup"><span data-stu-id="02433-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="02433-240">`Edit(int)`как правило, `GET`называется через .</span><span class="sxs-lookup"><span data-stu-id="02433-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="02433-241">В <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`, предоставляется для разгрома, так что он может выбрать на основе метода HTTP запроса.</span><span class="sxs-lookup"><span data-stu-id="02433-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="02433-242">Делает `HttpPostAttribute` `Edit(int, Product)` лучше матч, `Edit(int)`чем .</span><span class="sxs-lookup"><span data-stu-id="02433-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="02433-243">Важно понимать роль таких атрибутов, `HttpPostAttribute`как.</span><span class="sxs-lookup"><span data-stu-id="02433-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="02433-244">Аналогичные атрибуты определены для других [глаголов HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="02433-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="02433-245">В [обычной области,](#cr)обычнодля действия использовать одно и то же имя действия, когда они являются частью формы шоу, отправить рабочий процесс формы.</span><span class="sxs-lookup"><span data-stu-id="02433-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="02433-246">Например, [см.](xref:tutorials/first-mvc-app/controller-methods-views#get-post)</span><span class="sxs-lookup"><span data-stu-id="02433-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="02433-247">Если в этой области не может быть <xref:System.Reflection.AmbiguousMatchException> выбранный лучший кандидат, он будет брошен, перечислив несколько совпадающих конечных точек.</span><span class="sxs-lookup"><span data-stu-id="02433-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="02433-248">Обычные названия маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-248">Conventional route names</span></span>

<span data-ttu-id="02433-249">Строки `"blog"` и `"default"` в следующих примерах являются обычными названиями маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="02433-250">Названия маршрутов придают маршруту логическое название.</span><span class="sxs-lookup"><span data-stu-id="02433-250">The route names give the route a logical name.</span></span> <span data-ttu-id="02433-251">Названный маршрут можно использовать для генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="02433-252">Использование названного маршрута упрощает создание URL-адреса, когда упорядочение маршрутов может усложнить генерацию URL.</span><span class="sxs-lookup"><span data-stu-id="02433-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="02433-253">Названия маршрутов должны быть уникальными широкими приложениями.</span><span class="sxs-lookup"><span data-stu-id="02433-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="02433-254">Названия маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-254">Route names:</span></span>

* <span data-ttu-id="02433-255">Не повлияет на сопоставление URL или обработку запросов.</span><span class="sxs-lookup"><span data-stu-id="02433-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="02433-256">Используются только для генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-256">Are used only for URL generation.</span></span>

<span data-ttu-id="02433-257">Концепция названия маршрута представлена в маршрутизации как [IEndpointNameMetadata.](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)</span><span class="sxs-lookup"><span data-stu-id="02433-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="02433-258">Имя **маршрута** терминов и **имя конечных точек:**</span><span class="sxs-lookup"><span data-stu-id="02433-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="02433-259">Взаимозаменяемы.</span><span class="sxs-lookup"><span data-stu-id="02433-259">Are interchangeable.</span></span>
* <span data-ttu-id="02433-260">Какой из них используется в документации и коде, зависит от описанного API.</span><span class="sxs-lookup"><span data-stu-id="02433-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="02433-261">Аattributeная routing для АПЫ REST</span><span class="sxs-lookup"><span data-stu-id="02433-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="02433-262">REST AIS должны использовать функционирование атрибутов для моделирования функциональности приложения как набора ресурсов, в которых операции представлены [глаголами HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="02433-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="02433-263">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="02433-264">Следующий `StartUp.Configure` код характерен для REST API и используется в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02433-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="02433-265">В предыдущем коде, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` называется внутри, чтобы карта атрибута маршрутизаторов контроллеров.</span><span class="sxs-lookup"><span data-stu-id="02433-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="02433-266">Рассмотрим следующий пример:</span><span class="sxs-lookup"><span data-stu-id="02433-266">In the following example:</span></span>

* <span data-ttu-id="02433-267">Используется `Configure` предыдущий метод.</span><span class="sxs-lookup"><span data-stu-id="02433-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="02433-268">`HomeController`соответствует набору URL-адресов, похожих `{controller=Home}/{action=Index}/{id?}` на то, что соответствует обычному маршруту по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="02433-269">Действие `HomeController.Index` запущено для любого из `/` `/Home`путей `/Home/Index`URL, или `/Home/Index/3`.</span><span class="sxs-lookup"><span data-stu-id="02433-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="02433-270">В этом примере подчеркивается ключевая разница в программировании между направлением атрибутов и [обычной реуктором.](#cr)</span><span class="sxs-lookup"><span data-stu-id="02433-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="02433-271">Маршрутизации атрибутов требует большего ввода для указания маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="02433-272">Обычный маршрут по умолчанию обрабатывает маршруты более лаконично.</span><span class="sxs-lookup"><span data-stu-id="02433-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="02433-273">Однако маршрутирование атрибутов позволяет и требует точного контроля, какие шаблоны маршрутов применяются к каждому [действию.](#action)</span><span class="sxs-lookup"><span data-stu-id="02433-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="02433-274">При разгроме атрибутов имя контроллера и имена действий не играют **никакой** роли, в которой совпадает действие.</span><span class="sxs-lookup"><span data-stu-id="02433-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="02433-275">Следующий пример совпадает с теми же URL-адресами, что и предыдущий пример:</span><span class="sxs-lookup"><span data-stu-id="02433-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="02433-276">Следующий код использует замену `action` `controller`маркеров для и:</span><span class="sxs-lookup"><span data-stu-id="02433-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="02433-277">Следующий код `[Route("[controller]/[action]")]` применяется к контроллеру:</span><span class="sxs-lookup"><span data-stu-id="02433-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="02433-278">В предыдущем коде `Index` шаблоны метода `/` должны `~/` быть готовы или к шаблонам маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="02433-279">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="02433-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="02433-280">Просмотрите [приоритет шаблона Route](xref:fundamentals/routing#rtp) для получения информации о выборе шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="02433-281">Зарезервированные имена маршрутизации</span><span class="sxs-lookup"><span data-stu-id="02433-281">Reserved routing names</span></span>

<span data-ttu-id="02433-282">Следующие ключевые слова являются зарезервированными именами параметров маршрута при использовании контроллеров или страниц бритвы:</span><span class="sxs-lookup"><span data-stu-id="02433-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="02433-283">Использование `page` в качестве параметра маршрута с маршрутизанием атрибутов является обычной ошибкой.</span><span class="sxs-lookup"><span data-stu-id="02433-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="02433-284">Это приводит к непоследовательному и запутанному поведению с генерацией URL.</span><span class="sxs-lookup"><span data-stu-id="02433-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="02433-285">Специальные имена параметров используются поколением URL для определения того, относится ли операция генерации URL к странице Razor или к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="02433-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="02433-286">Шаблоны глаголов HTTP</span><span class="sxs-lookup"><span data-stu-id="02433-286">HTTP verb templates</span></span>

<span data-ttu-id="02433-287">ASP.NET Core имеет следующие шаблоны глагола HTTP:</span><span class="sxs-lookup"><span data-stu-id="02433-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="02433-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="02433-289">[(HttpPost)](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="02433-290">[(Httpput)](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="02433-291">[(HttpDelete)](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="02433-292">[(Httphead)](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="02433-293">[(Httppatch)](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="02433-294">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-294">Route templates</span></span>

<span data-ttu-id="02433-295">ASP.NET Core имеет следующие шаблоны маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="02433-296">Все [шаблоны глагола HTTP](#verb) являются шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="02433-297">[(Маршрут)](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="02433-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="02433-298">Аattributeная реуктора с атрибутами глагола Http</span><span class="sxs-lookup"><span data-stu-id="02433-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="02433-299">Рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="02433-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="02433-300">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="02433-300">In the preceding code:</span></span>

* <span data-ttu-id="02433-301">Каждое действие `[HttpGet]` содержит атрибут, который ограничивает соответствие только запросам HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="02433-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="02433-302">Действие `GetProduct` включает `"{id}"` в себя `id` шаблон, поэтому `"api/[controller]"` присваивается к шаблону на контроллере.</span><span class="sxs-lookup"><span data-stu-id="02433-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="02433-303">Шаблон методов `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="02433-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="02433-304">Поэтому это действие только соответствует GET `/api/test2/xyz``/api/test2/123`запросы на форму,`/api/test2/{any string}`и т.д.</span><span class="sxs-lookup"><span data-stu-id="02433-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="02433-305">Действие `GetIntProduct` содержит `"int/{id:int}")` шаблон.</span><span class="sxs-lookup"><span data-stu-id="02433-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="02433-306">Часть `:int` шаблона ограничивает значения `id` маршрута строками, которые могут быть преобразованы в ряды.</span><span class="sxs-lookup"><span data-stu-id="02433-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="02433-307">Запрос GET `/api/test2/int/abc`на:</span><span class="sxs-lookup"><span data-stu-id="02433-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="02433-308">Не совпадает с этим действием.</span><span class="sxs-lookup"><span data-stu-id="02433-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="02433-309">Возвращает ошибку [404 Не найденной.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)</span><span class="sxs-lookup"><span data-stu-id="02433-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="02433-310">Действие `GetInt2Product` содержится `{id}` в шаблоне, но `id` не ограничивает значения, которые могут быть преобразованы в ряд.</span><span class="sxs-lookup"><span data-stu-id="02433-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="02433-311">Запрос GET `/api/test2/int2/abc`на:</span><span class="sxs-lookup"><span data-stu-id="02433-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="02433-312">Соответствует этому маршруту.</span><span class="sxs-lookup"><span data-stu-id="02433-312">Matches this route.</span></span>
  * <span data-ttu-id="02433-313">Привязка модели `abc` не преобразуется в несколько.</span><span class="sxs-lookup"><span data-stu-id="02433-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="02433-314">Параметр `id` метода является неистежем.</span><span class="sxs-lookup"><span data-stu-id="02433-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="02433-315">Возвращает [400 Bad Request,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) потому`abc` что связывание модели не удалось преобразовать в ряд.</span><span class="sxs-lookup"><span data-stu-id="02433-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="02433-316">В реукторе <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> атрибутов <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>можно <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>использовать <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>такие атрибуты, как и .</span><span class="sxs-lookup"><span data-stu-id="02433-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="02433-317">Все атрибуты [глагола HTTP](#verb) принимают шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="02433-318">В следующем примере показаны два действия, которые соответствуют тому же шаблону маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="02433-319">Использование пути `/products3`URL:</span><span class="sxs-lookup"><span data-stu-id="02433-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="02433-320">Действие `MyProductsController.ListProducts` выполняется при `GET` [глаголе HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="02433-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="02433-321">Действие `MyProductsController.CreateProduct` выполняется при `POST` [глаголе HTTP.](#verb)</span><span class="sxs-lookup"><span data-stu-id="02433-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="02433-322">При создании REST API редко требуется использовать `[Route(...)]` метод действия, поскольку действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="02433-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="02433-323">Лучше использовать более конкретный [атрибут глагола HTTP,](#verb) чтобы быть точным о том, что поддерживает ваш API.</span><span class="sxs-lookup"><span data-stu-id="02433-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="02433-324">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="02433-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="02433-325">REST AIS должны использовать функционирование атрибутов для моделирования функциональности приложения как набора ресурсов, в которых операции представлены глаголами HTTP.</span><span class="sxs-lookup"><span data-stu-id="02433-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="02433-326">Это означает, что многие операции, например, GET и POST на одном логическом ресурсе используют один и тот же URL.</span><span class="sxs-lookup"><span data-stu-id="02433-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="02433-327">Маршрутизация с помощью атрибутов обеспечивает необходимый уровень контроля, позволяющий тщательно разработать схему общедоступных конечных точек API-интерфейса.</span><span class="sxs-lookup"><span data-stu-id="02433-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="02433-328">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="02433-329">В следующем примере `id` требуется как часть пути URL:</span><span class="sxs-lookup"><span data-stu-id="02433-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="02433-330">Действие: `Products2ApiController.GetProduct(int)`</span><span class="sxs-lookup"><span data-stu-id="02433-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="02433-331">Работает с URL-траекторией, как`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="02433-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="02433-332">Не работает с траекторией `/products2`URL.</span><span class="sxs-lookup"><span data-stu-id="02433-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="02433-333">Атрибут [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) позволяет выполнять действие для ограничения поддерживаемых типов содержимого запросов.</span><span class="sxs-lookup"><span data-stu-id="02433-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="02433-334">Для получения дополнительной [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes)информации см.</span><span class="sxs-lookup"><span data-stu-id="02433-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="02433-335">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="02433-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="02433-336">Для получения `[ApiController]`дополнительной информации о, см. [ApiController attribute](xref:web-api/index##apicontroller-attribute)</span><span class="sxs-lookup"><span data-stu-id="02433-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="02433-337">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="02433-337">Route name</span></span>

<span data-ttu-id="02433-338">В следующем коде определяется имя маршрута`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="02433-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="02433-339">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="02433-340">Названия маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-340">Route names:</span></span>

* <span data-ttu-id="02433-341">Не повлияет на поведение сопоставления URL-адреса с разгромом.</span><span class="sxs-lookup"><span data-stu-id="02433-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="02433-342">Используются только для генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-342">Are only used for URL generation.</span></span>

<span data-ttu-id="02433-343">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="02433-344">Сравните предыдущий код с обычным маршрутом `id` по умолчанию, который определяет параметр как необязательный ().`{id?}`</span><span class="sxs-lookup"><span data-stu-id="02433-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="02433-345">Возможность точно говоруна AIS имеет `/products` свои `/products/5` преимущества, такие как разрешение и отправка на различные действия.</span><span class="sxs-lookup"><span data-stu-id="02433-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="02433-346">Объединение маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-346">Combining attribute routes</span></span>

<span data-ttu-id="02433-347">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="02433-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="02433-348">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="02433-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="02433-349">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="02433-350">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="02433-350">In the preceding example:</span></span>

* <span data-ttu-id="02433-351">Путь `/products` URL может совпадать`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="02433-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="02433-352">Путь `/products/5` URL может `ProductsApi.GetProduct(int)`совпадать с.</span><span class="sxs-lookup"><span data-stu-id="02433-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="02433-353">Оба эти действия совпадают только с HTTP, `GET` поскольку они помечены атрибутом. `[HttpGet]`</span><span class="sxs-lookup"><span data-stu-id="02433-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="02433-354">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="02433-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="02433-355">Следующий пример соответствует набору путей URL, похожих на маршрут по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="02433-356">В следующей `[Route]` таблице объясняется атрибуты в предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="02433-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="02433-357">Атрибут</span><span class="sxs-lookup"><span data-stu-id="02433-357">Attribute</span></span>               | <span data-ttu-id="02433-358">Комбинирует сястом`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="02433-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="02433-359">Определение шаблона маршрута</span><span class="sxs-lookup"><span data-stu-id="02433-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="02433-360">Да</span><span class="sxs-lookup"><span data-stu-id="02433-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="02433-361">Да</span><span class="sxs-lookup"><span data-stu-id="02433-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="02433-362">**Нет**</span><span class="sxs-lookup"><span data-stu-id="02433-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="02433-363">Да</span><span class="sxs-lookup"><span data-stu-id="02433-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="02433-364">Порядок маршрута атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-364">Attribute route order</span></span>

<span data-ttu-id="02433-365">Routing строит дерево и сопоставляет все конечные точки одновременно:</span><span class="sxs-lookup"><span data-stu-id="02433-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="02433-366">Записи маршрута ведут себя так, как будто размещены в идеальном порядке.</span><span class="sxs-lookup"><span data-stu-id="02433-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="02433-367">Наиболее конкретные маршруты имеют возможность выполнить до более общих маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="02433-368">Например, маршрут `blog/search/{topic}` атрибута более специфичен, `blog/{*article}`чем маршрут атрибута, как.</span><span class="sxs-lookup"><span data-stu-id="02433-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="02433-369">Маршрут `blog/search/{topic}` имеет более высокий приоритет, по умолчанию, потому что он более специфичен.</span><span class="sxs-lookup"><span data-stu-id="02433-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="02433-370">Используя [обычную маршрутизм,](#cr)разработчик отвечает за размещение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="02433-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="02433-371">Маршруты атрибутов могут настроить <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> заказ с помощью свойства.</span><span class="sxs-lookup"><span data-stu-id="02433-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="02433-372">Все [атрибуты маршрута, предусмотренные](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) рамками, включают в себя. `Order`</span><span class="sxs-lookup"><span data-stu-id="02433-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="02433-373">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="02433-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="02433-374">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="02433-374">The default order is `0`.</span></span> <span data-ttu-id="02433-375">Настройка маршрута `Order = -1` с использованием трасс перед маршрутами, которые не устанавливают заказ.</span><span class="sxs-lookup"><span data-stu-id="02433-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="02433-376">Настройка маршрута `Order = 1` с использованием трасс после заказа маршрута по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="02433-377">**Избегайте** в `Order`зависимости от .</span><span class="sxs-lookup"><span data-stu-id="02433-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="02433-378">Если пространство URL-пространства приложения требует четкого значения заказа для правильного маршрута, то это, скорее всего, сбивает с толку и клиентов.</span><span class="sxs-lookup"><span data-stu-id="02433-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="02433-379">В целом маршрутатрибут выбирает правильный маршрут с сопоставлением URL.</span><span class="sxs-lookup"><span data-stu-id="02433-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="02433-380">Если порядок по умолчанию, используемый для генерации URL, не работает, использование `Order` имени маршрута в качестве переопределения обычно проще, чем применение свойства.</span><span class="sxs-lookup"><span data-stu-id="02433-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="02433-381">Рассмотрим следующие два контроллера, `/home`которые определяют соответствие маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="02433-382">Запрос `/home` с предыдущим кодом бросает исключение, аналогичное следующему:</span><span class="sxs-lookup"><span data-stu-id="02433-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="02433-383">Добавление `Order` к одному из атрибутов маршрута разрешает двусмысленность:</span><span class="sxs-lookup"><span data-stu-id="02433-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="02433-384">С предыдущим кодом `/home` `HomeController.Index` запускаетконечную точку.</span><span class="sxs-lookup"><span data-stu-id="02433-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="02433-385">Чтобы добраться `MyDemoController.MyIndex`до `/home/MyIndex`запроса .</span><span class="sxs-lookup"><span data-stu-id="02433-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="02433-386">**Примечание.**</span><span class="sxs-lookup"><span data-stu-id="02433-386">**Note**:</span></span>

* <span data-ttu-id="02433-387">Предыдущий код является примером или плохой конструкцией routing.</span><span class="sxs-lookup"><span data-stu-id="02433-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="02433-388">Он был использован `Order` для иллюстрации свойства.</span><span class="sxs-lookup"><span data-stu-id="02433-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="02433-389">Свойство `Order` только решает двусмысленность, этот шаблон не может быть сопоставлен.</span><span class="sxs-lookup"><span data-stu-id="02433-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="02433-390">Было бы лучше удалить `[Route("Home")]` шаблон.</span><span class="sxs-lookup"><span data-stu-id="02433-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="02433-391">[См. маршрут Razor Pages и конвенций приложений: Заказ маршрута](xref:razor-pages/razor-pages-conventions#route-order) для получения информации о заказе маршрута с помощью страниц Razor.</span><span class="sxs-lookup"><span data-stu-id="02433-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="02433-392">В некоторых случаях ошибка HTTP 500 возвращается с неоднозначными маршрутами.</span><span class="sxs-lookup"><span data-stu-id="02433-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="02433-393">Используйте [журнал,](xref:fundamentals/logging/index) чтобы увидеть, какие конечные `AmbiguousMatchException`точки вызвали .</span><span class="sxs-lookup"><span data-stu-id="02433-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="02433-394">Замена токенов в шаблонах маршрутов «контроллер», «действие», «область»</span><span class="sxs-lookup"><span data-stu-id="02433-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="02433-395">Для удобства маршруты атрибутов поддерживают замену маркеров для зарезервированных параметров маршрута, прилагая токен в один из следующих:</span><span class="sxs-lookup"><span data-stu-id="02433-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="02433-396">Квадратные скобки:`[]`</span><span class="sxs-lookup"><span data-stu-id="02433-396">Square braces: `[]`</span></span>
* <span data-ttu-id="02433-397">Кудрявые скобки:`{}`</span><span class="sxs-lookup"><span data-stu-id="02433-397">Curly braces: `{}`</span></span>

<span data-ttu-id="02433-398">Токены `[action]`и `[area]` `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут:</span><span class="sxs-lookup"><span data-stu-id="02433-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="02433-399">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="02433-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="02433-400">Матчи`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="02433-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="02433-401">Матчи`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="02433-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="02433-402">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="02433-403">Предыдущий пример ведет себя так же, как и следующий код:</span><span class="sxs-lookup"><span data-stu-id="02433-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="02433-404">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="02433-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="02433-405">Это мощный в сочетании с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="02433-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="02433-406">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="02433-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`генерирует уникальное название маршрута для каждого действия:</span><span class="sxs-lookup"><span data-stu-id="02433-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="02433-408">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="02433-409"> создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="02433-410">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="02433-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="02433-411">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="02433-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="02433-412">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="02433-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="02433-413">Преобразователь параметров реализует <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="02433-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="02433-414">Например, пользовательский `SlugifyParameterTransformer` трансформатор `SubscriptionManagement` параметра `subscription-management`изменяет значение маршрута на:</span><span class="sxs-lookup"><span data-stu-id="02433-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="02433-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="02433-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="02433-416">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="02433-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="02433-417">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="02433-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="02433-418">Предыдущий `ListAll` метод `/subscription-management/list-all`совпадает.</span><span class="sxs-lookup"><span data-stu-id="02433-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="02433-419">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02433-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="02433-420">См [веб-документов MDN на Slug](https://developer.mozilla.org/docs/Glossary/Slug) для определения Slug.</span><span class="sxs-lookup"><span data-stu-id="02433-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="02433-421">Несколько маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-421">Multiple attribute routes</span></span>

<span data-ttu-id="02433-422">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="02433-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="02433-423">Наиболее распространенный случай использования этой возможности — имитация поведения маршрута по умолчанию на основе соглашения, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02433-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="02433-424">Размещение нескольких атрибутов маршрута на контроллере означает, что каждый из них сочетается с каждым из атрибутов маршрута на методах действия:</span><span class="sxs-lookup"><span data-stu-id="02433-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="02433-425">Все ограничения маршрута [глагола HTTP](#verb) реализуем. `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="02433-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="02433-426">При размещении нескольких <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> атрибутов маршрута, которые реализуются в действии:</span><span class="sxs-lookup"><span data-stu-id="02433-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="02433-427">Каждое ограничение действия сочетается с шаблоном маршрута, применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="02433-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="02433-428">Использование нескольких маршрутов на действиях может показаться полезным и мощным, лучше сохранить пространство URL вашего приложения основным и четко определены.</span><span class="sxs-lookup"><span data-stu-id="02433-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="02433-429">Используйте несколько маршрутов на **действиях только** там, где это необходимо, например, для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="02433-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="02433-430">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="02433-431">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="02433-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="02433-432">В предыдущем коде `[HttpPost("product/{id:int}")]` применяется ограничение маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="02433-433">Действие `ProductsController.ShowProduct` сопоставляется только по таким `/product/3`url-адресам, как.</span><span class="sxs-lookup"><span data-stu-id="02433-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="02433-434">Часть шаблона `{id:int}` маршрута ограничивает этот сегмент только нераспределенным.</span><span class="sxs-lookup"><span data-stu-id="02433-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="02433-435">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="02433-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="02433-436">Пользовательские атрибуты маршрута с помощью IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="02433-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="02433-437">Все [атрибуты маршрута реализуем.](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider></span><span class="sxs-lookup"><span data-stu-id="02433-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="02433-438">Время выполнения ASP.NET core:</span><span class="sxs-lookup"><span data-stu-id="02433-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="02433-439">Ищет атрибуты на классах контроллеров и методах действий при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="02433-440">Использует атрибуты, `IRouteTemplateProvider` которые реализуем для построения исходного набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="02433-441">Реализация `IRouteTemplateProvider` для определения пользовательских атрибутов маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="02433-442">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="02433-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="02433-443">Предыдущий `Get` метод `Order = 2, Template = api/MyTestApi`возвращается.</span><span class="sxs-lookup"><span data-stu-id="02433-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="02433-444">Используйте модель приложения для настройки маршрутов атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="02433-445">Модель приложения:</span><span class="sxs-lookup"><span data-stu-id="02433-445">The application model:</span></span>

* <span data-ttu-id="02433-446">Является объектом модели, созданной при запуске.</span><span class="sxs-lookup"><span data-stu-id="02433-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="02433-447">Содержит все метаданные, используемые ASP.NET Core для маршрутизаира и выполнения действий в приложении.</span><span class="sxs-lookup"><span data-stu-id="02433-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="02433-448">Модель приложения включает в себя все данные, собранные из атрибутов маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="02433-449">Данные из атрибутов маршрута `IRouteTemplateProvider` обеспечиваются реализацией.</span><span class="sxs-lookup"><span data-stu-id="02433-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="02433-450">Конвенций:</span><span class="sxs-lookup"><span data-stu-id="02433-450">Conventions:</span></span>

* <span data-ttu-id="02433-451">Может быть написана для изменения модели приложения, чтобы настроить, как ведет себя routing.</span><span class="sxs-lookup"><span data-stu-id="02433-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="02433-452">Прочитаны при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-452">Are read at app startup.</span></span>

<span data-ttu-id="02433-453">В этом разделе показан основной пример настройки трассировки с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="02433-454">Следующий код делает маршруты примерно в соответствии со структурой папок проекта.</span><span class="sxs-lookup"><span data-stu-id="02433-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="02433-455">Следующий код предотвращает `namespace` применение конвенции к контроллерам, которые являются атрибутами:</span><span class="sxs-lookup"><span data-stu-id="02433-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="02433-456">Например, следующий контроллер не `NamespaceRoutingConvention`использует:</span><span class="sxs-lookup"><span data-stu-id="02433-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="02433-457">Метод `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="02433-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="02433-458">Ничего не делает, если контроллер приписывается маршрутистируем.</span><span class="sxs-lookup"><span data-stu-id="02433-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="02433-459">Устанавливает шаблон контроллеров на `namespace`основе, `namespace` с базой удалены.</span><span class="sxs-lookup"><span data-stu-id="02433-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="02433-460">Можно `NamespaceRoutingConvention` наносить `Startup.ConfigureServices`в:</span><span class="sxs-lookup"><span data-stu-id="02433-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="02433-461">Например, рассмотрим следующий контроллер:</span><span class="sxs-lookup"><span data-stu-id="02433-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="02433-462">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="02433-462">In the preceding code:</span></span>

* <span data-ttu-id="02433-463">База `namespace` `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="02433-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="02433-464">Полное имя предыдущего `My.Application.Admin.Controllers.UsersController`контроллера .</span><span class="sxs-lookup"><span data-stu-id="02433-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="02433-465">Устанавливает `NamespaceRoutingConvention` шаблон контроллеров `Admin/Controllers/Users/[action]/{id?`для .</span><span class="sxs-lookup"><span data-stu-id="02433-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="02433-466">Также `NamespaceRoutingConvention` может быть применен в качестве атрибута на контроллере:</span><span class="sxs-lookup"><span data-stu-id="02433-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="02433-467">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="02433-468">ASP.NET приложения Core могут смешивать использование обычной реукторов и атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="02433-469">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="02433-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="02433-470">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="02433-471">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="02433-472">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="02433-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="02433-473">**Любой** атрибут маршрута на контроллере делает **все** действия в атрибуте контроллера маршрутизированными.</span><span class="sxs-lookup"><span data-stu-id="02433-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="02433-474">Атрибутная реуктора и обычная реуктора использует один и тот же двигатель.</span><span class="sxs-lookup"><span data-stu-id="02433-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="02433-475">Поколение URL и значения окружающей среды</span><span class="sxs-lookup"><span data-stu-id="02433-475">URL Generation and ambient values</span></span>

<span data-ttu-id="02433-476">Приложения могут использовать функции генерации URL-адресов для создания url-адресов для действий.</span><span class="sxs-lookup"><span data-stu-id="02433-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="02433-477">Создание URL-адресов устраняет URL-адреса хардкодирования, делая код более надежным и обслуживаемым.</span><span class="sxs-lookup"><span data-stu-id="02433-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="02433-478">В этом разделе основное внимание уделяется функциям генерации URL, предоставляемым MVC, и охватывает только основы работы генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="02433-479">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="02433-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="02433-480">Интерфейс <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> является основным элементом инфраструктуры между MVC и routing для генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="02433-481">Экземпляр `IUrlHelper` доступен через `Url` свойство в контроллерах, представлениях и компонентах представления.</span><span class="sxs-lookup"><span data-stu-id="02433-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="02433-482">В следующем примере `IUrlHelper` интерфейс используется `Controller.Url` через свойство для создания URL-адреса для другого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="02433-483">Если приложение использует обычный маршрут по умолчанию, значение `url` переменной — строка `/UrlGeneration/Destination`пути URL.</span><span class="sxs-lookup"><span data-stu-id="02433-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="02433-484">Этот путь URL создается путем маршрутизации путем объединения:</span><span class="sxs-lookup"><span data-stu-id="02433-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="02433-485">Значения маршрута из текущего запроса, которые называются **окружающими значениями.**</span><span class="sxs-lookup"><span data-stu-id="02433-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="02433-486">Значения, передаваемые `Url.Action` и заменяющие эти значения в шаблон маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="02433-487">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="02433-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="02433-488">Параметр маршрута, не имеюв значения, может:</span><span class="sxs-lookup"><span data-stu-id="02433-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="02433-489">Используйте значение по умолчанию, если оно имеется.</span><span class="sxs-lookup"><span data-stu-id="02433-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="02433-490">Пропустите, если это необязательно.</span><span class="sxs-lookup"><span data-stu-id="02433-490">Be skipped if it's optional.</span></span> <span data-ttu-id="02433-491">Например, `id` из шаблона `{controller}/{action}/{id?}`маршрута .</span><span class="sxs-lookup"><span data-stu-id="02433-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="02433-492">Генерация URL-адреса выходит из строя, если какой-либо необходимый параметр маршрута не имеет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="02433-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="02433-493">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="02433-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="02433-494">Предыдущий пример `Url.Action` предполагает [обычную разгром.](#cr)</span><span class="sxs-lookup"><span data-stu-id="02433-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="02433-495">Генерация URL работает аналогично с [помощью рекрутинга атрибутов,](#ar)хотя понятия отличаются.</span><span class="sxs-lookup"><span data-stu-id="02433-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="02433-496">С обычной реуктором:</span><span class="sxs-lookup"><span data-stu-id="02433-496">With conventional routing:</span></span>

* <span data-ttu-id="02433-497">Значения маршрута используются для расширения шаблона.</span><span class="sxs-lookup"><span data-stu-id="02433-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="02433-498">Значения маршрута для `controller` `action` этого шаблона и обычно отображаются.</span><span class="sxs-lookup"><span data-stu-id="02433-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="02433-499">Это работает, потому что URL-адреса, соответствующие реаутингу, придерживаются конвенции.</span><span class="sxs-lookup"><span data-stu-id="02433-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="02433-500">В следующем примере используется реукторатирование атрибутов:</span><span class="sxs-lookup"><span data-stu-id="02433-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="02433-501">Действие `Source` в предыдущем коде `custom/url/to/destination`генерирует .</span><span class="sxs-lookup"><span data-stu-id="02433-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="02433-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>был добавлен в ASP.NET Core 3.0 в качестве альтернативы `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="02433-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="02433-503">`LinkGenerator`предлагает аналогичную, но более гибкую функциональность.</span><span class="sxs-lookup"><span data-stu-id="02433-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="02433-504">Каждый `IUrlHelper` метод на имеет соответствующее `LinkGenerator` семейство методов, а также.</span><span class="sxs-lookup"><span data-stu-id="02433-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="02433-505">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="02433-505">Generating URLs by action name</span></span>

<span data-ttu-id="02433-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), и все связанные с этим перегрузки все предназначены для создания целевой точки конечных путем указания имени контроллера и имя действия.</span><span class="sxs-lookup"><span data-stu-id="02433-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="02433-507">При `Url.Action`использовании текущие значения `controller` `action` маршрута и предусмотрены временем выполнения:</span><span class="sxs-lookup"><span data-stu-id="02433-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="02433-508">Ценность `controller` и `action` являются частью как [окружающих ценностей,](#ambient) так и ценностей.</span><span class="sxs-lookup"><span data-stu-id="02433-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="02433-509">Метод `Url.Action` всегда использует текущие `action` значения `controller` и генерирует путь URL, который направляет к текущему действию.</span><span class="sxs-lookup"><span data-stu-id="02433-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="02433-510">Попытка реуктора использовать значения в окружающих значениях для заполнения информации, которая не была предоставлена при генерации URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="02433-511">Рассмотрим маршрут, как `{a}/{b}/{c}/{d}` с `{ a = Alice, b = Bob, c = Carol, d = David }`окружающими значениями:</span><span class="sxs-lookup"><span data-stu-id="02433-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="02433-512">В routing достаточно информации для создания URL-адреса без каких-либо дополнительных значений.</span><span class="sxs-lookup"><span data-stu-id="02433-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="02433-513">Маршрутирование имеет достаточно информации, потому что все параметры маршрута имеют значение.</span><span class="sxs-lookup"><span data-stu-id="02433-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="02433-514">Если добавлено значение: `{ d = Donovan }`</span><span class="sxs-lookup"><span data-stu-id="02433-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="02433-515">Значение `{ d = David }` игнорируется.</span><span class="sxs-lookup"><span data-stu-id="02433-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="02433-516">Сгенерированный `Alice/Bob/Carol/Donovan`путь URL .</span><span class="sxs-lookup"><span data-stu-id="02433-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="02433-517">**Предупреждение**: Пути URL являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="02433-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="02433-518">В предыдущем примере, `{ c = Cheryl }` если добавлено значение:</span><span class="sxs-lookup"><span data-stu-id="02433-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="02433-519">Оба значения `{ c = Carol, d = David }` игнорируются.</span><span class="sxs-lookup"><span data-stu-id="02433-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="02433-520">Больше нет значения для `d` и генерации URL сбой.</span><span class="sxs-lookup"><span data-stu-id="02433-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="02433-521">Нужные значения `c` и `d` должны быть указаны для создания URL-</span><span class="sxs-lookup"><span data-stu-id="02433-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="02433-522">Возможно, вы обнаружите, что `{controller}/{action}/{id?}`поразит эту проблему маршрутом по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="02433-523">Эта проблема является редким `Url.Action` на практике, потому `controller` `action` что всегда четко определяет и значение.</span><span class="sxs-lookup"><span data-stu-id="02433-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="02433-524">Несколько перегрузок [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) принимают объект значений маршрута, чтобы обеспечить значения для параметров маршрута, кроме `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="02433-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="02433-525">Объект значений маршрута часто `id`используется с .</span><span class="sxs-lookup"><span data-stu-id="02433-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="02433-526">Например, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="02433-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="02433-527">Объект значения маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-527">The route values object:</span></span>

* <span data-ttu-id="02433-528">По конвенции, как правило, объект анонимного типа.</span><span class="sxs-lookup"><span data-stu-id="02433-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="02433-529">Может быть `IDictionary<>` или [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="02433-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="02433-530">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="02433-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="02433-531">Предыдущий код `/Products/Buy/17?color=red`генерирует .</span><span class="sxs-lookup"><span data-stu-id="02433-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="02433-532">Следующий код генерирует абсолютный URL:</span><span class="sxs-lookup"><span data-stu-id="02433-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="02433-533">Чтобы создать абсолютный URL, используйте один из следующих:</span><span class="sxs-lookup"><span data-stu-id="02433-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="02433-534">Перегрузка, которая `protocol`принимает .</span><span class="sxs-lookup"><span data-stu-id="02433-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="02433-535">Например, предыдущий код.</span><span class="sxs-lookup"><span data-stu-id="02433-535">For example, the preceding code.</span></span>
* <span data-ttu-id="02433-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), который генерирует абсолютные URIs по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="02433-537">Создание URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="02433-537">Generate URLs by route</span></span>

<span data-ttu-id="02433-538">Предыдущий код продемонстрировал генерацию URL-адреса, передавая в контроллер и имя действия.</span><span class="sxs-lookup"><span data-stu-id="02433-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="02433-539">`IUrlHelper`также предоставляет [url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) семейство методов.</span><span class="sxs-lookup"><span data-stu-id="02433-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="02433-540">Эти методы аналогичны [Url.Action,](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)но они не копируют `action` текущие значения и `controller` значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="02433-541">Наиболее распространенное `Url.RouteUrl`использование:</span><span class="sxs-lookup"><span data-stu-id="02433-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="02433-542">Укажите имя маршрута для создания URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="02433-543">Как правило, не указывается контроллер или имя действия.</span><span class="sxs-lookup"><span data-stu-id="02433-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="02433-544">Следующий файл Razor генерирует HTML `Destination_Route`ссылку на:</span><span class="sxs-lookup"><span data-stu-id="02433-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="02433-545">Создание URL-адресов в HTML и Razor</span><span class="sxs-lookup"><span data-stu-id="02433-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="02433-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>предоставляет <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> методы [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) и [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) для генерации `<form>` и `<a>` элементов соответственно.</span><span class="sxs-lookup"><span data-stu-id="02433-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="02433-547">Эти методы используют метод [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) для создания URL-адреса и принимают аналогичные аргументы.</span><span class="sxs-lookup"><span data-stu-id="02433-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="02433-548">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="02433-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="02433-549">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="02433-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="02433-550">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="02433-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="02433-551">Для получения дополнительной информации ознакомьтесь с [формами Tag Helpers в формах.](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="02433-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="02433-552">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="02433-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="02433-553">Генерация URL в результатах действий</span><span class="sxs-lookup"><span data-stu-id="02433-553">URL generation in Action Results</span></span>

<span data-ttu-id="02433-554">Предыдущие примеры `IUrlHelper` показали использование в контроллере.</span><span class="sxs-lookup"><span data-stu-id="02433-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="02433-555">Наиболее распространенным использованием контроллера является создание URL-адреса в результате действия.</span><span class="sxs-lookup"><span data-stu-id="02433-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="02433-556">Базовые классы <xref:Microsoft.AspNetCore.Mvc.ControllerBase> и <xref:Microsoft.AspNetCore.Mvc.Controller> предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="02433-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="02433-557">Одним из типичных способов является перенаправление после приема пользовательского ввода:</span><span class="sxs-lookup"><span data-stu-id="02433-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="02433-558">Действия результаты заводских <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> методов, таких как и <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`следовать аналогичной схеме методы на .</span><span class="sxs-lookup"><span data-stu-id="02433-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="02433-559">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="02433-560">[Обычная маршрутизирующая система](#cr) может использовать особый вид определения маршрута, называемый [выделенным обычным маршрутом.](#dcr)</span><span class="sxs-lookup"><span data-stu-id="02433-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="02433-561">В следующем примере названный `blog` маршрут представляет собой специальный обычный маршрут:</span><span class="sxs-lookup"><span data-stu-id="02433-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="02433-562">Используя предыдущие определения маршрута, `Url.Action("Index", "Home")` генерирует путь `/` URL `default` с помощью маршрута, но почему?</span><span class="sxs-lookup"><span data-stu-id="02433-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="02433-563">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="02433-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="02433-564">[Выделенные обычные маршруты](#dcr) опираются на особое поведение значений по умолчанию, не имеющеющее соответствующего параметра маршрута, который не позволяет маршруту быть слишком [жадным](xref:fundamentals/routing#greedy) с генерацией URL.</span><span class="sxs-lookup"><span data-stu-id="02433-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="02433-565">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="02433-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="02433-566">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="02433-567">Генерация `blog` URL-адреса с `{ controller = Home, action = Index }` помощью `{ controller = Blog, action = Article }`сбоев, поскольку значения не совпадают с значениями.</span><span class="sxs-lookup"><span data-stu-id="02433-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="02433-568">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="02433-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="02433-569">Области</span><span class="sxs-lookup"><span data-stu-id="02433-569">Areas</span></span>

<span data-ttu-id="02433-570">[Области](xref:mvc/controllers/areas) — это функция MVC, используемая для организации связанных функциональных возможностей в группу в виде отдельной:</span><span class="sxs-lookup"><span data-stu-id="02433-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="02433-571">Пространство имен для реаниматирования для действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="02433-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="02433-572">Структура Folder для представлений.</span><span class="sxs-lookup"><span data-stu-id="02433-572">Folder structure for views.</span></span>

<span data-ttu-id="02433-573">Использование областей позволяет приложению иметь несколько контроллеров с одинаковым именем, если у них есть разные области.</span><span class="sxs-lookup"><span data-stu-id="02433-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="02433-574">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="02433-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="02433-575">В этом разделе рассматриваются способы взаимодействия трассировки с областями.</span><span class="sxs-lookup"><span data-stu-id="02433-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="02433-576">Ознакомьтесь с [разделами Области](xref:mvc/controllers/areas) для получения подробной информации о том, как используются области с представлениями.</span><span class="sxs-lookup"><span data-stu-id="02433-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="02433-577">Следующий пример настраивает MVC для использования `area` обычного маршрута `area` `Blog`по умолчанию и маршрута для имени:</span><span class="sxs-lookup"><span data-stu-id="02433-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="02433-578">В предыдущем <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> коде, называется `"blog_route"`для создания .</span><span class="sxs-lookup"><span data-stu-id="02433-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="02433-579">Второй параметр, `"Blog"`это название области.</span><span class="sxs-lookup"><span data-stu-id="02433-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="02433-580">При сопоставлении `/Manage/Users/AddUser`пути `"blog_route"` URL, например, `{ area = Blog, controller = Users, action = AddUser }`маршрут генерирует значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="02433-581">Значение `area` маршрута производится значением `area`по умолчанию для.</span><span class="sxs-lookup"><span data-stu-id="02433-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="02433-582">`MapAreaControllerRoute` Маршрут, созданный, эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="02433-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="02433-583">Метод `MapAreaControllerRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="02433-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="02433-584">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="02433-585">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="02433-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="02433-586">В общем, маршруты с областями должны быть размещены раньше, поскольку они более специфичны, чем маршруты без области.</span><span class="sxs-lookup"><span data-stu-id="02433-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="02433-587">Используя предыдущий пример, значения `{ area = Blog, controller = Users, action = AddUser }` маршрута соответствуют следующим действиям:</span><span class="sxs-lookup"><span data-stu-id="02433-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="02433-588">Атрибут [«Область»](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) — это то, что означает контроллер как часть области.</span><span class="sxs-lookup"><span data-stu-id="02433-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="02433-589">Этот контроллер находится `Blog` в этом районе.</span><span class="sxs-lookup"><span data-stu-id="02433-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="02433-590">Контроллеры без `[Area]` атрибута не являются членами какой-либо области и **не** совпадают, когда значение `area` маршрута обеспечивается маршрутизатием.</span><span class="sxs-lookup"><span data-stu-id="02433-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="02433-591">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="02433-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="02433-592">Пространство имен каждого контроллера отображается здесь для полноты.</span><span class="sxs-lookup"><span data-stu-id="02433-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="02433-593">Если предыдущие контроллеры используют одно и то же пространство имен, будет сгенерирована ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="02433-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="02433-594">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="02433-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="02433-595">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="02433-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="02433-596">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="02433-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="02433-597">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="02433-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="02433-598">При выполнения действия внутри области значение `area` маршрута доступно в качестве [эмбиентного значения](#ambient) для маршрутики для использования в генерации URL.</span><span class="sxs-lookup"><span data-stu-id="02433-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="02433-599">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="02433-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="02433-600">Следующий код генерирует `/Zebra/Users/AddUser`URL:</span><span class="sxs-lookup"><span data-stu-id="02433-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="02433-601">Определение действий</span><span class="sxs-lookup"><span data-stu-id="02433-601">Action definition</span></span>

<span data-ttu-id="02433-602">Общедоступные методы на контроллере, за исключением методов с атрибутом [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) являются действиями.</span><span class="sxs-lookup"><span data-stu-id="02433-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="02433-603">Образец кода</span><span class="sxs-lookup"><span data-stu-id="02433-603">Sample code</span></span>

 * <span data-ttu-id="02433-604">Метод [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) включен в [загрузку образца](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) и используется для отображения информации о маршрутизании.</span><span class="sxs-lookup"><span data-stu-id="02433-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="02433-605">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="02433-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02433-606">В ASP.NET Core MVC используется [ПО промежуточного слоя](xref:fundamentals/middleware/index) маршрутизации для сопоставления URL-адресов входящих запросов с действиями.</span><span class="sxs-lookup"><span data-stu-id="02433-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="02433-607">Маршруты определяются в коде запуска или атрибутах.</span><span class="sxs-lookup"><span data-stu-id="02433-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="02433-608">Они описывают то, как пути URL-адресов должны сопоставляться с действиями.</span><span class="sxs-lookup"><span data-stu-id="02433-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="02433-609">С помощью маршрутов также формируются URL-адреса (для ссылок), отправляемые в ответах.</span><span class="sxs-lookup"><span data-stu-id="02433-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="02433-610">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="02433-611">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="02433-612">Дополнительные сведения см. в разделе [Смешанная маршрутизация](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="02433-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="02433-613">В этом документе описывается взаимодействие между MVC и маршрутизацией и использование возможностей маршрутизации в типичных приложениях MVC.</span><span class="sxs-lookup"><span data-stu-id="02433-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="02433-614">Подробные сведения о расширенной маршрутизации см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="02433-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="02433-615">Настройка ПО промежуточного слоя маршрутизации</span><span class="sxs-lookup"><span data-stu-id="02433-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="02433-616">Метод *Configure* содержит код, который выглядит примерно так:</span><span class="sxs-lookup"><span data-stu-id="02433-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="02433-617">В вызове `UseMvc` метод `MapRoute` используется для создания одного маршрута, который называется маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="02433-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="02433-618">В большинстве приложений MVC используется маршрут с шаблоном, сходным с маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="02433-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="02433-619">Шаблон маршрута `"{controller=Home}/{action=Index}/{id?}"` может сопоставлять путь URL-адреса, например `/Products/Details/5`, и извлекает значения маршрута `{ controller = Products, action = Details, id = 5 }` путем разбивки пути на лексемы.</span><span class="sxs-lookup"><span data-stu-id="02433-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="02433-620">MVC попытается найти контроллер с именем `ProductsController` и выполнить действие `Details`:</span><span class="sxs-lookup"><span data-stu-id="02433-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="02433-621">Обратите внимание на то, что в этом примере привязка модели будет использовать значение `id = 5`, чтобы присвоить параметру `id` значение `5` при вызове действия.</span><span class="sxs-lookup"><span data-stu-id="02433-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="02433-622">Дополнительные сведения см. в разделе [Привязка модели](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="02433-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="02433-623">Использование маршрута `default`:</span><span class="sxs-lookup"><span data-stu-id="02433-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="02433-624">Шаблон маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-624">The route template:</span></span>

* <span data-ttu-id="02433-625">`{controller=Home}` определяет `Home` в качестве объекта `controller` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="02433-626">`{action=Index}` определяет `Index` в качестве объекта `action` по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="02433-627">`{id?}` определяет необязательный параметр `id`.</span><span class="sxs-lookup"><span data-stu-id="02433-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="02433-628">Параметры маршрута по умолчанию и необязательные параметры необязательно должны присутствовать в пути URL-адреса для сопоставления.</span><span class="sxs-lookup"><span data-stu-id="02433-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="02433-629">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="02433-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="02433-630">`"{controller=Home}/{action=Index}/{id?}"` может сопоставляться с путем URL-адреса `/` и выдает значения маршрута `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="02433-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="02433-631">Для объектов `controller` и `action` используются значения по умолчанию. `id` не имеет значения, так как в пути URL-адреса нет соответствующего сегмента.</span><span class="sxs-lookup"><span data-stu-id="02433-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="02433-632">MVC будет использовать эти значения маршрута для выбора действия `HomeController` и `Index`:</span><span class="sxs-lookup"><span data-stu-id="02433-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="02433-633">При использовании этого определения контроллера и шаблона маршрута действие `HomeController.Index` будет выполняться для любых из следующих путей URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="02433-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="02433-634">Универсальный метод `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="02433-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="02433-635">Можно использовать вместо следующего метода:</span><span class="sxs-lookup"><span data-stu-id="02433-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="02433-636">`UseMvc` и `UseMvcWithDefaultRoute` добавляют экземпляр `RouterMiddleware` в конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="02433-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="02433-637">MVC не взаимодействует с ПО промежуточного слоя напрямую, а использует маршрутизацию для обработки запросов.</span><span class="sxs-lookup"><span data-stu-id="02433-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="02433-638">MVC подключается к маршрутам посредством экземпляра `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="02433-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="02433-639">Код метода `UseMvc` имеет примерно следующий вид:</span><span class="sxs-lookup"><span data-stu-id="02433-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="02433-640">Метод `UseMvc` не определяет маршруты напрямую, а добавляет заполнитель в коллекцию маршрутов для маршрута `attribute`.</span><span class="sxs-lookup"><span data-stu-id="02433-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="02433-641">Перегрузка `UseMvc(Action<IRouteBuilder>)` позволяет добавлять собственные маршруты, а также поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="02433-642">Метод `UseMvc` и все его варианты добавляют заполнитель для маршрута на основе атрибутов. Маршрутизация с помощью атрибутов доступна всегда вне зависимости от того, как настроен метод `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="02433-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="02433-643">Метод `UseMvcWithDefaultRoute` определяет маршрут по умолчанию и поддерживает маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="02433-644">В разделе [Маршрутизация с помощью атрибутов](#attribute-routing-ref-label) приводятся более подробные сведения о маршрутизации с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="02433-645">Маршрутизация на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-645">Conventional routing</span></span>

<span data-ttu-id="02433-646">Маршрут `default`:</span><span class="sxs-lookup"><span data-stu-id="02433-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="02433-647">Предыдущий код является примером обычной реуктора.</span><span class="sxs-lookup"><span data-stu-id="02433-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="02433-648">Этот стиль называется обычной маршрутизаний, поскольку он устанавливает *конвенцию* для путей URL:</span><span class="sxs-lookup"><span data-stu-id="02433-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="02433-649">Первый сегмент пути отображает имя контроллера.</span><span class="sxs-lookup"><span data-stu-id="02433-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="02433-650">Вторая карта к названию действия.</span><span class="sxs-lookup"><span data-stu-id="02433-650">The second maps to the action name.</span></span>
* <span data-ttu-id="02433-651">Третий сегмент используется для `id`дополнительного .</span><span class="sxs-lookup"><span data-stu-id="02433-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="02433-652">`id`карты для модели сущности.</span><span class="sxs-lookup"><span data-stu-id="02433-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="02433-653">При использовании этого маршрута `default` путь URL-адреса `/Products/List` сопоставляется с действием `ProductsController.List`, а путь `/Blog/Article/17` сопоставляется с `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="02433-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="02433-654">Такое сопоставление основывается **только** на именах контроллера и действия, но не на пространствах имен, расположениях исходных файлов или параметрах метода.</span><span class="sxs-lookup"><span data-stu-id="02433-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="02433-655">Использование маршрутизации на основе соглашений с маршрутом по умолчанию позволяет быстро создавать приложение, не придумывая новый шаблон URL-адреса для каждого определяемого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="02433-656">В случае с приложением с действиями в стиле CRUD единообразие URL-адресов для всех контроллеров позволяет упростить код и сделать пользовательский интерфейс более предсказуемым.</span><span class="sxs-lookup"><span data-stu-id="02433-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="02433-657">Параметр `id` определяется в шаблоне маршрута как необязательный. Это означает, что действия могут выполняться, даже если идентификатор не указан в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="02433-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="02433-658">Как правило, если параметр `id` отсутствует в URL-адресе, привязка модели присваивает ему значение `0`, и в результате в базе данных не будет найдена сущность, соответствующая `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="02433-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="02433-659">Маршрутизация с помощью атрибутов обеспечивает детальный контроль, позволяя настраивать идентификатор как обязательный лишь для некоторых действий.</span><span class="sxs-lookup"><span data-stu-id="02433-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="02433-660">В документации необязательные параметры, такие как `id`, будут включаться, только если они, скорее всего, могут использоваться в соответствующей ситуации.</span><span class="sxs-lookup"><span data-stu-id="02433-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="02433-661">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-661">Multiple routes</span></span>

<span data-ttu-id="02433-662">В метод `UseMvc` можно добавить несколько маршрутов, добавив дополнительные вызовы `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="02433-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="02433-663">Таким образом можно определить несколько соглашений или добавить маршруты на основе соглашений, предназначенные для определенного действия, например:</span><span class="sxs-lookup"><span data-stu-id="02433-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="02433-664">Маршрут `blog` здесь — это *выделенный маршрут на основе соглашения*. Это означает, что он использует систему маршрутизации на основе соглашений, но предназначен для определенного действия.</span><span class="sxs-lookup"><span data-stu-id="02433-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="02433-665">Так как параметры `controller` и `action` отсутствуют в шаблоне маршрута, они могут иметь только значения по умолчанию, поэтому этот маршрут всегда будет сопоставляться с действием `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="02433-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="02433-666">Маршруты в коллекции маршрутов упорядочены и обрабатываются в порядке добавления.</span><span class="sxs-lookup"><span data-stu-id="02433-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="02433-667">Поэтому в этом примере маршрут `blog` будет проверяться перед маршрутом `default`.</span><span class="sxs-lookup"><span data-stu-id="02433-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-668">*Выделенные обычные маршруты* часто используют `{*article}` параметры **маршрута catch-all,** например, для захвата оставшейся части пути URL.</span><span class="sxs-lookup"><span data-stu-id="02433-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="02433-669">Из-за этого маршрут может оказаться слишком универсальным, то есть он будет соответствовать URL-адресам, которые должны сопоставляться с другими маршрутами.</span><span class="sxs-lookup"><span data-stu-id="02433-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="02433-670">Чтобы избежать этой проблемы, такие универсальные маршруты следует помещать в конце таблицы маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="02433-671">Откат</span><span class="sxs-lookup"><span data-stu-id="02433-671">Fallback</span></span>

<span data-ttu-id="02433-672">В ходе обработки запроса MVC проверяет, можно ли найти контроллер и действие в приложении с помощью предоставленных значений маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="02433-673">Если нет действия, соответствующего значениям, маршрут считается не сопоставленным, и проверяется следующий маршрут.</span><span class="sxs-lookup"><span data-stu-id="02433-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="02433-674">Этот процесс называется *откатом* и призван упростить обработку случаев, когда маршруты на основе соглашений перекрываются.</span><span class="sxs-lookup"><span data-stu-id="02433-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="02433-675">Разрешение неоднозначности действий</span><span class="sxs-lookup"><span data-stu-id="02433-675">Disambiguating actions</span></span>

<span data-ttu-id="02433-676">Если при маршрутизации найдены два соответствующих действия, платформа MVC должна устранить неоднозначность, выбрав наиболее подходящее из них, или создать исключение.</span><span class="sxs-lookup"><span data-stu-id="02433-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="02433-677">Пример:</span><span class="sxs-lookup"><span data-stu-id="02433-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="02433-678">Этот контроллер определяет два действия, которые соответствуют пути URL-адреса `/Products/Edit/17` и маршрутизируют данные `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="02433-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="02433-679">Это типичный шаблон для контроллеров MVC, в котором метод `Edit(int)` отображает форму для изменения сведений о продукте, а метод `Edit(int, Product)` обрабатывает отправленную форму.</span><span class="sxs-lookup"><span data-stu-id="02433-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="02433-680">Чтобы это было возможным, платформа MVC должна выбрать `Edit(int, Product)` для HTTP-запроса `POST` и `Edit(int)` для любой другой HTTP-команды.</span><span class="sxs-lookup"><span data-stu-id="02433-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="02433-681">`HttpPostAttribute` (`[HttpPost]`) — это реализация интерфейса `IActionConstraint`, которая позволяет выбрать действие, только если HTTP-команда — `POST`.</span><span class="sxs-lookup"><span data-stu-id="02433-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="02433-682">Наличие интерфейса `IActionConstraint` делает метод `Edit(int, Product)` более подходящим вариантом, чем `Edit(int)`, поэтому `Edit(int, Product)` будет проверяться первым.</span><span class="sxs-lookup"><span data-stu-id="02433-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="02433-683">Пользовательские реализации `IActionConstraint` потребуется создавать только в особых ситуациях, однако важно понимать роль таких атрибутов, как `HttpPostAttribute`, — аналогичные атрибуты определены для других HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="02433-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="02433-684">При маршрутизации на основе соглашений для действий часто используются одинаковые имена в рамках рабочего процесса `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="02433-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="02433-685">Удобство такого шаблона станет очевидным после ознакомления с разделом [Сведения об интерфейсе IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="02433-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="02433-686">Если найдено несколько совпадений и MVC не может определить наиболее подходящий маршрут, создается исключение `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="02433-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="02433-687">Имена маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-687">Route names</span></span>

<span data-ttu-id="02433-688">Строки `"blog"` и `"default"` в следующих примерах представляют собой имена маршрутов:</span><span class="sxs-lookup"><span data-stu-id="02433-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="02433-689">Имя маршрута — это логическое имя, которое позволяет использовать именованный маршрут для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="02433-690">Имена значительно упрощают создание URL-адресов, если оно представляет сложность из-за порядка маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="02433-691">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="02433-692">Имена маршрутов не влияют на сопоставление URL-адресов или обработку запросов; они служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="02433-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="02433-693">В статье [Маршрутизация](xref:fundamentals/routing) приводятся более подробные сведения о формировании URL-адресов, в том числе во вспомогательных объектах MVC.</span><span class="sxs-lookup"><span data-stu-id="02433-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="02433-694">Маршрутизация с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-694">Attribute routing</span></span>

<span data-ttu-id="02433-695">При маршрутизации с помощью атрибутов используется набор атрибутов для сопоставления действий непосредственно с шаблонами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="02433-696">В приведенном ниже примере в методе `Configure` используется `app.UseMvc();`, и маршрут не передается.</span><span class="sxs-lookup"><span data-stu-id="02433-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="02433-697">`HomeController` будет соответствовать набору URL-адресов, аналогичных тем, которым соответствует маршрут по умолчанию `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="02433-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="02433-698">Действие `HomeController.Index()` будет выполняться для любого из путей URL-адресов `/`, `/Home` или `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="02433-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-699">В этом примере показано ключевое различие между маршрутизацией с помощью атрибутов и маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="02433-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="02433-700">При использовании маршрутизации с помощью атрибутов для указания маршрута требуется больше входных данных; маршрут по умолчанию на основе соглашения более лаконичен.</span><span class="sxs-lookup"><span data-stu-id="02433-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="02433-701">Однако маршрутизация с помощью атрибутов обеспечивает более точный контроль над тем, какие шаблоны маршрутов применяются к каждому действию, и требует такого контроля.</span><span class="sxs-lookup"><span data-stu-id="02433-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="02433-702">В случае с маршрутизацией с помощью атрибутов имя контроллера и имена действий **не** имеют значения при выборе действия.</span><span class="sxs-lookup"><span data-stu-id="02433-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="02433-703">В этом примере сопоставление будет производиться с теми же URL-адресами, что и в предыдущем.</span><span class="sxs-lookup"><span data-stu-id="02433-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="02433-704">Приведенные выше шаблоны маршрутов не определяют параметры маршрутов для `action`, `area` и `controller`.</span><span class="sxs-lookup"><span data-stu-id="02433-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="02433-705">По сути, эти параметры маршрутов не разрешены в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="02433-706">Так как шаблон маршрута уже связан с действием, не имеет смысла анализировать имя действия в URL-адресе.</span><span class="sxs-lookup"><span data-stu-id="02433-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="02433-707">Маршрутизация с помощью атрибутов Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="02433-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="02433-708">При маршрутизации с помощью атрибутов также могут использоваться атрибуты `Http[Verb]`, такие как `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="02433-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="02433-709">Все эти атрибуты могут принимать шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="02433-710">В этом примере показаны два действия, которые совпадают с одним шаблоном маршрута:</span><span class="sxs-lookup"><span data-stu-id="02433-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="02433-711">Для такого пути URL-адреса, как `/products`, действие `ProductsApi.ListProducts` будет выполнено, если HTTP-командой является `GET`, а действие `ProductsApi.CreateProduct` будет выполнено, если HTTP-командой является `POST`.</span><span class="sxs-lookup"><span data-stu-id="02433-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="02433-712">При маршрутизации с помощью атрибутов URL-адрес сначала сопоставляется с набором шаблоном маршрутов, определяемых атрибутами маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="02433-713">После того как найден соответствующий шаблон маршрута, применяются ограничения `IActionConstraint` для определения действий, которые могут быть выполнены.</span><span class="sxs-lookup"><span data-stu-id="02433-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="02433-714">При разработке REST API редко приходится использовать `[Route(...)]` для метода действия, так как действие принимает все методы HTTP.</span><span class="sxs-lookup"><span data-stu-id="02433-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="02433-715">Предпочтительнее применять более конкретные атрибуты `Http*Verb*Attributes`, чтобы точно определить поддерживаемые API возможности.</span><span class="sxs-lookup"><span data-stu-id="02433-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="02433-716">Клиенты интерфейсов REST API должны знать, какие пути и HTTP-команды сопоставляются с определенными логическими операциями.</span><span class="sxs-lookup"><span data-stu-id="02433-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="02433-717">Так как маршрут на основе атрибутов применяется к определенному действию, можно легко сделать параметры обязательными в рамках определения шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="02433-718">В этом примере параметр `id` является обязательным в пути URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="02433-719">Действие `ProductsApi.GetProduct(int)` будет выполнено для такого пути URL-адреса, как `/products/3`, но не для такого пути URL-адреса, как `/products`.</span><span class="sxs-lookup"><span data-stu-id="02433-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="02433-720">Полное описание шаблонов маршрутов и связанных параметров см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="02433-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="02433-721">Имя маршрута</span><span class="sxs-lookup"><span data-stu-id="02433-721">Route Name</span></span>

<span data-ttu-id="02433-722">В следующем коде определяется *имя маршрута*`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="02433-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="02433-723">Имена маршрутов могут использоваться для формирования URL-адреса на основе определенного маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="02433-724">Они не влияют на то, как производится сопоставление URL-адресов при маршрутизации, и служат только для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="02433-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="02433-725">Имена маршрутов должны быть уникальными в пределах приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-726">Сравните это поведение с *маршрутом по умолчанию* на основе соглашения, в котором параметр `id` определяется как необязательный (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="02433-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="02433-727">Такая возможность точно указывать интерфейсы API имеет преимущества. Например, она позволяет направлять `/products` и `/products/5` в разные действия.</span><span class="sxs-lookup"><span data-stu-id="02433-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="02433-728">Объединение маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-728">Combining routes</span></span>

<span data-ttu-id="02433-729">Чтобы избежать лишних повторов при маршрутизации с помощью атрибутов, атрибуты маршрута для контроллера объединяются с атрибутами маршрута для отдельных действий.</span><span class="sxs-lookup"><span data-stu-id="02433-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="02433-730">Все шаблоны маршрутов, определенные в контроллере, добавляются перед шаблонами маршрутов для действий.</span><span class="sxs-lookup"><span data-stu-id="02433-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="02433-731">В результате добавления атрибута маршрута для контроллера **все** его действия будут использовать маршрутизацию с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="02433-732">В этом примере путь URL-адреса `/products` может соответствовать `ProductsApi.ListProducts`, а путь URL-адреса `/products/5` — `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="02433-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="02433-733">Оба эти действия соответствуют только HTTP-запросу `GET`, так как они помечены атрибутом `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="02433-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="02433-734">Шаблоны маршрутов, применяемые к действию, которое начинается с символа `/` или `~/`, не объединяются с шаблонами маршрутов, применяемыми к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="02433-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="02433-735">Этот пример соответствует такому же набору путей URL-адресов, что и *маршрут по умолчанию*.</span><span class="sxs-lookup"><span data-stu-id="02433-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="02433-736">Упорядочение маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-736">Ordering attribute routes</span></span>

<span data-ttu-id="02433-737">В отличие от обычных маршрутов, которые выполняются в определенном порядке, маршрутирование атрибутов строит дерево и совпадает со всеми маршрутами одновременно.</span><span class="sxs-lookup"><span data-stu-id="02433-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="02433-738">Это равносильно тому, как если бы записи маршрутов находились в идеальном порядке: наиболее конкретные маршруты имеют возможность выполнения перед более общими.</span><span class="sxs-lookup"><span data-stu-id="02433-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="02433-739">Например, такой маршрут, как `blog/search/{topic}`, является более конкретным по сравнению с `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="02433-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="02433-740">С логической точки зрения, маршрут `blog/search/{topic}` по умолчанию выполняется первым, так как это единственная разумная очередность.</span><span class="sxs-lookup"><span data-stu-id="02433-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="02433-741">При использовании маршрутизации на основе соглашений разработчик отвечает за расположение маршрутов в нужном порядке.</span><span class="sxs-lookup"><span data-stu-id="02433-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="02433-742">При маршрутизации с помощью атрибутов порядок может настраиваться с помощью свойства `Order` всех атрибутов маршрутов, предоставляемых платформой.</span><span class="sxs-lookup"><span data-stu-id="02433-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="02433-743">Маршруты обрабатываются в порядке возрастания значения свойства `Order`.</span><span class="sxs-lookup"><span data-stu-id="02433-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="02433-744">Порядок по умолчанию — `0`.</span><span class="sxs-lookup"><span data-stu-id="02433-744">The default order is `0`.</span></span> <span data-ttu-id="02433-745">Маршрут, для которого задано значение `Order = -1`, будет выполняться перед маршрутами, для которых порядок не задан.</span><span class="sxs-lookup"><span data-stu-id="02433-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="02433-746">Маршрут, для которого задано значение `Order = 1`, будет выполняться после маршрутов с порядком по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="02433-747">Старайтесь не использовать свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="02433-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="02433-748">Если для правильной маршрутизации в пространстве URL-адресов требуются явно заданные значения порядка, скорее всего, это будет вызывать путаницу и в среде клиентов.</span><span class="sxs-lookup"><span data-stu-id="02433-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="02433-749">Как правило, при маршрутизации с помощью атрибутов правильный маршрут выбирается посредством сопоставления URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="02433-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="02433-750">Если порядок по умолчанию для формирования URL-адресов не работает, использовать имя маршрута в качестве переопределения, как правило, проще, чем применять свойство `Order`.</span><span class="sxs-lookup"><span data-stu-id="02433-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="02433-751">Средства маршрутизации в Razor Pages и контроллере MVC имеют общую реализацию.</span><span class="sxs-lookup"><span data-stu-id="02433-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="02433-752">Сведения о порядке маршрутизации в Razor Pages см. в статье [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) (Маршрутизация и соглашения в приложении Razor Pages: порядок маршрутизации).</span><span class="sxs-lookup"><span data-stu-id="02433-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="02433-753">Замена токенов в шаблонах маршрутов ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="02433-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="02433-754">Для удобства атрибут маршруты поддерживают *замену маркера,* прикрепив`[` `]`токен в квадратные скобки (, ).</span><span class="sxs-lookup"><span data-stu-id="02433-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="02433-755">Токены `[action]`, `[area]` и `[controller]` заменяются значениями имени действия, имени области и имени контроллера из действия, в котором определен маршрут.</span><span class="sxs-lookup"><span data-stu-id="02433-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="02433-756">В следующем примере действия могут соответствовать путям URL-адресов, как описано в комментариях:</span><span class="sxs-lookup"><span data-stu-id="02433-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="02433-757">Замена токенов происходит на последнем этапе создания маршрутов на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="02433-758">Приведенный выше пример будет работать так же, как следующий код:</span><span class="sxs-lookup"><span data-stu-id="02433-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="02433-759">Маршруты на основе атрибутов могут также сочетаться с наследованием.</span><span class="sxs-lookup"><span data-stu-id="02433-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="02433-760">Эта возможность особенно эффективна при использовании вместе с заменой токенов.</span><span class="sxs-lookup"><span data-stu-id="02433-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="02433-761">Замена токенов также применяется к именам маршрутов, определенным в маршрутах на основе атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="02433-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` создает уникальное имя маршрута для каждого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="02433-763">Для сопоставления с литеральным разделителем замены токенов `[` или `]` его следует экранировать путем повтора символа (`[[` или `]]`).</span><span class="sxs-lookup"><span data-stu-id="02433-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="02433-764">Использование преобразователя параметров для настройки замены токенов</span><span class="sxs-lookup"><span data-stu-id="02433-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="02433-765">Замену токенов можно настроить, используя преобразователь параметров.</span><span class="sxs-lookup"><span data-stu-id="02433-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="02433-766">Преобразователь параметров реализует `IOutboundParameterTransformer` и преобразует значения параметров.</span><span class="sxs-lookup"><span data-stu-id="02433-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="02433-767">Например, пользовательский преобразователь параметра `SlugifyParameterTransformer` изменяет значение маршрута `SubscriptionManagement` на `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="02433-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="02433-768">`RouteTokenTransformerConvention` является соглашением для модели приложения, которое:</span><span class="sxs-lookup"><span data-stu-id="02433-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="02433-769">Применяет преобразователь параметров ко всем маршрутам атрибута в приложении.</span><span class="sxs-lookup"><span data-stu-id="02433-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="02433-770">Настраивает значения токена маршрут атрибута при замене.</span><span class="sxs-lookup"><span data-stu-id="02433-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="02433-771">`RouteTokenTransformerConvention` регистрируется в качестве параметра в `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="02433-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="02433-772">Несколько маршрутов</span><span class="sxs-lookup"><span data-stu-id="02433-772">Multiple Routes</span></span>

<span data-ttu-id="02433-773">Маршрутизация с помощью атрибутов поддерживает определение нескольких маршрутов к одному и тому же действию.</span><span class="sxs-lookup"><span data-stu-id="02433-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="02433-774">Наиболее распространенный случай использования этой возможности — имитация поведения *маршрута по умолчанию на основе соглашения*, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="02433-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="02433-775">Добавление нескольких атрибутов маршрута для контроллера означает, что каждый из них будет объединяться с каждым из атрибутов маршрута, определенных для методов действий.</span><span class="sxs-lookup"><span data-stu-id="02433-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="02433-776">Если несколько атрибутов маршрута (реализующих интерфейс `IActionConstraint`) добавлены для действия, каждое ограничение действия объединяется с шаблоном маршрута из атрибута, в котором оно определено.</span><span class="sxs-lookup"><span data-stu-id="02433-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="02433-777">Хотя использование нескольких маршрутов к действиям может показаться очень эффективной возможностью, лучше, чтобы пространство URL-адресов приложения оставалось простым и четко организованным.</span><span class="sxs-lookup"><span data-stu-id="02433-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="02433-778">Используйте несколько маршрутов к действиям только там, где это необходимо, например для поддержки существующих клиентов.</span><span class="sxs-lookup"><span data-stu-id="02433-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="02433-779">Задание необязательных параметров, значений по умолчанию и ограничений для маршрутов на основе атрибутов</span><span class="sxs-lookup"><span data-stu-id="02433-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="02433-780">Маршруты на основе атрибутов поддерживают тот же синтаксис для указания необязательных параметров, значений по умолчанию и ограничений, что и маршруты на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="02433-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="02433-781">Подробное описание синтаксиса шаблона маршрута см. в разделе [Справочник по шаблону маршрута](xref:fundamentals/routing#route-template-reference).</span><span class="sxs-lookup"><span data-stu-id="02433-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="02433-782">Пользовательские атрибуты маршрута с использованием `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="02433-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="02433-783">Все атрибуты маршрутов, предоставляемые платформой ( `[Route(...)]`, `[HttpGet(...)]` и т. д.), реализуют интерфейс `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="02433-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="02433-784">MVC ищет атрибуты в классах контроллеров и методах действий при запуске приложения и использует те из них, которые реализуют интерфейс `IRouteTemplateProvider`, для формирования начального набора маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="02433-785">Вы можете реализовать интерфейс `IRouteTemplateProvider` для определения собственных атрибутов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="02433-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="02433-786">Каждая реализация `IRouteTemplateProvider` позволяет определить один маршрут с пользовательским шаблоном маршрута, порядком и именем.</span><span class="sxs-lookup"><span data-stu-id="02433-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="02433-787">Атрибут в приведенном выше примере автоматически присваивает шаблону `Template` значение `"api/[controller]"` при применении `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="02433-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="02433-788">Настройка маршрутов на основе атрибутов с помощью модели приложения</span><span class="sxs-lookup"><span data-stu-id="02433-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="02433-789">*Модель приложения* — это объектная модель, которая создается при запуске со всеми метаданными, используемыми платформой MVC для маршрутизации и выполнения действий.</span><span class="sxs-lookup"><span data-stu-id="02433-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="02433-790">*Модель приложения* включает в себя все данные, собранные из атрибутов маршрутов (посредством интерфейса `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="02433-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="02433-791">Вы можете создать *соглашения*, чтобы изменить модель приложения при запуске с целью настройки маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="02433-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="02433-792">В этом разделе приводится простой пример настройки маршрутизации с помощью модели приложения.</span><span class="sxs-lookup"><span data-stu-id="02433-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="02433-793">Смешанная маршрутизация с помощью атрибутов и на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="02433-794">В приложениях MVC маршрутизация с помощью атрибутов и маршрутизация на основе соглашений могут использоваться вместе.</span><span class="sxs-lookup"><span data-stu-id="02433-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="02433-795">Маршруты на основе соглашений часто применяются для контроллеров, предоставляющих HTML-страницы для браузеров, а маршруты на основе атрибутов — для контроллеров, предоставляющих интерфейсы REST API.</span><span class="sxs-lookup"><span data-stu-id="02433-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="02433-796">Действия маршрутизируются либо на основе соглашений, либо с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="02433-797">При добавлении маршрута к контроллеру или действию они становятся маршрутизируемыми с помощью атрибутов.</span><span class="sxs-lookup"><span data-stu-id="02433-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="02433-798">Действия, определяющие маршруты на основе атрибутов, недоступны по маршрутам на основе соглашений, и наоборот.</span><span class="sxs-lookup"><span data-stu-id="02433-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="02433-799">**Любой** атрибут маршрута контроллера делает все действия в атрибуте контроллера маршрутизируемыми.</span><span class="sxs-lookup"><span data-stu-id="02433-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-800">Два типа систем маршрутизации отличает процесс, выполняемый после нахождения URL-адреса, соответствующего шаблону маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="02433-801">При маршрутизации на основе соглашений значения маршрута из соответствия используются для выбора действия и контроллера из таблицы подстановки, содержащей все действия с маршрутизацией на основе соглашений.</span><span class="sxs-lookup"><span data-stu-id="02433-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="02433-802">При маршрутизации с помощью атрибутов каждый шаблон уже связан с действием, и дальнейшая подстановка не требуется.</span><span class="sxs-lookup"><span data-stu-id="02433-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="02433-803">Сложные сегменты</span><span class="sxs-lookup"><span data-stu-id="02433-803">Complex segments</span></span>

<span data-ttu-id="02433-804">Сложные сегменты (например, `[Route("/dog{token}cat")]`) обрабатываются путем "нежадного" сопоставления литералов справа налево.</span><span class="sxs-lookup"><span data-stu-id="02433-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="02433-805">Описание см. в [исходном коде](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296).</span><span class="sxs-lookup"><span data-stu-id="02433-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="02433-806">Дополнительные сведения см. в [этой проблеме](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="02433-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="02433-807">Формирование URL-адреса</span><span class="sxs-lookup"><span data-stu-id="02433-807">URL Generation</span></span>

<span data-ttu-id="02433-808">Приложения MVC могут использовать функции формирования URL-адреса, предоставляемые системой маршрутизации, для создания URL-ссылок на действия.</span><span class="sxs-lookup"><span data-stu-id="02433-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="02433-809">Формирование URL-адресов устраняет необходимость в их жестком задании, что делает код надежнее и проще в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="02433-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="02433-810">В этом разделе рассматриваются функции формирования URL-адреса, предоставляемые платформой MVC, и описываются лишь базовые принципы их работы.</span><span class="sxs-lookup"><span data-stu-id="02433-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="02433-811">Подробное описание формирования URL-адреса см. в статье [Маршрутизация](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="02433-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="02433-812">Интерфейс `IUrlHelper` — это базовый компонент инфраструктуры, обеспечивающий взаимодействие между MVC и системой маршрутизации для формирования URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="02433-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="02433-813">Доступ к экземпляру `IUrlHelper` в контроллерах, представлениях и компонентах представлений можно получить посредством свойства `Url`.</span><span class="sxs-lookup"><span data-stu-id="02433-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="02433-814">В этом примере интерфейс `IUrlHelper` используется посредством свойства `Controller.Url` для формирования URL-адреса другого действия.</span><span class="sxs-lookup"><span data-stu-id="02433-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="02433-815">Если в приложении применяется маршрут по умолчанию на основе соглашения, значением переменной `url` будет строка с путем URL-адреса `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="02433-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="02433-816">Этот путь URL-адреса создается системой маршрутизации путем объединения значений маршрута из текущего запроса (значения окружения) со значениями, переданными в `Url.Action`, и подстановки этих значений в шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="02433-817">Значение каждого параметра маршрута в шаблоне маршрута заменяется соответствующими именами со значениями и значениями окружения.</span><span class="sxs-lookup"><span data-stu-id="02433-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="02433-818">Параметр маршрута, у которого нет значения, может принимать значение по умолчанию, если оно имеется, или пропускаться, если он является необязательным (как в случае с параметром `id` в этом примере).</span><span class="sxs-lookup"><span data-stu-id="02433-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="02433-819">Сформировать URL-адрес не удастся, если у любого из обязательных параметров маршрута не будет соответствующего значения.</span><span class="sxs-lookup"><span data-stu-id="02433-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="02433-820">Если для маршрута не удалось сформировать URL-адрес, проверяется следующий маршрут, пока не будут проверены все маршруты или не будет найдено соответствие.</span><span class="sxs-lookup"><span data-stu-id="02433-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="02433-821">В примере `Url.Action` выше предполагается использование маршрутизации на основе соглашений, однако формирование URL-адреса происходит аналогичным образом и в случае с маршрутизацией с помощью атрибутов, хотя принципы иные.</span><span class="sxs-lookup"><span data-stu-id="02433-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="02433-822">При применении маршрутизации на основе соглашений шаблон расширяется с помощью значений маршрута, и значения маршрута для `controller` и `action` обычно находятся в этом шаблоне. Это возможно по той причине, что URL-адреса, сопоставленные системой маршрутизации, следуют *соглашению*.</span><span class="sxs-lookup"><span data-stu-id="02433-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="02433-823">При маршрутизации с помощью атрибутов значения маршрута для `controller` и `action` не могут находиться в шаблоне. Вместо этого они применяются для поиска нужного шаблона.</span><span class="sxs-lookup"><span data-stu-id="02433-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="02433-824">В этом примере используется маршрутизация с помощью атрибутов:</span><span class="sxs-lookup"><span data-stu-id="02433-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="02433-825">MVC создает таблицу подстановки, содержащую все действия с маршрутизацией с помощью атрибутов, и сопоставляет значения `controller` и `action` для выбора шаблона маршрута, с помощью которого должен формироваться URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="02433-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="02433-826">В приведенном выше примере создается URL-адрес `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="02433-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="02433-827">Формирование URL-адресов по имени действия</span><span class="sxs-lookup"><span data-stu-id="02433-827">Generating URLs by action name</span></span>

<span data-ttu-id="02433-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="02433-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="02433-829">`Action`) и все связанные перегрузки предполагают необходимость указания целевого объекта путем задания имени контроллера и имени действия.</span><span class="sxs-lookup"><span data-stu-id="02433-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-830">При использовании метода `Url.Action` текущие значения маршрута для `controller` и `action` уже определены: значения `controller` и `action` включены как в \*значения окружения, так и в \* **обычные** *значения*.</span><span class="sxs-lookup"><span data-stu-id="02433-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="02433-831">Метод `Url.Action` всегда использует текущие значения `action` и `controller` и формирует путь URL-адреса, ведущий к текущему действию.</span><span class="sxs-lookup"><span data-stu-id="02433-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="02433-832">При формировании URL-адреса система маршрутизации пытается использовать значения окружения для заполнения сведений, которые вы не предоставили.</span><span class="sxs-lookup"><span data-stu-id="02433-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="02433-833">При использовании такого маршрута, как `{a}/{b}/{c}/{d}`, и значений окружения `{ a = Alice, b = Bob, c = Carol, d = David }` система маршрутизации имеет достаточно информации для формирования URL-адреса без дополнительных значений, так как все параметры маршрута имеют значения.</span><span class="sxs-lookup"><span data-stu-id="02433-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="02433-834">Если добавить значение `{ d = Donovan }`, значение `{ d = David }` не будет учитываться, и будет сформирован путь URL-адреса `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="02433-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="02433-835">Пути URL-адресов являются иерархическими.</span><span class="sxs-lookup"><span data-stu-id="02433-835">URL paths are hierarchical.</span></span> <span data-ttu-id="02433-836">Если в приведенном выше примере добавить значение `{ c = Cheryl }`, пропущены будут оба значения `{ c = Carol, d = David }`.</span><span class="sxs-lookup"><span data-stu-id="02433-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="02433-837">В этом случае параметр `d` больше не имеет значения и сформировать URL-адрес не удастся.</span><span class="sxs-lookup"><span data-stu-id="02433-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="02433-838">Потребуется указать нужные значения для параметров `c` и `d`.</span><span class="sxs-lookup"><span data-stu-id="02433-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="02433-839">Эта проблема может возникнуть с маршрутом по умолчанию (`{controller}/{action}/{id?}`), но на практике она встречается редко, так как `Url.Action` всегда явным образом задает значения `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="02433-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="02433-840">Более длинные перегрузки `Url.Action` также принимают дополнительный объект *значений маршрута* для предоставления значений параметров маршрута, отличных от `controller` и `action`.</span><span class="sxs-lookup"><span data-stu-id="02433-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="02433-841">Чаще всего он применяется с параметром `id`, например `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="02433-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="02433-842">В соответствии с соглашением объект *значений маршрута* обычно имеет анонимный тип, но это может быть также экземпляр `IDictionary<>` или *обычный объект .NET*.</span><span class="sxs-lookup"><span data-stu-id="02433-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="02433-843">Остальные значения маршрута, которые не соответствуют параметрам маршрута, помещаются в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="02433-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="02433-844">Чтобы создать абсолютный URL-адрес, используйте перегрузку, принимающую `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="02433-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="02433-845">Формирование URL-адресов по маршруту</span><span class="sxs-lookup"><span data-stu-id="02433-845">Generating URLs by route</span></span>

<span data-ttu-id="02433-846">В приведенном выше коде демонстрировалось формирование URL-адреса путем передачи имен контроллера и действия.</span><span class="sxs-lookup"><span data-stu-id="02433-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="02433-847">Интерфейс `IUrlHelper` также предоставляет семейство методов `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="02433-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="02433-848">Эти методы похожи на `Url.Action`, но они не копируют текущие значения `action` и `controller` в значения маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="02433-849">Наиболее распространенный способ их применения — указание имени определенного маршрута, который должен использоваться для формирования URL-адреса, как правило, *без* указания имени контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="02433-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="02433-850">Формирование URL-адресов в HTML</span><span class="sxs-lookup"><span data-stu-id="02433-850">Generating URLs in HTML</span></span>

<span data-ttu-id="02433-851">Интерфейс `IHtmlHelper` предоставляет методы `HtmlHelper``Html.BeginForm` и `Html.ActionLink` для создания элементов `<form>` и `<a>` соответственно.</span><span class="sxs-lookup"><span data-stu-id="02433-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="02433-852">Эти методы используют метод `Url.Action` для формирования URL-адреса и принимают одинаковые аргументы.</span><span class="sxs-lookup"><span data-stu-id="02433-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="02433-853">Эквивалентами методов `Url.RouteUrl` для `HtmlHelper` являются методы `Html.BeginRouteForm` и `Html.RouteLink`, которые имеют схожие функции.</span><span class="sxs-lookup"><span data-stu-id="02433-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="02433-854">Для формирования URL-адресов используются вспомогательные функции тегов `form` и `<a>`.</span><span class="sxs-lookup"><span data-stu-id="02433-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="02433-855">Обе они реализуются с помощью интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="02433-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="02433-856">Дополнительные сведения см. в статье [Работа с формами](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="02433-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="02433-857">Внутри представлений интерфейс `IUrlHelper` доступен посредством свойства `Url` для особых случаев формирования URL-адресов, помимо описанных выше.</span><span class="sxs-lookup"><span data-stu-id="02433-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="02433-858">Формирование URL-адресов в результатах действий</span><span class="sxs-lookup"><span data-stu-id="02433-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="02433-859">В предыдущих примерах было продемонстрировано использование интерфейса `IUrlHelper` в контроллере, хотя чаще всего URL-адреса формируются в контроллерах в рамках результата действия.</span><span class="sxs-lookup"><span data-stu-id="02433-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="02433-860">Базовые классы `ControllerBase` и `Controller` предоставляют удобные методы для результатов действий, ссылающихся на другое действие.</span><span class="sxs-lookup"><span data-stu-id="02433-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="02433-861">Одним из типичных способов их применения является перенаправление после принятия входных данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="02433-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="02433-862">Фабричные методы результатов действий по своей структуре похожи на методы интерфейса `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="02433-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="02433-863">Выделенные маршруты на основе соглашений</span><span class="sxs-lookup"><span data-stu-id="02433-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="02433-864">При маршрутизации на основе соглашений может использоваться особый тип определения маршрута — *выделенный маршрут на основе соглашения*.</span><span class="sxs-lookup"><span data-stu-id="02433-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="02433-865">В приведенном ниже примере маршрут с именем `blog` является выделенным маршрутом на основе соглашения.</span><span class="sxs-lookup"><span data-stu-id="02433-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="02433-866">При использовании таких определений маршрутов метод `Url.Action("Index", "Home")` создаст путь URL-адреса `/` с маршрутом `default`. В чем же причина?</span><span class="sxs-lookup"><span data-stu-id="02433-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="02433-867">Можно было бы предположить, что значений маршрута `{ controller = Home, action = Index }` было бы достаточно для формирования URL-адреса с помощью `blog` и результатом было бы `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="02433-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="02433-868">В случае с выделенными маршрутами на основе соглашений используется особое поведение значений по умолчанию, для которых нет соответствующих параметров маршрута. Благодаря ему маршрут не может быть слишком универсальным при формировании URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="02433-869">В этом случае значения по умолчанию — `{ controller = Blog, action = Article }`, но параметров маршрута `controller` и `action` нет.</span><span class="sxs-lookup"><span data-stu-id="02433-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="02433-870">Когда система маршрутизации производит формирование URL-адреса, предоставленные значения должны соответствовать значениям по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="02433-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="02433-871">Сформировать URL-адрес с помощью `blog` не удастся, так как значения `{ controller = Home, action = Index }` не соответствуют `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="02433-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="02433-872">После этого система маршрутизации выполнит попытку использовать маршрут `default`, которая завершится успешно.</span><span class="sxs-lookup"><span data-stu-id="02433-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="02433-873">Области</span><span class="sxs-lookup"><span data-stu-id="02433-873">Areas</span></span>

<span data-ttu-id="02433-874">[Области](areas.md) — это возможность MVC, которая служит для объединения связанных функций в группу в виде отдельного пространства имен маршрутизации (для действий контроллеров) и структуры папок (для представлений).</span><span class="sxs-lookup"><span data-stu-id="02433-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="02433-875">Благодаря областям приложение может иметь несколько контроллеров с одинаковыми именами, если у них будут разные *области*.</span><span class="sxs-lookup"><span data-stu-id="02433-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="02433-876">При использовании областей создается иерархия в целях маршрутизации. Для этого к `controller` и `action` добавляется еще один параметр маршрута, `area`.</span><span class="sxs-lookup"><span data-stu-id="02433-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="02433-877">В этом разделе рассматривается взаимодействие системы маршрутизации с областями. Подробные сведения об использовании областей с представлениями см. в статье [Области](areas.md).</span><span class="sxs-lookup"><span data-stu-id="02433-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="02433-878">В следующем примере в MVC настраивается использование маршрута на основе соглашения по умолчанию и *маршрута области* для области с именем `Blog`:</span><span class="sxs-lookup"><span data-stu-id="02433-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="02433-879">Результатом сопоставления первого маршрута с таким путем URL-адреса, как `/Manage/Users/AddUser`, будут значения маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="02433-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="02433-880">Значение маршрута `area` получается на основе значения по умолчанию для параметра `area`. По сути, маршрут, создаваемый методом `MapAreaRoute`, эквивалентен следующему:</span><span class="sxs-lookup"><span data-stu-id="02433-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="02433-881">Метод `MapAreaRoute` создает маршрут с помощью значения по умолчанию и ограничения для `area` с использованием предоставленного имени маршрута (в данном случае `Blog`).</span><span class="sxs-lookup"><span data-stu-id="02433-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="02433-882">Значение по умолчанию гарантирует, что маршрут всегда создает значение `{ area = Blog, ... }`. Ограничение требует значения `{ area = Blog, ... }` для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="02433-883">При маршрутизации на основе соглашений учитывается порядок.</span><span class="sxs-lookup"><span data-stu-id="02433-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="02433-884">Как правило, маршруты с областями должны находиться раньше в таблице маршрутов, так как они более точные, чем маршруты без областей.</span><span class="sxs-lookup"><span data-stu-id="02433-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="02433-885">В предыдущем примере значения маршрута будут соответствовать следующему действию:</span><span class="sxs-lookup"><span data-stu-id="02433-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="02433-886">`AreaAttribute` обозначает контроллер в рамках области. В таком случае говорят, что контроллер находится в области `Blog`.</span><span class="sxs-lookup"><span data-stu-id="02433-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="02433-887">Контроллеры без атрибута `[Area]` не входят ни в одну область и **не** будут соответствовать маршруту, если система маршрутизации предоставляет значение маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="02433-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="02433-888">В приведенном ниже примере только первый контроллер может соответствовать значениям маршрута `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="02433-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="02433-889">Пространство имен каждого контроллера приведено здесь для полноты. В противном случае между контроллерами возник бы конфликт именования, и произошла бы ошибка компилятора.</span><span class="sxs-lookup"><span data-stu-id="02433-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="02433-890">Имена пространств классов не влияют на маршрутизацию в MVC.</span><span class="sxs-lookup"><span data-stu-id="02433-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="02433-891">Первые два контроллера входят в области и будут соответствовать запросу, только если соответствующее имя области предоставлено значением маршрута `area`.</span><span class="sxs-lookup"><span data-stu-id="02433-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="02433-892">Третий контроллер не входит ни в одну область и может соответствовать запросу, только если значение `area` не предоставлено системой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="02433-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="02433-893">В плане сопоставления *отсутствующих значений* отсутствие значения `area` равносильно тому, как если значением `area` было бы NULL или пустая строка.</span><span class="sxs-lookup"><span data-stu-id="02433-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="02433-894">При выполнении действия внутри области значение маршрута для `area` будет доступно как *значение окружения*, которое система маршрутизации использует для формирования URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="02433-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="02433-895">Это означает, что по умолчанию области являются *фиксированными* при формировании URL-адресов, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="02433-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="02433-896">Основные сведения об интерфейсе IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="02433-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="02433-897">В этом разделе описываются внутренние принципы работы платформы и то, как MVC выбирает действие, которое необходимо выполнить.</span><span class="sxs-lookup"><span data-stu-id="02433-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="02433-898">Типичному приложению пользовательская реализация `IActionConstraint` не требуется.</span><span class="sxs-lookup"><span data-stu-id="02433-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="02433-899">Скорее всего, вы уже пользовались интерфейсом `IActionConstraint`, даже если не знакомы с ним.</span><span class="sxs-lookup"><span data-stu-id="02433-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="02433-900">Атрибут `[HttpGet]` и сходные атрибуты `[Http-VERB]` реализуют интерфейс `IActionConstraint`, чтобы ограничить выполнение метода действия.</span><span class="sxs-lookup"><span data-stu-id="02433-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="02433-901">В случае с маршрутом по умолчанию на основе соглашения путь URL-адреса `/Products/Edit` даст значения `{ controller = Products, action = Edit }`, которые будут соответствовать **обоим** приведенным здесь действиям.</span><span class="sxs-lookup"><span data-stu-id="02433-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="02433-902">В терминологии `IActionConstraint` можно сказать, что оба действия считаются кандидатами, так как они оба соответствуют данным маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="02433-903">Когда метод `HttpGetAttribute` выполняется, он сообщает, что *Edit()* является соответствием для запроса *GET*, но не является соответствием для каких-либо иных HTTP-команд.</span><span class="sxs-lookup"><span data-stu-id="02433-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="02433-904">Для действия `Edit(...)` ограничения не определены, поэтому оно соответствует любой HTTP-команде.</span><span class="sxs-lookup"><span data-stu-id="02433-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="02433-905">Поэтому если рассматривать запрос `POST`, соответствием будет только `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="02433-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="02433-906">Однако в случае с запросом `GET` соответствовать могут оба действия, хотя действие с `IActionConstraint` всегда считается *имеющим приоритет*.</span><span class="sxs-lookup"><span data-stu-id="02433-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="02433-907">Таким образом, поскольку действие `Edit()` имеет атрибут `[HttpGet]`, оно считается более конкретным и будет выбрано в случае соответствия обоих действий.</span><span class="sxs-lookup"><span data-stu-id="02433-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="02433-908">По сути, интерфейс `IActionConstraint` представляет собой своего рода *перегрузку*, но вместо перегрузки методов с одинаковыми именами он перегружает действия, соответствующие одному и тому же URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="02433-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="02433-909">При маршрутизации с помощью атрибутов также применяется интерфейс `IActionConstraint`, в результате чего кандидатами могут считаться действия из разных контроллеров.</span><span class="sxs-lookup"><span data-stu-id="02433-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="02433-910">Реализация IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="02433-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="02433-911">Самый простой способ реализовать интерфейс `IActionConstraint` — создать класс, производный от `System.Attribute`, и добавить его в действия и контроллеры.</span><span class="sxs-lookup"><span data-stu-id="02433-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="02433-912">MVC автоматически обнаруживает экземпляры `IActionConstraint`, применяемые как атрибуты.</span><span class="sxs-lookup"><span data-stu-id="02433-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="02433-913">Вы можете использовать модель приложения для применения ограничений, и это, пожалуй, самый гибкий подход, так как он позволяет метапрограммировать способ их применения.</span><span class="sxs-lookup"><span data-stu-id="02433-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="02433-914">В следующем примере ограничение выбирает действие, основанное на *коде страны,* из данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="02433-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="02433-915">[Полный пример можно найти в GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="02433-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="02433-916">Вы отвечаете за реализацию метода `Accept` и определение очередности применения ограничений.</span><span class="sxs-lookup"><span data-stu-id="02433-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="02433-917">В этом случае метод `Accept` возвращает значение `true`, означающее, что действие является соответствующим при соответствии значения маршрута `country`.</span><span class="sxs-lookup"><span data-stu-id="02433-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="02433-918">Таким образом, он позволяет возвращаться к действию без атрибута, чем отличается от метода `RouteValueAttribute`.</span><span class="sxs-lookup"><span data-stu-id="02433-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="02433-919">В примере показано, что если определено действие `en-US`, то для кода страны `fr-FR` будет выбран более общий контроллер, к которому не применяется ограничение `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="02433-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="02433-920">Свойство `Order` определяет, к какому *этапу* относится ограничение.</span><span class="sxs-lookup"><span data-stu-id="02433-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="02433-921">Ограничения действий применяются группами в соответствии со значением `Order`.</span><span class="sxs-lookup"><span data-stu-id="02433-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="02433-922">Например, все предоставляемые платформой атрибуты HTTP-методов имеют одинаковое значение `Order`, благодаря чему они выполняются на одном этапе.</span><span class="sxs-lookup"><span data-stu-id="02433-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="02433-923">Чтобы реализовать требуемые политики, можно использовать любое количество этапов.</span><span class="sxs-lookup"><span data-stu-id="02433-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="02433-924">Чтобы выбрать значение `Order`, подумайте, должно ли ограничение применяться перед выполнением HTTP-методов.</span><span class="sxs-lookup"><span data-stu-id="02433-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="02433-925">Чем меньше значение, тем раньше применяется ограничение.</span><span class="sxs-lookup"><span data-stu-id="02433-925">Lower numbers run first.</span></span>

::: moniker-end
