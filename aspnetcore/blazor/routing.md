---
<span data-ttu-id="49b9e-101">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-102">'Blazor'</span></span>
- <span data-ttu-id="49b9e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-103">'Identity'</span></span>
- <span data-ttu-id="49b9e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-105">'Razor'</span></span>
- <span data-ttu-id="49b9e-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="49b9e-107">Маршрутизация ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="49b9e-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="49b9e-108">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="49b9e-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="49b9e-109">Узнайте, как маршрутизировать запросы и использовать компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> для создания навигационных ссылок в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="49b9e-109">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="49b9e-110">Интеграция маршрутизации конечных точек ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49b9e-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="49b9e-111"> Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="49b9e-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="49b9e-112">Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="49b9e-113">Наиболее типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="49b9e-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="49b9e-114">По соглашению страница *узла* обычно называется *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="49b9e-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="49b9e-115">Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="49b9e-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="49b9e-116">Резервный маршрут рассматривается, если другие маршруты не сопоставляются.</span><span class="sxs-lookup"><span data-stu-id="49b9e-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="49b9e-117">Это позволяет приложению использовать другие контроллеры и страницы, не мешая работе приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="49b9e-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="49b9e-118">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="49b9e-118">Route templates</span></span>

<span data-ttu-id="49b9e-119">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет выполнять маршрутизацию для каждого компонента с указанным маршрутом.</span><span class="sxs-lookup"><span data-stu-id="49b9e-119">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="49b9e-120">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> находится в файле *App.razor*.</span><span class="sxs-lookup"><span data-stu-id="49b9e-120">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="49b9e-121">При компиляции файла *.razor* с директивой `@page` созданный класс предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="49b9e-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="49b9e-122">В среде выполнения компонент <xref:Microsoft.AspNetCore.Components.RouteView> выполняет следующие операции:</span><span class="sxs-lookup"><span data-stu-id="49b9e-122">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="49b9e-123">получает <xref:Microsoft.AspNetCore.Components.RouteData> от <xref:Microsoft.AspNetCore.Components.Routing.Router> вместе с всеми необходимыми параметрами;</span><span class="sxs-lookup"><span data-stu-id="49b9e-123">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="49b9e-124">визуализирует указанный компонент с его макетом (или необязательным макетом по умолчанию), используя указанные параметры.</span><span class="sxs-lookup"><span data-stu-id="49b9e-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="49b9e-125">При необходимости можно указать параметр <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> с классом макета, который будет использоваться для компонентов, которые не задают макет.</span><span class="sxs-lookup"><span data-stu-id="49b9e-125">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="49b9e-126">Шаблоны Blazor по умолчанию определяют компонент `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="49b9e-127">Файл *MainLayout.razor* находится в папке *Shared* проекта шаблона.</span><span class="sxs-lookup"><span data-stu-id="49b9e-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="49b9e-128">Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="49b9e-129">К компоненту можно применить несколько шаблонов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="49b9e-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="49b9e-130">Следующий компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="49b9e-131">Для правильного разрешения URL-адресов приложение должно включать тег `<base>` в файл *wwwroot/index.html* (Blazor WebAssembly) или файл *Pages/_Host.cshtml* (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="49b9e-132">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="49b9e-133">Предоставление пользовательского содержимого, когда содержимое не найдено</span><span class="sxs-lookup"><span data-stu-id="49b9e-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="49b9e-134">Компонент <xref:Microsoft.AspNetCore.Components.Routing.Router> позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.</span><span class="sxs-lookup"><span data-stu-id="49b9e-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="49b9e-135">В файле *App.razor* задайте пользовательское содержимое в параметре шаблона <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-135">In the *App.razor* file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="49b9e-136">Содержимое тегов `<NotFound>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="49b9e-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="49b9e-137">Сведения о применении макета по умолчанию к содержимому <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-137">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="49b9e-138">Маршрутизация к компонентам из нескольких сборок</span><span class="sxs-lookup"><span data-stu-id="49b9e-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="49b9e-139">Используйте параметр <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, чтобы указать дополнительные сборки для компонента <xref:Microsoft.AspNetCore.Components.Routing.Router>, которые следует учитывать при поиске маршрутизируемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="49b9e-139">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="49b9e-140">Указанные сборки рассматриваются в дополнение к сборке, указанной в `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="49b9e-141">В следующем примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="49b9e-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="49b9e-142">В следующем примере <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> приводится поддержка маршрутизации для `Component1`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-142">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="49b9e-143">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="49b9e-143">Route parameters</span></span>

