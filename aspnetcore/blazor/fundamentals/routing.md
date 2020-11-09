---
title: 'Маршрутизация ASP.NET Core Blazor'
author: guardrex
description: Узнайте, как маршрутизировать запросы в приложениях и использовать компонент NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/routing
ms.openlocfilehash: 5898059d83576cd0d2af15ad61bc399cbfbe0e99
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055858"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="23a2d-103">Маршрутизация ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="23a2d-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="23a2d-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="23a2d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="23a2d-105">Узнайте, как маршрутизировать запросы и использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="23a2d-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="23a2d-106">Интеграция маршрутизации конечных точек ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23a2d-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="23a2d-107">Blazor Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="23a2d-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="23a2d-108">Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="23a2d-109">Наиболее типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="23a2d-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="23a2d-110">По соглашению страница *узла* обычно называется `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="23a2d-111">Маршрут, указанный в файле узла, называется *резервным маршрутом* , так как он работает с низким приоритетом в соответствии с правилами маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="23a2d-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="23a2d-112">Резервный маршрут рассматривается, если другие маршруты не сопоставляются.</span><span class="sxs-lookup"><span data-stu-id="23a2d-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="23a2d-113">Это позволяет приложению использовать другие контроллеры и страницы, не мешая работе приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="23a2d-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="23a2d-114">Сведения о настройке <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> для размещения сервера по некорневому URL-адресу см. в разделе <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="23a2d-115">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="23a2d-115">Route templates</span></span>

<span data-ttu-id="23a2d-116">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию для каждого компонента с указанным маршрутом.</span><span class="sxs-lookup"><span data-stu-id="23a2d-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="23a2d-117">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> находится в файле `App.razor`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="23a2d-118">При компиляции файла `.razor` с директивой `@page` созданный класс предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="23a2d-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="23a2d-119">В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:</span><span class="sxs-lookup"><span data-stu-id="23a2d-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="23a2d-120">получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с всеми необходимыми параметрами;</span><span class="sxs-lookup"><span data-stu-id="23a2d-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="23a2d-121">визуализирует указанный компонент с его макетом (или необязательным макетом по умолчанию), используя указанные параметры.</span><span class="sxs-lookup"><span data-stu-id="23a2d-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="23a2d-122">При необходимости можно указать параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета, который будет использоваться для компонентов, которые не задают макет.</span><span class="sxs-lookup"><span data-stu-id="23a2d-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="23a2d-123">Шаблоны Blazor по умолчанию определяют компонент `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="23a2d-124">Файл `MainLayout.razor` находится в папке `Shared` проекта шаблона.</span><span class="sxs-lookup"><span data-stu-id="23a2d-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="23a2d-125">Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="23a2d-126">К компоненту можно применить несколько шаблонов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="23a2d-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="23a2d-127">Следующий компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="23a2d-128">Для правильного разрешения URL-адресов приложение должно содержать тег `<base>` в файле `wwwroot/index.html` (Blazor WebAssembly) или файле `Pages/_Host.cshtml` (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="23a2d-129">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="23a2d-130">Предоставление пользовательского содержимого, когда содержимое не найдено</span><span class="sxs-lookup"><span data-stu-id="23a2d-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="23a2d-131">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.</span><span class="sxs-lookup"><span data-stu-id="23a2d-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="23a2d-132">В файле `App.razor` задайте пользовательское содержимое в параметре шаблона <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="23a2d-133">Содержимое тегов `<NotFound>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="23a2d-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="23a2d-134">Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="23a2d-135">Маршрутизация к компонентам из нескольких сборок</span><span class="sxs-lookup"><span data-stu-id="23a2d-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="23a2d-136">Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="23a2d-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="23a2d-137">Указанные сборки рассматриваются в дополнение к сборке, указанной в `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="23a2d-138">В следующем примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="23a2d-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="23a2d-139">В следующем примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="23a2d-140">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="23a2d-140">Route parameters</span></span>

