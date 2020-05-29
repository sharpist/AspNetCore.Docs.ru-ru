---
<span data-ttu-id="aac35-101">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-102">'Blazor'</span></span>
- <span data-ttu-id="aac35-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-103">'Identity'</span></span>
- <span data-ttu-id="aac35-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-105">'Razor'</span></span>
- <span data-ttu-id="aac35-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="aac35-107">Маршрутизация ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="aac35-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="aac35-108">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="aac35-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aac35-109">Узнайте, как маршрутизировать запросы и использовать компонент `NavLink` для создания навигационных ссылок в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="aac35-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="aac35-110">Интеграция маршрутизации конечных точек ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aac35-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="aac35-111"> Server интегрирован с функцией [маршрутизации конечных точек ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="aac35-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="aac35-112">Приложение ASP.NET Core настроено для приема входящих подключений для интерактивных компонентов с помощью `MapBlazorHub` в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="aac35-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="aac35-113">Наиболее типичная конфигурация — маршрутизация всех запросов на страницу Razor, которая выступает в качестве узла для серверной части приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aac35-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="aac35-114">По соглашению страница *узла* обычно называется *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aac35-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="aac35-115">Маршрут, указанный в файле узла, называется *резервным маршрутом*, так как он работает с низким приоритетом в соответствии с правилами маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="aac35-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="aac35-116">Резервный маршрут рассматривается, если другие маршруты не сопоставляются.</span><span class="sxs-lookup"><span data-stu-id="aac35-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="aac35-117">Это позволяет приложению использовать другие контроллеры и страницы, не мешая работе приложения Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="aac35-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="aac35-118">Шаблоны маршрутов</span><span class="sxs-lookup"><span data-stu-id="aac35-118">Route templates</span></span>

<span data-ttu-id="aac35-119">Компонент `Router` позволяет выполнять маршрутизацию для каждого компонента с указанным маршрутом.</span><span class="sxs-lookup"><span data-stu-id="aac35-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="aac35-120">Компонент `Router` находится в файле *App.razor*.</span><span class="sxs-lookup"><span data-stu-id="aac35-120">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="aac35-121">При компиляции файла *.razor* с директивой `@page` созданный класс предоставляет атрибут <xref:Microsoft.AspNetCore.Components.RouteAttribute> для указания шаблона маршрута.</span><span class="sxs-lookup"><span data-stu-id="aac35-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="aac35-122">В среде выполнения компонент `RouteView` выполняет следующие операции:</span><span class="sxs-lookup"><span data-stu-id="aac35-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="aac35-123">получает `RouteData` от `Router` вместе с всеми необходимыми параметрами;</span><span class="sxs-lookup"><span data-stu-id="aac35-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="aac35-124">визуализирует указанный компонент с его макетом (или необязательным макетом по умолчанию), используя указанные параметры.</span><span class="sxs-lookup"><span data-stu-id="aac35-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="aac35-125">При необходимости можно указать параметр `DefaultLayout` с классом макета, который будет использоваться для компонентов, которые не задают макет.</span><span class="sxs-lookup"><span data-stu-id="aac35-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="aac35-126">Шаблоны Blazor по умолчанию определяют компонент `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="aac35-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="aac35-127">Файл *MainLayout.razor* находится в папке *Shared* проекта шаблона.</span><span class="sxs-lookup"><span data-stu-id="aac35-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="aac35-128">Дополнительные сведения о макетах см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="aac35-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="aac35-129">К компоненту можно применить несколько шаблонов маршрутов.</span><span class="sxs-lookup"><span data-stu-id="aac35-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="aac35-130">Следующий компонент отвечает на запросы `/BlazorRoute` и `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="aac35-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="aac35-131">Для правильного разрешения URL-адресов приложение должно включать тег `<base>` в файл *wwwroot/index.html* (Blazor WebAssembly) или файл *Pages/_Host.cshtml* (Blazor Server) с базовым путем к приложению, указанным в атрибуте `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="aac35-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="aac35-132">Для получения дополнительной информации см. <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="aac35-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="aac35-133">Предоставление пользовательского содержимого, когда содержимое не найдено</span><span class="sxs-lookup"><span data-stu-id="aac35-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="aac35-134">Компонент `Router` позволяет приложению указать пользовательское содержимое, если содержимое для запрошенного маршрута не найдено.</span><span class="sxs-lookup"><span data-stu-id="aac35-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="aac35-135">В файле *App.razor* задайте пользовательское содержимое в параметре шаблона `NotFound` компонента `Router`.</span><span class="sxs-lookup"><span data-stu-id="aac35-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="aac35-136">Содержимое тегов `<NotFound>` может включать произвольные элементы, например другие интерактивные компоненты.</span><span class="sxs-lookup"><span data-stu-id="aac35-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="aac35-137">Сведения о применении макета по умолчанию к содержимому `NotFound` см. в разделе <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="aac35-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="aac35-138">Маршрутизация к компонентам из нескольких сборок</span><span class="sxs-lookup"><span data-stu-id="aac35-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="aac35-139">Используйте параметр `AdditionalAssemblies`, чтобы указать дополнительные сборки для компонента `Router`, которые следует учитывать при поиске маршрутизируемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="aac35-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="aac35-140">Указанные сборки рассматриваются в дополнение к сборке, указанной в `AppAssembly`.</span><span class="sxs-lookup"><span data-stu-id="aac35-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="aac35-141">В следующем примере `Component1` представляет собой маршрутизируемый компонент, определенный в упоминаемой библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="aac35-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="aac35-142">В следующем примере `AdditionalAssemblies` приводится поддержка маршрутизации для `Component1`.</span><span class="sxs-lookup"><span data-stu-id="aac35-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="aac35-143">Параметры маршрута</span><span class="sxs-lookup"><span data-stu-id="aac35-143">Route parameters</span></span>

