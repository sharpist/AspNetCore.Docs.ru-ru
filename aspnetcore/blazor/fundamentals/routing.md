---
title: Маршрутизация ASP.NET Core Blazor
author: guardrex
description: Узнайте, как маршрутизировать запросы в приложениях и использовать компонент NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/routing
ms.openlocfilehash: 9668077d9b59ff20b1aab0b496278f2460e5ad2a
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103451"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="053ed-103">Маршрутизация ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="053ed-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="053ed-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="053ed-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="053ed-105">Узнайте, как маршрутизировать запросы и использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="053ed-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="053ed-106">Интеграция маршрутизации конечных точек ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="053ed-106">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="053ed-107"> Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="053ed-107"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="053ed-108">Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="053ed-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="053ed-109">Наиболее типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="053ed-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="053ed-110">По соглашению страница *узла* обычно называется *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="053ed-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="053ed-111">Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="053ed-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="053ed-112">Резервный маршрут рассматривается, если другие маршруты не сопоставляются.</span><span class="sxs-lookup"><span data-stu-id="053ed-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="053ed-113">Это позволяет приложению использовать другие контроллеры и страницы, не мешая работе приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="053ed-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="053ed-114">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="053ed-114">Route templates</span></span>

<span data-ttu-id="053ed-115">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию для каждого компонента с указанным маршрутом.</span><span class="sxs-lookup"><span data-stu-id="053ed-115">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="053ed-116">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> находится в файле *App.razor*.</span><span class="sxs-lookup"><span data-stu-id="053ed-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="053ed-117">При компиляции файла *.razor* с директивой `@page` созданный класс предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="053ed-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="053ed-118">В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:</span><span class="sxs-lookup"><span data-stu-id="053ed-118">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="053ed-119">получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с всеми необходимыми параметрами;</span><span class="sxs-lookup"><span data-stu-id="053ed-119">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="053ed-120">визуализирует указанный компонент с его макетом (или необязательным макетом по умолчанию), используя указанные параметры.</span><span class="sxs-lookup"><span data-stu-id="053ed-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="053ed-121">При необходимости можно указать параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета, который будет использоваться для компонентов, которые не задают макет.</span><span class="sxs-lookup"><span data-stu-id="053ed-121">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="053ed-122">Шаблоны Blazor по умолчанию определяют компонент `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="053ed-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="053ed-123">Файл *MainLayout.razor* находится в папке *Shared* проекта шаблона.</span><span class="sxs-lookup"><span data-stu-id="053ed-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="053ed-124">Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="053ed-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="053ed-125">К компоненту можно применить несколько шаблонов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="053ed-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="053ed-126">Следующий компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="053ed-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="053ed-127">Для правильного разрешения URL-адресов приложение должно включать тег `<base>` в файл *wwwroot/index.html* (Blazor WebAssembly) или файл *Pages/_Host.cshtml* (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="053ed-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="053ed-128">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="053ed-128">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="053ed-129">Предоставление пользовательского содержимого, когда содержимое не найдено</span><span class="sxs-lookup"><span data-stu-id="053ed-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="053ed-130">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.</span><span class="sxs-lookup"><span data-stu-id="053ed-130">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="053ed-131">В файле *App.razor* задайте пользовательское содержимое в параметре шаблона <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="053ed-131">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="053ed-132">Содержимое тегов `<NotFound>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="053ed-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="053ed-133">Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="053ed-133">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="053ed-134">Маршрутизация к компонентам из нескольких сборок</span><span class="sxs-lookup"><span data-stu-id="053ed-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="053ed-135">Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="053ed-135">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="053ed-136">Указанные сборки рассматриваются в дополнение к сборке, указанной в `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="053ed-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="053ed-137">В следующем примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="053ed-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="053ed-138">В следующем примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.</span><span class="sxs-lookup"><span data-stu-id="053ed-138">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="053ed-139">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="053ed-139">Route parameters</span></span>

<span data-ttu-id="053ed-140">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).</span><span class="sxs-lookup"><span data-stu-id="053ed-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="053ed-141">Необязательные параметры не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="053ed-141">Optional parameters aren't supported.</span></span> <span data-ttu-id="053ed-142">В предыдущем примере применяются две директивы `@page`.</span><span class="sxs-lookup"><span data-stu-id="053ed-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="053ed-143">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="053ed-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="053ed-144">Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="053ed-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="053ed-145">Ограничения маршрута</span><span class="sxs-lookup"><span data-stu-id="053ed-145">Route constraints</span></span>