<span data-ttu-id="49b9e-144">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).</span><span class="sxs-lookup"><span data-stu-id="49b9e-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="49b9e-145">Необязательные параметры не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="49b9e-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="49b9e-146">В предыдущем примере применяются две директивы `@page`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="49b9e-147">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="49b9e-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="49b9e-148">Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="49b9e-149">Ограничения маршрута</span><span class="sxs-lookup"><span data-stu-id="49b9e-149">Route constraints</span></span>

<span data-ttu-id="49b9e-150">Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.</span><span class="sxs-lookup"><span data-stu-id="49b9e-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="49b9e-151">В следующем примере маршрут к компоненту `Users` соответствует только в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="49b9e-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="49b9e-152">в URL-адресе запроса имеется сегмент маршрута `Id`;</span><span class="sxs-lookup"><span data-stu-id="49b9e-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="49b9e-153">сегмент `Id` является целым числом (`int`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="49b9e-154">Доступны ограничения маршрутов, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="49b9e-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="49b9e-155">Сведения об ограничениях маршрута, соответствующих инвариантному языку и региональным параметрам, см. в предупреждении внизу таблицы.</span><span class="sxs-lookup"><span data-stu-id="49b9e-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="49b9e-156">Ограничение</span><span class="sxs-lookup"><span data-stu-id="49b9e-156">Constraint</span></span> | <span data-ttu-id="49b9e-157">Пример</span><span class="sxs-lookup"><span data-stu-id="49b9e-157">Example</span></span>           | <span data-ttu-id="49b9e-158">Примеры совпадений</span><span class="sxs-lookup"><span data-stu-id="49b9e-158">Example Matches</span></span>                                                                  | <span data-ttu-id="49b9e-159">Инвариант</span><span class="sxs-lookup"><span data-stu-id="49b9e-159">Invariant</span></span><br><span data-ttu-id="49b9e-160">язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="49b9e-160">culture</span></span><br><span data-ttu-id="49b9e-161">соответствие</span><span class="sxs-lookup"><span data-stu-id="49b9e-161">matching</span></span> |
| ---
<span data-ttu-id="49b9e-162">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-163">'Blazor'</span></span>
- <span data-ttu-id="49b9e-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-164">'Identity'</span></span>
- <span data-ttu-id="49b9e-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-166">'Razor'</span></span>
- <span data-ttu-id="49b9e-167">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-168">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-169">'Blazor'</span></span>
- <span data-ttu-id="49b9e-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-170">'Identity'</span></span>
- <span data-ttu-id="49b9e-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-172">'Razor'</span></span>
- <span data-ttu-id="49b9e-173">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-174">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-175">'Blazor'</span></span>
- <span data-ttu-id="49b9e-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-176">'Identity'</span></span>
- <span data-ttu-id="49b9e-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-178">'Razor'</span></span>
- <span data-ttu-id="49b9e-179">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-179">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-180">----- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-181">'Blazor'</span></span>
- <span data-ttu-id="49b9e-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-182">'Identity'</span></span>
- <span data-ttu-id="49b9e-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-184">'Razor'</span></span>
- <span data-ttu-id="49b9e-185">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-186">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-187">'Blazor'</span></span>
- <span data-ttu-id="49b9e-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-188">'Identity'</span></span>
- <span data-ttu-id="49b9e-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-190">'Razor'</span></span>
- <span data-ttu-id="49b9e-191">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-192">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-193">'Blazor'</span></span>
- <span data-ttu-id="49b9e-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-194">'Identity'</span></span>
- <span data-ttu-id="49b9e-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-196">'Razor'</span></span>
- <span data-ttu-id="49b9e-197">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-198">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-199">'Blazor'</span></span>
- <span data-ttu-id="49b9e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-200">'Identity'</span></span>
- <span data-ttu-id="49b9e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-202">'Razor'</span></span>
- <span data-ttu-id="49b9e-203">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-204">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-205">'Blazor'</span></span>
- <span data-ttu-id="49b9e-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-206">'Identity'</span></span>
- <span data-ttu-id="49b9e-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-208">'Razor'</span></span>
- <span data-ttu-id="49b9e-209">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-210">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-211">'Blazor'</span></span>
- <span data-ttu-id="49b9e-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-212">'Identity'</span></span>
- <span data-ttu-id="49b9e-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-214">'Razor'</span></span>
- <span data-ttu-id="49b9e-215">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-215">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-216">--------- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-217">'Blazor'</span></span>
- <span data-ttu-id="49b9e-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-218">'Identity'</span></span>
- <span data-ttu-id="49b9e-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-220">'Razor'</span></span>
- <span data-ttu-id="49b9e-221">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-222">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-223">'Blazor'</span></span>
- <span data-ttu-id="49b9e-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-224">'Identity'</span></span>
- <span data-ttu-id="49b9e-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-226">'Razor'</span></span>
- <span data-ttu-id="49b9e-227">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-228">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-229">'Blazor'</span></span>
- <span data-ttu-id="49b9e-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-230">'Identity'</span></span>
- <span data-ttu-id="49b9e-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-232">'Razor'</span></span>
- <span data-ttu-id="49b9e-233">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-234">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-235">'Blazor'</span></span>
- <span data-ttu-id="49b9e-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-236">'Identity'</span></span>
- <span data-ttu-id="49b9e-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-238">'Razor'</span></span>
- <span data-ttu-id="49b9e-239">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-240">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-241">'Blazor'</span></span>
- <span data-ttu-id="49b9e-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-242">'Identity'</span></span>
- <span data-ttu-id="49b9e-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-244">'Razor'</span></span>
- <span data-ttu-id="49b9e-245">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-246">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-247">'Blazor'</span></span>
- <span data-ttu-id="49b9e-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-248">'Identity'</span></span>
- <span data-ttu-id="49b9e-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-250">'Razor'</span></span>
- <span data-ttu-id="49b9e-251">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-252">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-253">'Blazor'</span></span>
- <span data-ttu-id="49b9e-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-254">'Identity'</span></span>
- <span data-ttu-id="49b9e-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-256">'Razor'</span></span>
- <span data-ttu-id="49b9e-257">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-258">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-259">'Blazor'</span></span>
- <span data-ttu-id="49b9e-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-260">'Identity'</span></span>
- <span data-ttu-id="49b9e-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-262">'Razor'</span></span>
- <span data-ttu-id="49b9e-263">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-264">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-265">'Blazor'</span></span>
- <span data-ttu-id="49b9e-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-266">'Identity'</span></span>
- <span data-ttu-id="49b9e-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-268">'Razor'</span></span>
- <span data-ttu-id="49b9e-269">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-270">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-271">'Blazor'</span></span>
- <span data-ttu-id="49b9e-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-272">'Identity'</span></span>
- <span data-ttu-id="49b9e-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-274">'Razor'</span></span>
- <span data-ttu-id="49b9e-275">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-276">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-277">'Blazor'</span></span>
- <span data-ttu-id="49b9e-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-278">'Identity'</span></span>
- <span data-ttu-id="49b9e-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-280">'Razor'</span></span>
- <span data-ttu-id="49b9e-281">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-282">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-283">'Blazor'</span></span>
- <span data-ttu-id="49b9e-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-284">'Identity'</span></span>
- <span data-ttu-id="49b9e-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-286">'Razor'</span></span>
- <span data-ttu-id="49b9e-287">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-288">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-289">'Blazor'</span></span>
- <span data-ttu-id="49b9e-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-290">'Identity'</span></span>
- <span data-ttu-id="49b9e-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-292">'Razor'</span></span>
- <span data-ttu-id="49b9e-293">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-294">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-295">'Blazor'</span></span>
- <span data-ttu-id="49b9e-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-296">'Identity'</span></span>
- <span data-ttu-id="49b9e-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-298">'Razor'</span></span>
- <span data-ttu-id="49b9e-299">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-300">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-301">'Blazor'</span></span>
- <span data-ttu-id="49b9e-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-302">'Identity'</span></span>
- <span data-ttu-id="49b9e-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-304">'Razor'</span></span>
- <span data-ttu-id="49b9e-305">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-306">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-307">'Blazor'</span></span>
- <span data-ttu-id="49b9e-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-308">'Identity'</span></span>
- <span data-ttu-id="49b9e-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-310">'Razor'</span></span>
- <span data-ttu-id="49b9e-311">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-312">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-313">'Blazor'</span></span>
- <span data-ttu-id="49b9e-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-314">'Identity'</span></span>
- <span data-ttu-id="49b9e-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-316">'Razor'</span></span>
- <span data-ttu-id="49b9e-317">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-318">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-319">'Blazor'</span></span>
- <span data-ttu-id="49b9e-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-320">'Identity'</span></span>
- <span data-ttu-id="49b9e-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-322">'Razor'</span></span>
- <span data-ttu-id="49b9e-323">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-324">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-325">'Blazor'</span></span>
- <span data-ttu-id="49b9e-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-326">'Identity'</span></span>
- <span data-ttu-id="49b9e-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-328">'Razor'</span></span>
- <span data-ttu-id="49b9e-329">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-330">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-331">'Blazor'</span></span>
- <span data-ttu-id="49b9e-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-332">'Identity'</span></span>
- <span data-ttu-id="49b9e-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-334">'Razor'</span></span>
- <span data-ttu-id="49b9e-335">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-336">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-337">'Blazor'</span></span>
- <span data-ttu-id="49b9e-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-338">'Identity'</span></span>
- <span data-ttu-id="49b9e-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-340">'Razor'</span></span>
- <span data-ttu-id="49b9e-341">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-342">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-343">'Blazor'</span></span>
- <span data-ttu-id="49b9e-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-344">'Identity'</span></span>
- <span data-ttu-id="49b9e-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-346">'Razor'</span></span>
- <span data-ttu-id="49b9e-347">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-348">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-349">'Blazor'</span></span>
- <span data-ttu-id="49b9e-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-350">'Identity'</span></span>
- <span data-ttu-id="49b9e-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-352">'Razor'</span></span>
- <span data-ttu-id="49b9e-353">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-354">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-355">'Blazor'</span></span>
- <span data-ttu-id="49b9e-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-356">'Identity'</span></span>
- <span data-ttu-id="49b9e-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-358">'Razor'</span></span>
- <span data-ttu-id="49b9e-359">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-360">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-361">'Blazor'</span></span>
- <span data-ttu-id="49b9e-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-362">'Identity'</span></span>
- <span data-ttu-id="49b9e-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-364">'Razor'</span></span>
- <span data-ttu-id="49b9e-365">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-366">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-367">'Blazor'</span></span>
- <span data-ttu-id="49b9e-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-368">'Identity'</span></span>
- <span data-ttu-id="49b9e-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-370">'Razor'</span></span>
- <span data-ttu-id="49b9e-371">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-372">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-373">'Blazor'</span></span>
- <span data-ttu-id="49b9e-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-374">'Identity'</span></span>
- <span data-ttu-id="49b9e-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-376">'Razor'</span></span>
- <span data-ttu-id="49b9e-377">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-378">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-379">'Blazor'</span></span>
- <span data-ttu-id="49b9e-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-380">'Identity'</span></span>
- <span data-ttu-id="49b9e-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-382">'Razor'</span></span>
- <span data-ttu-id="49b9e-383">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-384">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-385">'Blazor'</span></span>
- <span data-ttu-id="49b9e-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-386">'Identity'</span></span>
- <span data-ttu-id="49b9e-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-388">'Razor'</span></span>
- <span data-ttu-id="49b9e-389">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-390">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-391">'Blazor'</span></span>
- <span data-ttu-id="49b9e-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-392">'Identity'</span></span>
- <span data-ttu-id="49b9e-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-394">'Razor'</span></span>
- <span data-ttu-id="49b9e-395">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-396">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-397">'Blazor'</span></span>
- <span data-ttu-id="49b9e-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-398">'Identity'</span></span>
- <span data-ttu-id="49b9e-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-400">'Razor'</span></span>
- <span data-ttu-id="49b9e-401">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-402">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-403">'Blazor'</span></span>
- <span data-ttu-id="49b9e-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-404">'Identity'</span></span>
- <span data-ttu-id="49b9e-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-406">'Razor'</span></span>
- <span data-ttu-id="49b9e-407">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-408">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-409">'Blazor'</span></span>
- <span data-ttu-id="49b9e-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-410">'Identity'</span></span>
- <span data-ttu-id="49b9e-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-412">'Razor'</span></span>
- <span data-ttu-id="49b9e-413">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-414">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-415">'Blazor'</span></span>
- <span data-ttu-id="49b9e-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-416">'Identity'</span></span>
- <span data-ttu-id="49b9e-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-418">'Razor'</span></span>
- <span data-ttu-id="49b9e-419">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-420">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-421">'Blazor'</span></span>
- <span data-ttu-id="49b9e-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-422">'Identity'</span></span>
- <span data-ttu-id="49b9e-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-424">'Razor'</span></span>
- <span data-ttu-id="49b9e-425">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-426">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-427">'Blazor'</span></span>
- <span data-ttu-id="49b9e-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-428">'Identity'</span></span>
- <span data-ttu-id="49b9e-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-430">'Razor'</span></span>
- <span data-ttu-id="49b9e-431">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-432">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-433">'Blazor'</span></span>
- <span data-ttu-id="49b9e-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-434">'Identity'</span></span>
- <span data-ttu-id="49b9e-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-436">'Razor'</span></span>
- <span data-ttu-id="49b9e-437">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-438">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-439">'Blazor'</span></span>
- <span data-ttu-id="49b9e-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-440">'Identity'</span></span>
- <span data-ttu-id="49b9e-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-442">'Razor'</span></span>
- <span data-ttu-id="49b9e-443">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-443">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-444">---------------------------------------- | :--- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-445">'Blazor'</span></span>
- <span data-ttu-id="49b9e-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-446">'Identity'</span></span>
- <span data-ttu-id="49b9e-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-448">'Razor'</span></span>
- <span data-ttu-id="49b9e-449">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-450">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-451">'Blazor'</span></span>
- <span data-ttu-id="49b9e-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-452">'Identity'</span></span>
- <span data-ttu-id="49b9e-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-454">'Razor'</span></span>
- <span data-ttu-id="49b9e-455">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-456">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-457">'Blazor'</span></span>
- <span data-ttu-id="49b9e-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-458">'Identity'</span></span>
- <span data-ttu-id="49b9e-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-460">'Razor'</span></span>
- <span data-ttu-id="49b9e-461">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-462">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-463">'Blazor'</span></span>
- <span data-ttu-id="49b9e-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-464">'Identity'</span></span>
- <span data-ttu-id="49b9e-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-466">'Razor'</span></span>
- <span data-ttu-id="49b9e-467">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-468">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-469">'Blazor'</span></span>
- <span data-ttu-id="49b9e-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-470">'Identity'</span></span>
- <span data-ttu-id="49b9e-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-472">'Razor'</span></span>
- <span data-ttu-id="49b9e-473">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-474">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-475">'Blazor'</span></span>
- <span data-ttu-id="49b9e-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-476">'Identity'</span></span>
- <span data-ttu-id="49b9e-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-478">'Razor'</span></span>
- <span data-ttu-id="49b9e-479">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-480">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-481">'Blazor'</span></span>
- <span data-ttu-id="49b9e-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-482">'Identity'</span></span>
- <span data-ttu-id="49b9e-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-484">'Razor'</span></span>
- <span data-ttu-id="49b9e-485">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-486">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-487">'Blazor'</span></span>
- <span data-ttu-id="49b9e-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-488">'Identity'</span></span>
- <span data-ttu-id="49b9e-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-490">'Razor'</span></span>
- <span data-ttu-id="49b9e-491">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-492">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-493">'Blazor'</span></span>
- <span data-ttu-id="49b9e-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-494">'Identity'</span></span>
- <span data-ttu-id="49b9e-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-496">'Razor'</span></span>
- <span data-ttu-id="49b9e-497">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-498">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-499">'Blazor'</span></span>
- <span data-ttu-id="49b9e-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-500">'Identity'</span></span>
- <span data-ttu-id="49b9e-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-502">'Razor'</span></span>
- <span data-ttu-id="49b9e-503">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-504">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-505">'Blazor'</span></span>
- <span data-ttu-id="49b9e-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-506">'Identity'</span></span>
- <span data-ttu-id="49b9e-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-508">'Razor'</span></span>
- <span data-ttu-id="49b9e-509">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-510">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-511">'Blazor'</span></span>
- <span data-ttu-id="49b9e-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-512">'Identity'</span></span>
- <span data-ttu-id="49b9e-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-514">'Razor'</span></span>
- <span data-ttu-id="49b9e-515">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-516">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-517">'Blazor'</span></span>
- <span data-ttu-id="49b9e-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-518">'Identity'</span></span>
- <span data-ttu-id="49b9e-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-520">'Razor'</span></span>
- <span data-ttu-id="49b9e-521">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-521">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Нет                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Да                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Да                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Да                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Да                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Нет                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Да                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Да                              |</span><span class="sxs-lookup"><span data-stu-id="49b9e-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="49b9e-523">Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или <xref:System.DateTime>), всегда используют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="49b9e-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="49b9e-524">Эти ограничения предполагают, что URL-адрес является нелокализуемым.</span><span class="sxs-lookup"><span data-stu-id="49b9e-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="49b9e-525">Маршрутизация URL-адресов, содержащих точки</span><span class="sxs-lookup"><span data-stu-id="49b9e-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="49b9e-526">В приложениях Blazor Server маршрутом по умолчанию в *_Host.cshtml* является `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="49b9e-527">URL-адрес запроса, содержащий точку (`.`), не соответствует маршруту по умолчанию, так как такой адрес считается запросом файла.</span><span class="sxs-lookup"><span data-stu-id="49b9e-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="49b9e-528">Приложение Blazor возвращает ответ *404 Not Found* (Не найдено) для статического файла, который не существует.</span><span class="sxs-lookup"><span data-stu-id="49b9e-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="49b9e-529">Чтобы использовать маршруты, содержащие точку, добавьте в файл *_Host.cshtml* следующий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="49b9e-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="49b9e-530">Шаблон `"/{**path}"` включает в себя следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="49b9e-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="49b9e-531">двойная *универсальная* звездочка (`**`) для захвата пути через границы нескольких папок без кодирования косой чертой (`/`);</span><span class="sxs-lookup"><span data-stu-id="49b9e-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="49b9e-532">имя параметра маршрута `path`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="49b9e-533">Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="49b9e-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="49b9e-534">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="49b9e-535">Компонент NavLink</span><span class="sxs-lookup"><span data-stu-id="49b9e-535">NavLink component</span></span>