<span data-ttu-id="aac35-144">Маршрутизатор использует параметры маршрута для заполнения соответствующих параметров компонента с тем же именем (без учета регистра).</span><span class="sxs-lookup"><span data-stu-id="aac35-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="aac35-145">Необязательные параметры не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="aac35-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="aac35-146">В предыдущем примере применяются две директивы `@page`.</span><span class="sxs-lookup"><span data-stu-id="aac35-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="aac35-147">Первая позволяет переходить к компоненту без параметра.</span><span class="sxs-lookup"><span data-stu-id="aac35-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="aac35-148">Вторая директива `@page` принимает параметр маршрута `{text}` и присваивает значение свойству `Text`.</span><span class="sxs-lookup"><span data-stu-id="aac35-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="aac35-149">Ограничения маршрута</span><span class="sxs-lookup"><span data-stu-id="aac35-149">Route constraints</span></span>

<span data-ttu-id="aac35-150">Ограничение маршрута применяет сопоставление типов в сегменте маршрута к компоненту.</span><span class="sxs-lookup"><span data-stu-id="aac35-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="aac35-151">В следующем примере маршрут к компоненту `Users` соответствует только в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="aac35-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="aac35-152">в URL-адресе запроса имеется сегмент маршрута `Id`;</span><span class="sxs-lookup"><span data-stu-id="aac35-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="aac35-153">сегмент `Id` является целым числом (`int`).</span><span class="sxs-lookup"><span data-stu-id="aac35-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="aac35-154">Доступны ограничения маршрутов, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="aac35-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="aac35-155">Сведения об ограничениях маршрута, соответствующих инвариантному языку и региональным параметрам, см. в предупреждении внизу таблицы.</span><span class="sxs-lookup"><span data-stu-id="aac35-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="aac35-156">Ограничение</span><span class="sxs-lookup"><span data-stu-id="aac35-156">Constraint</span></span> | <span data-ttu-id="aac35-157">Пример</span><span class="sxs-lookup"><span data-stu-id="aac35-157">Example</span></span>           | <span data-ttu-id="aac35-158">Примеры совпадений</span><span class="sxs-lookup"><span data-stu-id="aac35-158">Example Matches</span></span>                                                                  | <span data-ttu-id="aac35-159">Инвариант</span><span class="sxs-lookup"><span data-stu-id="aac35-159">Invariant</span></span><br><span data-ttu-id="aac35-160">язык и региональные параметры</span><span class="sxs-lookup"><span data-stu-id="aac35-160">culture</span></span><br><span data-ttu-id="aac35-161">соответствие</span><span class="sxs-lookup"><span data-stu-id="aac35-161">matching</span></span> |
| ---
<span data-ttu-id="aac35-162">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-163">'Blazor'</span></span>
- <span data-ttu-id="aac35-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-164">'Identity'</span></span>
- <span data-ttu-id="aac35-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-166">'Razor'</span></span>
- <span data-ttu-id="aac35-167">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-168">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-169">'Blazor'</span></span>
- <span data-ttu-id="aac35-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-170">'Identity'</span></span>
- <span data-ttu-id="aac35-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-172">'Razor'</span></span>
- <span data-ttu-id="aac35-173">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-174">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-175">'Blazor'</span></span>
- <span data-ttu-id="aac35-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-176">'Identity'</span></span>
- <span data-ttu-id="aac35-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-178">'Razor'</span></span>
- <span data-ttu-id="aac35-179">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-179">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-180">----- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-181">'Blazor'</span></span>
- <span data-ttu-id="aac35-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-182">'Identity'</span></span>
- <span data-ttu-id="aac35-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-184">'Razor'</span></span>
- <span data-ttu-id="aac35-185">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-186">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-187">'Blazor'</span></span>
- <span data-ttu-id="aac35-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-188">'Identity'</span></span>
- <span data-ttu-id="aac35-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-190">'Razor'</span></span>
- <span data-ttu-id="aac35-191">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-192">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-193">'Blazor'</span></span>
- <span data-ttu-id="aac35-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-194">'Identity'</span></span>
- <span data-ttu-id="aac35-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-196">'Razor'</span></span>
- <span data-ttu-id="aac35-197">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-198">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-199">'Blazor'</span></span>
- <span data-ttu-id="aac35-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-200">'Identity'</span></span>
- <span data-ttu-id="aac35-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-202">'Razor'</span></span>
- <span data-ttu-id="aac35-203">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-204">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-205">'Blazor'</span></span>
- <span data-ttu-id="aac35-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-206">'Identity'</span></span>
- <span data-ttu-id="aac35-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-208">'Razor'</span></span>
- <span data-ttu-id="aac35-209">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-210">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-211">'Blazor'</span></span>
- <span data-ttu-id="aac35-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-212">'Identity'</span></span>
- <span data-ttu-id="aac35-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-214">'Razor'</span></span>
- <span data-ttu-id="aac35-215">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-215">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-216">--------- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-217">'Blazor'</span></span>
- <span data-ttu-id="aac35-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-218">'Identity'</span></span>
- <span data-ttu-id="aac35-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-220">'Razor'</span></span>
- <span data-ttu-id="aac35-221">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-222">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-223">'Blazor'</span></span>
- <span data-ttu-id="aac35-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-224">'Identity'</span></span>
- <span data-ttu-id="aac35-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-226">'Razor'</span></span>
- <span data-ttu-id="aac35-227">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-228">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-229">'Blazor'</span></span>
- <span data-ttu-id="aac35-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-230">'Identity'</span></span>
- <span data-ttu-id="aac35-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-232">'Razor'</span></span>
- <span data-ttu-id="aac35-233">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-234">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-235">'Blazor'</span></span>
- <span data-ttu-id="aac35-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-236">'Identity'</span></span>
- <span data-ttu-id="aac35-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-238">'Razor'</span></span>
- <span data-ttu-id="aac35-239">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-240">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-241">'Blazor'</span></span>
- <span data-ttu-id="aac35-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-242">'Identity'</span></span>
- <span data-ttu-id="aac35-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-244">'Razor'</span></span>
- <span data-ttu-id="aac35-245">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-246">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-247">'Blazor'</span></span>
- <span data-ttu-id="aac35-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-248">'Identity'</span></span>
- <span data-ttu-id="aac35-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-250">'Razor'</span></span>
- <span data-ttu-id="aac35-251">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-252">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-253">'Blazor'</span></span>
- <span data-ttu-id="aac35-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-254">'Identity'</span></span>
- <span data-ttu-id="aac35-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-256">'Razor'</span></span>
- <span data-ttu-id="aac35-257">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-258">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-259">'Blazor'</span></span>
- <span data-ttu-id="aac35-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-260">'Identity'</span></span>
- <span data-ttu-id="aac35-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-262">'Razor'</span></span>
- <span data-ttu-id="aac35-263">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-264">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-265">'Blazor'</span></span>
- <span data-ttu-id="aac35-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-266">'Identity'</span></span>
- <span data-ttu-id="aac35-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-268">'Razor'</span></span>
- <span data-ttu-id="aac35-269">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-270">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-271">'Blazor'</span></span>
- <span data-ttu-id="aac35-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-272">'Identity'</span></span>
- <span data-ttu-id="aac35-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-274">'Razor'</span></span>
- <span data-ttu-id="aac35-275">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-276">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-277">'Blazor'</span></span>
- <span data-ttu-id="aac35-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-278">'Identity'</span></span>
- <span data-ttu-id="aac35-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-280">'Razor'</span></span>
- <span data-ttu-id="aac35-281">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-282">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-283">'Blazor'</span></span>
- <span data-ttu-id="aac35-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-284">'Identity'</span></span>
- <span data-ttu-id="aac35-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-286">'Razor'</span></span>
- <span data-ttu-id="aac35-287">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-288">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-289">'Blazor'</span></span>
- <span data-ttu-id="aac35-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-290">'Identity'</span></span>
- <span data-ttu-id="aac35-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-292">'Razor'</span></span>
- <span data-ttu-id="aac35-293">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-294">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-295">'Blazor'</span></span>
- <span data-ttu-id="aac35-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-296">'Identity'</span></span>
- <span data-ttu-id="aac35-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-298">'Razor'</span></span>
- <span data-ttu-id="aac35-299">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-300">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-301">'Blazor'</span></span>
- <span data-ttu-id="aac35-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-302">'Identity'</span></span>
- <span data-ttu-id="aac35-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-304">'Razor'</span></span>
- <span data-ttu-id="aac35-305">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-306">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-307">'Blazor'</span></span>
- <span data-ttu-id="aac35-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-308">'Identity'</span></span>
- <span data-ttu-id="aac35-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-310">'Razor'</span></span>
- <span data-ttu-id="aac35-311">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-312">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-313">'Blazor'</span></span>
- <span data-ttu-id="aac35-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-314">'Identity'</span></span>
- <span data-ttu-id="aac35-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-316">'Razor'</span></span>
- <span data-ttu-id="aac35-317">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-318">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-319">'Blazor'</span></span>
- <span data-ttu-id="aac35-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-320">'Identity'</span></span>
- <span data-ttu-id="aac35-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-322">'Razor'</span></span>
- <span data-ttu-id="aac35-323">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-324">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-325">'Blazor'</span></span>
- <span data-ttu-id="aac35-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-326">'Identity'</span></span>
- <span data-ttu-id="aac35-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-328">'Razor'</span></span>
- <span data-ttu-id="aac35-329">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-330">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-331">'Blazor'</span></span>
- <span data-ttu-id="aac35-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-332">'Identity'</span></span>
- <span data-ttu-id="aac35-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-334">'Razor'</span></span>
- <span data-ttu-id="aac35-335">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-336">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-337">'Blazor'</span></span>
- <span data-ttu-id="aac35-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-338">'Identity'</span></span>
- <span data-ttu-id="aac35-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-340">'Razor'</span></span>
- <span data-ttu-id="aac35-341">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-342">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-343">'Blazor'</span></span>
- <span data-ttu-id="aac35-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-344">'Identity'</span></span>
- <span data-ttu-id="aac35-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-346">'Razor'</span></span>
- <span data-ttu-id="aac35-347">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-348">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-349">'Blazor'</span></span>
- <span data-ttu-id="aac35-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-350">'Identity'</span></span>
- <span data-ttu-id="aac35-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-352">'Razor'</span></span>
- <span data-ttu-id="aac35-353">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-354">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-355">'Blazor'</span></span>
- <span data-ttu-id="aac35-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-356">'Identity'</span></span>
- <span data-ttu-id="aac35-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-358">'Razor'</span></span>
- <span data-ttu-id="aac35-359">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-360">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-361">'Blazor'</span></span>
- <span data-ttu-id="aac35-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-362">'Identity'</span></span>
- <span data-ttu-id="aac35-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-364">'Razor'</span></span>
- <span data-ttu-id="aac35-365">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-366">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-367">'Blazor'</span></span>
- <span data-ttu-id="aac35-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-368">'Identity'</span></span>
- <span data-ttu-id="aac35-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-370">'Razor'</span></span>
- <span data-ttu-id="aac35-371">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-372">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-373">'Blazor'</span></span>
- <span data-ttu-id="aac35-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-374">'Identity'</span></span>
- <span data-ttu-id="aac35-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-376">'Razor'</span></span>
- <span data-ttu-id="aac35-377">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-378">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-379">'Blazor'</span></span>
- <span data-ttu-id="aac35-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-380">'Identity'</span></span>
- <span data-ttu-id="aac35-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-382">'Razor'</span></span>
- <span data-ttu-id="aac35-383">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-384">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-385">'Blazor'</span></span>
- <span data-ttu-id="aac35-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-386">'Identity'</span></span>
- <span data-ttu-id="aac35-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-388">'Razor'</span></span>
- <span data-ttu-id="aac35-389">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-390">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-391">'Blazor'</span></span>
- <span data-ttu-id="aac35-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-392">'Identity'</span></span>
- <span data-ttu-id="aac35-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-394">'Razor'</span></span>
- <span data-ttu-id="aac35-395">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-396">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-397">'Blazor'</span></span>
- <span data-ttu-id="aac35-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-398">'Identity'</span></span>
- <span data-ttu-id="aac35-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-400">'Razor'</span></span>
- <span data-ttu-id="aac35-401">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-402">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-403">'Blazor'</span></span>
- <span data-ttu-id="aac35-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-404">'Identity'</span></span>
- <span data-ttu-id="aac35-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-406">'Razor'</span></span>
- <span data-ttu-id="aac35-407">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-408">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-409">'Blazor'</span></span>
- <span data-ttu-id="aac35-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-410">'Identity'</span></span>
- <span data-ttu-id="aac35-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-412">'Razor'</span></span>
- <span data-ttu-id="aac35-413">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-414">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-415">'Blazor'</span></span>
- <span data-ttu-id="aac35-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-416">'Identity'</span></span>
- <span data-ttu-id="aac35-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-418">'Razor'</span></span>
- <span data-ttu-id="aac35-419">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-420">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-421">'Blazor'</span></span>
- <span data-ttu-id="aac35-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-422">'Identity'</span></span>
- <span data-ttu-id="aac35-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-424">'Razor'</span></span>
- <span data-ttu-id="aac35-425">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-426">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-427">'Blazor'</span></span>
- <span data-ttu-id="aac35-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-428">'Identity'</span></span>
- <span data-ttu-id="aac35-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-430">'Razor'</span></span>
- <span data-ttu-id="aac35-431">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-432">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-433">'Blazor'</span></span>
- <span data-ttu-id="aac35-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-434">'Identity'</span></span>
- <span data-ttu-id="aac35-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-436">'Razor'</span></span>
- <span data-ttu-id="aac35-437">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-438">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-439">'Blazor'</span></span>
- <span data-ttu-id="aac35-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-440">'Identity'</span></span>
- <span data-ttu-id="aac35-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-442">'Razor'</span></span>
- <span data-ttu-id="aac35-443">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-443">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-444">---------------------------------------- | :--- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-445">'Blazor'</span></span>
- <span data-ttu-id="aac35-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-446">'Identity'</span></span>
- <span data-ttu-id="aac35-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-448">'Razor'</span></span>
- <span data-ttu-id="aac35-449">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-450">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-451">'Blazor'</span></span>
- <span data-ttu-id="aac35-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-452">'Identity'</span></span>
- <span data-ttu-id="aac35-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-454">'Razor'</span></span>
- <span data-ttu-id="aac35-455">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-456">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-457">'Blazor'</span></span>
- <span data-ttu-id="aac35-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-458">'Identity'</span></span>
- <span data-ttu-id="aac35-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-460">'Razor'</span></span>
- <span data-ttu-id="aac35-461">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-462">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-463">'Blazor'</span></span>
- <span data-ttu-id="aac35-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-464">'Identity'</span></span>
- <span data-ttu-id="aac35-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-466">'Razor'</span></span>
- <span data-ttu-id="aac35-467">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-468">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-469">'Blazor'</span></span>
- <span data-ttu-id="aac35-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-470">'Identity'</span></span>
- <span data-ttu-id="aac35-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-472">'Razor'</span></span>
- <span data-ttu-id="aac35-473">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-474">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-475">'Blazor'</span></span>
- <span data-ttu-id="aac35-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-476">'Identity'</span></span>
- <span data-ttu-id="aac35-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-478">'Razor'</span></span>
- <span data-ttu-id="aac35-479">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-480">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-481">'Blazor'</span></span>
- <span data-ttu-id="aac35-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-482">'Identity'</span></span>
- <span data-ttu-id="aac35-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-484">'Razor'</span></span>
- <span data-ttu-id="aac35-485">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-486">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-487">'Blazor'</span></span>
- <span data-ttu-id="aac35-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-488">'Identity'</span></span>
- <span data-ttu-id="aac35-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-490">'Razor'</span></span>
- <span data-ttu-id="aac35-491">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-492">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-493">'Blazor'</span></span>
- <span data-ttu-id="aac35-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-494">'Identity'</span></span>
- <span data-ttu-id="aac35-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-496">'Razor'</span></span>
- <span data-ttu-id="aac35-497">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-498">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-499">'Blazor'</span></span>
- <span data-ttu-id="aac35-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-500">'Identity'</span></span>
- <span data-ttu-id="aac35-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-502">'Razor'</span></span>
- <span data-ttu-id="aac35-503">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-504">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-505">'Blazor'</span></span>
- <span data-ttu-id="aac35-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-506">'Identity'</span></span>
- <span data-ttu-id="aac35-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-508">'Razor'</span></span>
- <span data-ttu-id="aac35-509">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-510">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-511">'Blazor'</span></span>
- <span data-ttu-id="aac35-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-512">'Identity'</span></span>
- <span data-ttu-id="aac35-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-514">'Razor'</span></span>
- <span data-ttu-id="aac35-515">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-516">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-517">'Blazor'</span></span>
- <span data-ttu-id="aac35-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-518">'Identity'</span></span>
- <span data-ttu-id="aac35-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-520">'Razor'</span></span>
- <span data-ttu-id="aac35-521">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-521">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Нет                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Да                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Да                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Да                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Да                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Нет                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Да                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Да                              |</span><span class="sxs-lookup"><span data-stu-id="aac35-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="aac35-523">Ограничения маршрута, которые проверяют URL-адрес и могут быть преобразованы в тип CLR (например, `int` или `DateTime`), всегда используют инвариантные язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="aac35-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="aac35-524">Эти ограничения предполагают, что URL-адрес является нелокализуемым.</span><span class="sxs-lookup"><span data-stu-id="aac35-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="aac35-525">Маршрутизация URL-адресов, содержащих точки</span><span class="sxs-lookup"><span data-stu-id="aac35-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="aac35-526">В приложениях Blazor Server маршрутом по умолчанию в *_Host.cshtml* является `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="aac35-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="aac35-527">URL-адрес запроса, содержащий точку (`.`), не соответствует маршруту по умолчанию, так как такой адрес считается запросом файла.</span><span class="sxs-lookup"><span data-stu-id="aac35-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="aac35-528">Приложение Blazor возвращает ответ *404 Not Found* (Не найдено) для статического файла, который не существует.</span><span class="sxs-lookup"><span data-stu-id="aac35-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="aac35-529">Чтобы использовать маршруты, содержащие точку, добавьте в файл *_Host.cshtml* следующий шаблон маршрута.</span><span class="sxs-lookup"><span data-stu-id="aac35-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="aac35-530">Шаблон `"/{**path}"` включает в себя следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="aac35-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="aac35-531">двойная *универсальная* звездочка (`**`) для захвата пути через границы нескольких папок без кодирования косой чертой (`/`);</span><span class="sxs-lookup"><span data-stu-id="aac35-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="aac35-532">имя параметра маршрута `path`.</span><span class="sxs-lookup"><span data-stu-id="aac35-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="aac35-533">Синтаксис *универсального* параметра (`*`/`**`) **не** поддерживается в компонентах Razor ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="aac35-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="aac35-534">Для получения дополнительной информации см. <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="aac35-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="aac35-535">Компонент NavLink</span><span class="sxs-lookup"><span data-stu-id="aac35-535">NavLink component</span></span>