<span data-ttu-id="053ed-146">Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.</span><span class="sxs-lookup"><span data-stu-id="053ed-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="053ed-147">В следующем примере маршрут к компоненту `Users` соответствует только в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="053ed-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="053ed-148">в URL-адресе запроса имеется сегмент маршрута `Id`;</span><span class="sxs-lookup"><span data-stu-id="053ed-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="053ed-149">сегмент `Id` является целым числом (`int`).</span><span class="sxs-lookup"><span data-stu-id="053ed-149">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="053ed-150">Доступны ограничения маршрутов, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="053ed-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="053ed-151">Сведения об ограничениях маршрута, соответствующих инвариантному языку и региональным параметрам, см. в предупреждении внизу таблицы.</span><span class="sxs-lookup"><span data-stu-id="053ed-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="053ed-152">Ограничение</span><span class="sxs-lookup"><span data-stu-id="053ed-152">Constraint</span></span> | <span data-ttu-id="053ed-153">Пример</span><span class="sxs-lookup"><span data-stu-id="053ed-153">Example</span></span>           | <span data-ttu-id="053ed-154">Примеры совпадений</span><span class="sxs-lookup"><span data-stu-id="053ed-154">Example Matches</span></span>                                                                  | <span data-ttu-id="053ed-155">Инвариант</span><span class="sxs-lookup"><span data-stu-id="053ed-155">Invariant</span></span><br><span data-ttu-id="053ed-156">язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="053ed-156">culture</span></span><br><span data-ttu-id="053ed-157">соответствие</span><span class="sxs-lookup"><span data-stu-id="053ed-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="053ed-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="053ed-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="053ed-159">Нет</span><span class="sxs-lookup"><span data-stu-id="053ed-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="053ed-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="053ed-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="053ed-161">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="053ed-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="053ed-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="053ed-163">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="053ed-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="053ed-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="053ed-165">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="053ed-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="053ed-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="053ed-167">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="053ed-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="053ed-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="053ed-169">Нет</span><span class="sxs-lookup"><span data-stu-id="053ed-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="053ed-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="053ed-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="053ed-171">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="053ed-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="053ed-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="053ed-173">Да</span><span class="sxs-lookup"><span data-stu-id="053ed-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="053ed-174">Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="053ed-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="053ed-175">Эти ограничения предполагают, что URL-адрес является нелокализуемым.</span><span class="sxs-lookup"><span data-stu-id="053ed-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="053ed-176">Маршрутизация URL-адресов, содержащих точки</span><span class="sxs-lookup"><span data-stu-id="053ed-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="053ed-177">В приложениях Blazor Server маршрутом по умолчанию в *_Host.cshtml* является `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="053ed-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="053ed-178">URL-адрес запроса, содержащий точку (`.`), не соответствует маршруту по умолчанию, так как такой адрес считается запросом файла.</span><span class="sxs-lookup"><span data-stu-id="053ed-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="053ed-179">Приложение Blazor возвращает ответ *404 Not Found* (Не найдено) для статического файла, который не существует.</span><span class="sxs-lookup"><span data-stu-id="053ed-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="053ed-180">Чтобы использовать маршруты, содержащие точку, добавьте в файл *_Host.cshtml* следующий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="053ed-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="053ed-181">Шаблон `"/{**path}"` включает в себя следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="053ed-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="053ed-182">двойная *универсальная* звездочка (`**`) для захвата пути через границы нескольких папок без кодирования косой чертой (`/`);</span><span class="sxs-lookup"><span data-stu-id="053ed-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="053ed-183">имя параметра маршрута `path`.</span><span class="sxs-lookup"><span data-stu-id="053ed-183">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="053ed-184">Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="053ed-184">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="053ed-185">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="053ed-185">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="053ed-186">Компонент NavLink</span><span class="sxs-lookup"><span data-stu-id="053ed-186">NavLink component</span></span>

<span data-ttu-id="053ed-187">Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="053ed-187">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="053ed-188">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="053ed-188">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="053ed-189">Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации.</span><span class="sxs-lookup"><span data-stu-id="053ed-189">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="053ed-190">Следующий компонент `NavMenu` создает панель навигации [Bootstrap](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>.</span><span class="sxs-lookup"><span data-stu-id="053ed-190">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="053ed-191">Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="053ed-191">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="053ed-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;</span><span class="sxs-lookup"><span data-stu-id="053ed-192"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="053ed-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*по умолчанию*). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="053ed-193"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="053ed-194">В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="053ed-194">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="053ed-195">Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `MyComponent` (например, `https://localhost:5001/MyComponent` и `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="053ed-195">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="053ed-196">Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки.</span><span class="sxs-lookup"><span data-stu-id="053ed-196">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="053ed-197">В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.</span><span class="sxs-lookup"><span data-stu-id="053ed-197">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="053ed-198">Отобразится следующая разметка HTML.</span><span class="sxs-lookup"><span data-stu-id="053ed-198">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="053ed-199">URI и вспомогательные инструменты состояния навигации</span><span class="sxs-lookup"><span data-stu-id="053ed-199">URI and navigation state helpers</span></span>