<span data-ttu-id="49b9e-536">Используйте при создании ссылок навигации компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> вместо HTML-элементов гиперссылок (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-536">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="49b9e-537">Компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="49b9e-537">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="49b9e-538">Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации.</span><span class="sxs-lookup"><span data-stu-id="49b9e-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="49b9e-539">Следующий компонент `NavMenu` создает панель навигации [Bootstrap](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов <xref:Microsoft.AspNetCore.Components.Routing.NavLink>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="49b9e-540">Существует два параметра <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>, которые можно назначить атрибуту `Match` элемента `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="49b9e-540">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="49b9e-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>. <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует всему текущему URL-адресу;</span><span class="sxs-lookup"><span data-stu-id="49b9e-541"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="49b9e-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*по умолчанию*). <xref:Microsoft.AspNetCore.Components.Routing.NavLink> активен, если он соответствует любому префиксу текущего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="49b9e-542"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="49b9e-543">В предыдущем примере <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-543">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="49b9e-544">Второй <xref:Microsoft.AspNetCore.Components.Routing.NavLink> получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `MyComponent` (например, `https://localhost:5001/MyComponent` и `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="49b9e-544">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="49b9e-545">Дополнительные атрибуты компонента <xref:Microsoft.AspNetCore.Components.Routing.NavLink> передаются в отображаемый тег привязки.</span><span class="sxs-lookup"><span data-stu-id="49b9e-545">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="49b9e-546">В следующем примере компонент <xref:Microsoft.AspNetCore.Components.Routing.NavLink> включает атрибут `target`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-546">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="49b9e-547">Отобразится следующая разметка HTML.</span><span class="sxs-lookup"><span data-stu-id="49b9e-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="49b9e-548">URI и вспомогательные инструменты состояния навигации</span><span class="sxs-lookup"><span data-stu-id="49b9e-548">URI and navigation state helpers</span></span>

<span data-ttu-id="49b9e-549">Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для работы с URI и навигацией в коде C#.</span><span class="sxs-lookup"><span data-stu-id="49b9e-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="49b9e-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> предоставляет события и методы, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="49b9e-550"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="49b9e-551">Член</span><span class="sxs-lookup"><span data-stu-id="49b9e-551">Member</span></span> | <span data-ttu-id="49b9e-552">Описание</span><span class="sxs-lookup"><span data-stu-id="49b9e-552">Description</span></span> |
| ---
<span data-ttu-id="49b9e-553">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-554">'Blazor'</span></span>
- <span data-ttu-id="49b9e-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-555">'Identity'</span></span>
- <span data-ttu-id="49b9e-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-557">'Razor'</span></span>
- <span data-ttu-id="49b9e-558">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-558">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-559">--- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-560">'Blazor'</span></span>
- <span data-ttu-id="49b9e-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-561">'Identity'</span></span>
- <span data-ttu-id="49b9e-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-563">'Razor'</span></span>
- <span data-ttu-id="49b9e-564">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-565">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-566">'Blazor'</span></span>
- <span data-ttu-id="49b9e-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-567">'Identity'</span></span>
- <span data-ttu-id="49b9e-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-569">'Razor'</span></span>
- <span data-ttu-id="49b9e-570">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="49b9e-571">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="49b9e-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="49b9e-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-572">'Blazor'</span></span>
- <span data-ttu-id="49b9e-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="49b9e-573">'Identity'</span></span>
- <span data-ttu-id="49b9e-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="49b9e-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="49b9e-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="49b9e-575">'Razor'</span></span>
- <span data-ttu-id="49b9e-576">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="49b9e-576">'SignalR' uid:</span></span> 

<span data-ttu-id="49b9e-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Возвращает текущий абсолютный URI.</span><span class="sxs-lookup"><span data-stu-id="49b9e-577">------ | | <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Gets the current absolute URI.</span></span> <span data-ttu-id="49b9e-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Возвращает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI.</span><span class="sxs-lookup"><span data-stu-id="49b9e-578">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="49b9e-579">Как правило, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> соответствует атрибуту `href` элемента документа `<base>` в *wwwroot/index.html* (Blazor WebAssembly) или *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="49b9e-579">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="49b9e-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Реализует переход по указанному URI.</span><span class="sxs-lookup"><span data-stu-id="49b9e-580">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigates to the specified URI.</span></span> <span data-ttu-id="49b9e-581">Если значение `forceLoad` равно `true`:</span><span class="sxs-lookup"><span data-stu-id="49b9e-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="49b9e-582">маршрутизация на стороне клиента обходится;</span><span class="sxs-lookup"><span data-stu-id="49b9e-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="49b9e-583">браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="49b9e-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="49b9e-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Событие, возникающее при изменении расположения навигации.</span><span class="sxs-lookup"><span data-stu-id="49b9e-584">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="49b9e-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Преобразует относительный URI в абсолютный.</span><span class="sxs-lookup"><span data-stu-id="49b9e-585">| | <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="49b9e-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Если указан базовый URI (например, URI, возвращенный ранее <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), преобразует абсолютный URI в URI относительно базового префикса.</span><span class="sxs-lookup"><span data-stu-id="49b9e-586">| | <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="49b9e-587">Следующий компонент при нажатии кнопки переходит к компоненту приложения `Counter`.</span><span class="sxs-lookup"><span data-stu-id="49b9e-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="49b9e-588">Следующий компонент обрабатывает событие изменения расположения путем определения <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-588">The following component handles a location changed event by setting <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="49b9e-589">При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется.</span><span class="sxs-lookup"><span data-stu-id="49b9e-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="49b9e-590">После отсоединения метода становится возможной сборка мусора для компонента.</span><span class="sxs-lookup"><span data-stu-id="49b9e-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="49b9e-591">В <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> содержатся следующие сведения о событии.</span><span class="sxs-lookup"><span data-stu-id="49b9e-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="49b9e-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>. URL-адрес нового расположения.</span><span class="sxs-lookup"><span data-stu-id="49b9e-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="49b9e-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>. Если `true`, Blazor перехватывает навигацию из браузера.</span><span class="sxs-lookup"><span data-stu-id="49b9e-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="49b9e-594">Если `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> приводит к переходу.</span><span class="sxs-lookup"><span data-stu-id="49b9e-594">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="49b9e-595">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="49b9e-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