<span data-ttu-id="23a2d-141">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).</span><span class="sxs-lookup"><span data-stu-id="23a2d-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="23a2d-142">Необязательные параметры не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="23a2d-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="23a2d-143">В предыдущем примере применяются две директивы `@page`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="23a2d-144">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="23a2d-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="23a2d-145">Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="23a2d-146">Ограничения маршрута</span><span class="sxs-lookup"><span data-stu-id="23a2d-146">Route constraints</span></span>

<span data-ttu-id="23a2d-147">Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.</span><span class="sxs-lookup"><span data-stu-id="23a2d-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="23a2d-148">В следующем примере маршрут к компоненту `Users` соответствует только в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="23a2d-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="23a2d-149">в URL-адресе запроса имеется сегмент маршрута `Id`;</span><span class="sxs-lookup"><span data-stu-id="23a2d-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="23a2d-150">сегмент `Id` является целым числом (`int`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="23a2d-151">Доступны ограничения маршрутов, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="23a2d-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="23a2d-152">Сведения об ограничениях маршрута, соответствующих инвариантному языку и региональным параметрам, см. в предупреждении внизу таблицы.</span><span class="sxs-lookup"><span data-stu-id="23a2d-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="23a2d-153">Ограничение</span><span class="sxs-lookup"><span data-stu-id="23a2d-153">Constraint</span></span> | <span data-ttu-id="23a2d-154">Пример</span><span class="sxs-lookup"><span data-stu-id="23a2d-154">Example</span></span>           | <span data-ttu-id="23a2d-155">Примеры совпадений</span><span class="sxs-lookup"><span data-stu-id="23a2d-155">Example Matches</span></span>                                                                  | <span data-ttu-id="23a2d-156">Инвариант</span><span class="sxs-lookup"><span data-stu-id="23a2d-156">Invariant</span></span><br><span data-ttu-id="23a2d-157">язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="23a2d-157">culture</span></span><br><span data-ttu-id="23a2d-158">соответствие</span><span class="sxs-lookup"><span data-stu-id="23a2d-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="23a2d-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="23a2d-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="23a2d-160">Нет</span><span class="sxs-lookup"><span data-stu-id="23a2d-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="23a2d-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="23a2d-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="23a2d-162">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="23a2d-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="23a2d-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="23a2d-164">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="23a2d-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="23a2d-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="23a2d-166">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="23a2d-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="23a2d-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="23a2d-168">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="23a2d-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="23a2d-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="23a2d-170">Нет</span><span class="sxs-lookup"><span data-stu-id="23a2d-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="23a2d-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="23a2d-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="23a2d-172">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="23a2d-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="23a2d-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="23a2d-174">Да</span><span class="sxs-lookup"><span data-stu-id="23a2d-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="23a2d-175">Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="23a2d-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="23a2d-176">Эти ограничения предполагают, что URL-адрес является нелокализуемым.</span><span class="sxs-lookup"><span data-stu-id="23a2d-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="23a2d-177">Маршрутизация URL-адресов, содержащих точки</span><span class="sxs-lookup"><span data-stu-id="23a2d-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="23a2d-178">Для размещенных приложений Blazor WebAssembly и Blazor Server шаблон маршрута по умолчанию на стороне сервера предполагает, что если последний сегмент URL-адреса запроса содержит точку (`.`), запрашивается файл (например, `https://localhost.com:5001/example/some.thing`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-178">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="23a2d-179">Без дополнительной конфигурации приложение возвращает ответ *404 — Not Found* (не найдено), если предполагалась маршрутизация к компоненту.</span><span class="sxs-lookup"><span data-stu-id="23a2d-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="23a2d-180">Чтобы использовать маршрут с одним или несколькими параметрами, содержащими точку, в приложении необходимо настроить маршрут с помощью пользовательского шаблона.</span><span class="sxs-lookup"><span data-stu-id="23a2d-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="23a2d-181">Рассмотрим следующий компонент `Example`, который может получать параметр маршрута из последнего сегмента URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="23a2d-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="23a2d-182">Чтобы позволить *серверному* приложению размещенного решения Blazor WebAssembly маршрутизировать запрос с точкой в параметре `param`, добавьте шаблон резервного маршрута к файлу с дополнительным параметром в `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="23a2d-182">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="23a2d-183">Чтобы позволить приложению Blazor Server маршрутизировать запрос с точкой в параметре `param`, добавьте шаблон резервного маршрута к странице с дополнительным параметром в `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="23a2d-183">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="23a2d-184">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="23a2d-185">Параметры маршрута catch-all</span><span class="sxs-lookup"><span data-stu-id="23a2d-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="23a2d-186">*Этот раздел относится к ASP.NET Core в релизе-кандидате 1 (RC1) платформы .NET 5 или более поздней версии.*</span><span class="sxs-lookup"><span data-stu-id="23a2d-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="23a2d-187">В компонентах поддерживаются параметры маршрута catch-all, которые захватывают пути в нескольких папках.</span><span class="sxs-lookup"><span data-stu-id="23a2d-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="23a2d-188">Требования к параметру catch-all</span><span class="sxs-lookup"><span data-stu-id="23a2d-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="23a2d-189">Его имя должно соответствовать имени сегмента маршрута.</span><span class="sxs-lookup"><span data-stu-id="23a2d-189">Named to match the route segment name.</span></span> <span data-ttu-id="23a2d-190">Регистр в имени не учитывается.</span><span class="sxs-lookup"><span data-stu-id="23a2d-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="23a2d-191">Тип `string`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-191">A `string` type.</span></span> <span data-ttu-id="23a2d-192">Платформа не обеспечивает автоматическое приведение.</span><span class="sxs-lookup"><span data-stu-id="23a2d-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="23a2d-193">В конце URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="23a2d-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="23a2d-194">Для URL-адреса `/page/this/is/a/test` с шаблоном маршрута `/page/{*pageRoute}` значение `PageRoute` равно `this/is/a/test`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="23a2d-195">Косые черты и сегменты захваченного пути декодированы.</span><span class="sxs-lookup"><span data-stu-id="23a2d-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="23a2d-196">Для шаблона маршрута `/page/{*pageRoute}` URL-адрес `/page/this/is/a%2Ftest%2A` возвращает `this/is/a/test*`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="23a2d-197">Параметры маршрута catch-all поддерживаются в ASP.NET Core в релизе-кандидате 1 (RC1) платформы .NET 5 или более поздней версии. \*</span><span class="sxs-lookup"><span data-stu-id="23a2d-197">Catch-all route parameters are supported in ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.\*</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="23a2d-198">Компонент NavLink</span><span class="sxs-lookup"><span data-stu-id="23a2d-198">NavLink component</span></span>

<span data-ttu-id="23a2d-199">Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="23a2d-200">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="23a2d-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="23a2d-201">Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации.</span><span class="sxs-lookup"><span data-stu-id="23a2d-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="23a2d-202">При необходимости назначьте имя класса CSS свойству <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType>, чтобы применить пользовательский класс CSS к отображаемой ссылке, если текущий маршрут совпадает с `href`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="23a2d-203">Следующий компонент `NavMenu` создает панель навигации [`Bootstrap`](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>:</span><span class="sxs-lookup"><span data-stu-id="23a2d-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="23a2d-204">Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="23a2d-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="23a2d-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;</span><span class="sxs-lookup"><span data-stu-id="23a2d-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="23a2d-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *по умолчанию* ). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="23a2d-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *default* ): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="23a2d-207">В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="23a2d-208">Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `MyComponent` (например, `https://localhost:5001/MyComponent` и `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="23a2d-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="23a2d-209">Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки.</span><span class="sxs-lookup"><span data-stu-id="23a2d-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="23a2d-210">В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="23a2d-211">Отобразится следующая разметка HTML.</span><span class="sxs-lookup"><span data-stu-id="23a2d-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="23a2d-212">В связи с тем, как Blazor выполняет рендеринг дочернего содержимого, для рендеринга компонентов `NavLink` в цикле `for` требуется задать локальную переменную индекса, если в содержимом дочернего компонента `NavLink` используется переменная цикла приращения:</span><span class="sxs-lookup"><span data-stu-id="23a2d-212">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="23a2d-213">Использование переменной индекса в этом сценарии обязательно для **любого** дочернего компонента, который использует переменную цикла в своем [дочернем содержимом](xref:blazor/components/index#child-content), а не только для компонента `NavLink`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="23a2d-214">Вместо этого можно использовать цикл `foreach` с <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="23a2d-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="23a2d-215">URI и вспомогательные инструменты состояния навигации</span><span class="sxs-lookup"><span data-stu-id="23a2d-215">URI and navigation state helpers</span></span>

<span data-ttu-id="23a2d-216">Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для работы с URI и навигацией в коде C#.</span><span class="sxs-lookup"><span data-stu-id="23a2d-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="23a2d-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="23a2d-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="23a2d-218">Член</span><span class="sxs-lookup"><span data-stu-id="23a2d-218">Member</span></span> | <span data-ttu-id="23a2d-219">Описание</span><span class="sxs-lookup"><span data-stu-id="23a2d-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="23a2d-220">Возвращает текущий абсолютный URI.</span><span class="sxs-lookup"><span data-stu-id="23a2d-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="23a2d-221">Получает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI.</span><span class="sxs-lookup"><span data-stu-id="23a2d-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="23a2d-222">Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="23a2d-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="23a2d-223">Переходит по указанному URI.</span><span class="sxs-lookup"><span data-stu-id="23a2d-223">Navigates to the specified URI.</span></span> <span data-ttu-id="23a2d-224">Если значение `forceLoad` равно `true`:</span><span class="sxs-lookup"><span data-stu-id="23a2d-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="23a2d-225">маршрутизация на стороне клиента обходится;</span><span class="sxs-lookup"><span data-stu-id="23a2d-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="23a2d-226">браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="23a2d-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="23a2d-227">Событие, возникающее при изменении расположения навигации.</span><span class="sxs-lookup"><span data-stu-id="23a2d-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="23a2d-228">Преобразует относительный URI в абсолютный.</span><span class="sxs-lookup"><span data-stu-id="23a2d-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="23a2d-229">Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса.</span><span class="sxs-lookup"><span data-stu-id="23a2d-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="23a2d-230">Следующий компонент при нажатии кнопки переходит к компоненту приложения `Counter`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="23a2d-231">Следующий компонент обрабатывает событие изменения расположения путем оформления подписки на <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="23a2d-232">При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется.</span><span class="sxs-lookup"><span data-stu-id="23a2d-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="23a2d-233">После отсоединения метода становится возможной сборка мусора для компонента.</span><span class="sxs-lookup"><span data-stu-id="23a2d-233">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="23a2d-234">В <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> содержатся следующие сведения о событии.</span><span class="sxs-lookup"><span data-stu-id="23a2d-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="23a2d-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.</span><span class="sxs-lookup"><span data-stu-id="23a2d-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="23a2d-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера.</span><span class="sxs-lookup"><span data-stu-id="23a2d-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="23a2d-237">Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.</span><span class="sxs-lookup"><span data-stu-id="23a2d-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="23a2d-238">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="23a2d-239">Строка запроса и параметры анализа</span><span class="sxs-lookup"><span data-stu-id="23a2d-239">Query string and parse parameters</span></span>

<span data-ttu-id="23a2d-240">Строку запроса можно получить из свойства <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri>:</span><span class="sxs-lookup"><span data-stu-id="23a2d-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="23a2d-241">Чтобы проанализировать параметры строки запроса:</span><span class="sxs-lookup"><span data-stu-id="23a2d-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="23a2d-242">Добавьте ссылку на пакет для [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="23a2d-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="23a2d-243">Получите значение после анализа строки запроса с помощью <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="23a2d-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="23a2d-244">Заполнитель `{KEY}` в предыдущем примере — это ключ параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="23a2d-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="23a2d-245">Например, пара "ключ-значение" URL-адреса `?ship=Tardis` использует ключ `ship`.</span><span class="sxs-lookup"><span data-stu-id="23a2d-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