<span data-ttu-id="053ed-200">Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для работы с URI и навигацией в коде C#.</span><span class="sxs-lookup"><span data-stu-id="053ed-200">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="053ed-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="053ed-201"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="053ed-202">Член</span><span class="sxs-lookup"><span data-stu-id="053ed-202">Member</span></span> | <span data-ttu-id="053ed-203">Описание</span><span class="sxs-lookup"><span data-stu-id="053ed-203">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="053ed-204">Возвращает текущий абсолютный URI.</span><span class="sxs-lookup"><span data-stu-id="053ed-204">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="053ed-205">Получает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI.</span><span class="sxs-lookup"><span data-stu-id="053ed-205">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="053ed-206">Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в *wwwroot/index.html* (Blazor WebAssembly) или *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="053ed-206">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="053ed-207">Переходит по указанному URI.</span><span class="sxs-lookup"><span data-stu-id="053ed-207">Navigates to the specified URI.</span></span> <span data-ttu-id="053ed-208">Если значение `forceLoad` равно `true`:</span><span class="sxs-lookup"><span data-stu-id="053ed-208">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="053ed-209">маршрутизация на стороне клиента обходится;</span><span class="sxs-lookup"><span data-stu-id="053ed-209">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="053ed-210">браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="053ed-210">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="053ed-211">Событие, возникающее при изменении расположения навигации.</span><span class="sxs-lookup"><span data-stu-id="053ed-211">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="053ed-212">Преобразует относительный URI в абсолютный.</span><span class="sxs-lookup"><span data-stu-id="053ed-212">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="053ed-213">Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса.</span><span class="sxs-lookup"><span data-stu-id="053ed-213">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="053ed-214">Следующий компонент при нажатии кнопки переходит к компоненту приложения `Counter`.</span><span class="sxs-lookup"><span data-stu-id="053ed-214">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="053ed-215">Следующий компонент обрабатывает событие изменения расположения путем определения <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="053ed-215">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="053ed-216">При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется.</span><span class="sxs-lookup"><span data-stu-id="053ed-216">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="053ed-217">После отсоединения метода становится возможной сборка мусора для компонента.</span><span class="sxs-lookup"><span data-stu-id="053ed-217">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="053ed-218">В <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> содержатся следующие сведения о событии.</span><span class="sxs-lookup"><span data-stu-id="053ed-218"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="053ed-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.</span><span class="sxs-lookup"><span data-stu-id="053ed-219"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="053ed-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера.</span><span class="sxs-lookup"><span data-stu-id="053ed-220"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="053ed-221">Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.</span><span class="sxs-lookup"><span data-stu-id="053ed-221">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="053ed-222">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="053ed-222">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