<span data-ttu-id="aac35-536">Используйте при создании ссылок навигации компонент `NavLink` вместо HTML-элементов гиперссылок (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="aac35-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="aac35-537">Компонент `NavLink` ведет себя как элемент `<a>`, за исключением того, что он переключает класс CSS `active` в зависимости от того, соответствует ли его `href` текущему URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="aac35-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="aac35-538">Класс `active` помогает пользователю понять, какая страница является активной страницей среди отображаемых ссылок навигации.</span><span class="sxs-lookup"><span data-stu-id="aac35-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="aac35-539">Следующий компонент `NavMenu` создает панель навигации [Bootstrap](https://getbootstrap.com/docs/), которая демонстрирует использование компонентов `NavLink`.</span><span class="sxs-lookup"><span data-stu-id="aac35-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="aac35-540">Существует два параметра `NavLinkMatch`, которые можно назначить атрибуту `Match` элемента `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="aac35-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="aac35-541">`NavLinkMatch.All` &ndash; `NavLink` активен, если он соответствует всему текущему URL-адресу;</span><span class="sxs-lookup"><span data-stu-id="aac35-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="aac35-542">`NavLinkMatch.Prefix` (*по умолчанию*) &ndash; `NavLink` активен, если он соответствует любому префиксу текущего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="aac35-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="aac35-543">В предыдущем примере `NavLink` `href=""` элемента Home соответствует домашнему URL-адресу и получает только класс CSS `active` в URL-адресе базового пути приложения по умолчанию (например, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="aac35-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="aac35-544">Второй `NavLink` получает класс `active`, когда пользователь посещает любой URL-адрес с префиксом `MyComponent` (например, `https://localhost:5001/MyComponent` и `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="aac35-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="aac35-545">Дополнительные атрибуты компонента `NavLink` передаются в отображаемый тег привязки.</span><span class="sxs-lookup"><span data-stu-id="aac35-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="aac35-546">В следующем примере компонент `NavLink` включает атрибут `target`.</span><span class="sxs-lookup"><span data-stu-id="aac35-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="aac35-547">Отобразится следующая разметка HTML.</span><span class="sxs-lookup"><span data-stu-id="aac35-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="aac35-548">URI и вспомогательные инструменты состояния навигации</span><span class="sxs-lookup"><span data-stu-id="aac35-548">URI and navigation state helpers</span></span>

<span data-ttu-id="aac35-549">Используйте <xref:Microsoft.AspNetCore.Components.NavigationManager> для работы с URI и навигацией в коде C#.</span><span class="sxs-lookup"><span data-stu-id="aac35-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="aac35-550">`NavigationManager` предоставляет события и методы, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="aac35-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="aac35-551">Член</span><span class="sxs-lookup"><span data-stu-id="aac35-551">Member</span></span> | <span data-ttu-id="aac35-552">Описание</span><span class="sxs-lookup"><span data-stu-id="aac35-552">Description</span></span> |
| ---
<span data-ttu-id="aac35-553">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-554">'Blazor'</span></span>
- <span data-ttu-id="aac35-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-555">'Identity'</span></span>
- <span data-ttu-id="aac35-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-557">'Razor'</span></span>
- <span data-ttu-id="aac35-558">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-558">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-559">--- | --- название: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-560">'Blazor'</span></span>
- <span data-ttu-id="aac35-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-561">'Identity'</span></span>
- <span data-ttu-id="aac35-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-563">'Razor'</span></span>
- <span data-ttu-id="aac35-564">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-565">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-566">'Blazor'</span></span>
- <span data-ttu-id="aac35-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-567">'Identity'</span></span>
- <span data-ttu-id="aac35-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-569">'Razor'</span></span>
- <span data-ttu-id="aac35-570">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="aac35-571">title: Маршрутизация ASP.NET Core Blazor автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="aac35-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="aac35-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="aac35-572">'Blazor'</span></span>
- <span data-ttu-id="aac35-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="aac35-573">'Identity'</span></span>
- <span data-ttu-id="aac35-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="aac35-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="aac35-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="aac35-575">'Razor'</span></span>
- <span data-ttu-id="aac35-576">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="aac35-576">'SignalR' uid:</span></span> 

<span data-ttu-id="aac35-577">------ | | Uri | Возвращает текущий абсолютный URI.</span><span class="sxs-lookup"><span data-stu-id="aac35-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="aac35-578">| | BaseUri | Возвращает базовый URI (с завершающей косой чертой), который можно добавить в начало относительных путей URI для получения абсолютного URI.</span><span class="sxs-lookup"><span data-stu-id="aac35-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="aac35-579">Как правило, `BaseUri` соответствует атрибуту `href` элемента документа `<base>` в *wwwroot/index.html* (Blazor WebAssembly) или *Pages/_Host.cshtml* (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="aac35-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="aac35-580">| | NavigateTo | Переходит по указанному URI.</span><span class="sxs-lookup"><span data-stu-id="aac35-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="aac35-581">Если значение `forceLoad` равно `true`:</span><span class="sxs-lookup"><span data-stu-id="aac35-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="aac35-582">маршрутизация на стороне клиента обходится;</span><span class="sxs-lookup"><span data-stu-id="aac35-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="aac35-583">браузер принуждается к загрузке новой страницы с сервера, независимо от того, обрабатывается ли универсальный код ресурса клиентским маршрутизатором.</span><span class="sxs-lookup"><span data-stu-id="aac35-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="aac35-584">| | LocationChanged | Событие, возникающее при изменении расположения в структуре навигации.</span><span class="sxs-lookup"><span data-stu-id="aac35-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="aac35-585">| | ToAbsoluteUri | Преобразует относительный URI в абсолютный.</span><span class="sxs-lookup"><span data-stu-id="aac35-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="aac35-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Если указан базовый URI (например, URI, возвращенный `GetBaseUri` ранее), этот элемент преобразует абсолютный URI в URI относительно базового префикса.</span><span class="sxs-lookup"><span data-stu-id="aac35-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="aac35-587">Следующий компонент при нажатии кнопки переходит к компоненту приложения `Counter`.</span><span class="sxs-lookup"><span data-stu-id="aac35-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="aac35-588">Следующий компонент обрабатывает событие изменения расположения.</span><span class="sxs-lookup"><span data-stu-id="aac35-588">The following component handles a location changed event.</span></span> <span data-ttu-id="aac35-589">При вызове `Dispose` платформой метод `HandleLocationChanged` отсоединяется.</span><span class="sxs-lookup"><span data-stu-id="aac35-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="aac35-590">После отсоединения метода становится возможной сборка мусора для компонента.</span><span class="sxs-lookup"><span data-stu-id="aac35-590">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="aac35-591">В <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> содержатся следующие сведения о событии.</span><span class="sxs-lookup"><span data-stu-id="aac35-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="aac35-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; URL-адрес нового расположения.</span><span class="sxs-lookup"><span data-stu-id="aac35-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="aac35-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; Если `true`, Blazor перехватывает навигацию из браузера.</span><span class="sxs-lookup"><span data-stu-id="aac35-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="aac35-594">Если `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) приводит к переходу.</span><span class="sxs-lookup"><span data-stu-id="aac35-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="aac35-595">Дополнительные сведения об удалении компонентов см. в разделе <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="aac35-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
