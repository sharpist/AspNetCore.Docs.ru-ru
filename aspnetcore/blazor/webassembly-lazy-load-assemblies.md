---
title: 'Сборки с отложенной загрузкой в ASP.NET Core :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: Узнайте, как выполнять отложенную загрузку сборок в приложениях ASP.NET Core :::no-loc(Blazor WebAssembly):::.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6a1feffb5341d432d6d1949a9e26b9537b85ba03
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054792"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="39d09-103">Сборки с отложенной загрузкой в ASP.NET Core :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="39d09-103">Lazy load assemblies in ASP.NET Core :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="39d09-104">Автор: Сэфиа Абдалла ([Safia Abdalla](https://safia.rocks)) и Люк Лэтэм ([Luke Latham](https://github.com/guardrex))</span><span class="sxs-lookup"><span data-stu-id="39d09-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="39d09-105">Производительность запуска приложения :::no-loc(Blazor WebAssembly)::: можно повысить, отложив загрузку некоторых сборок приложений, пока они не потребуются — это называется *отложенной загрузкой*.</span><span class="sxs-lookup"><span data-stu-id="39d09-105">:::no-loc(Blazor WebAssembly)::: app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="39d09-106">Например, сборки, которые используются только для отрисовки лишь одного компонента, могут быть настроены на загрузку только в том случае, если пользователь переходит к этому компоненту.</span><span class="sxs-lookup"><span data-stu-id="39d09-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="39d09-107">После загрузки сборки кэшируются на стороне клиента и доступны для всех последующих переходов.</span><span class="sxs-lookup"><span data-stu-id="39d09-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="39d09-108">Функция отложенной загрузки :::no-loc(Blazor)::: позволяет помечать сборки приложений для отложенной загрузки, чтобы загружать их во время выполнения, когда пользователь переходит по определенному маршруту.</span><span class="sxs-lookup"><span data-stu-id="39d09-108">:::no-loc(Blazor):::'s lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="39d09-109">Эта функция содержит изменения в файле проекта и изменения в маршрутизаторе приложения.</span><span class="sxs-lookup"><span data-stu-id="39d09-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="39d09-110">Отложенная загрузка сборки ничего не даст приложениям :::no-loc(Blazor Server):::, так как сборки не загружаются на клиент в приложении :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="39d09-110">Assembly lazy loading doesn't benefit :::no-loc(Blazor Server)::: apps because assemblies aren't downloaded to the client in a :::no-loc(Blazor Server)::: app.</span></span>

## <a name="project-file"></a><span data-ttu-id="39d09-111">Файл проекта</span><span class="sxs-lookup"><span data-stu-id="39d09-111">Project file</span></span>

<span data-ttu-id="39d09-112">Пометьте сборки для отложенной загрузки в файле проекта приложения (`.csproj`) с помощью элемента `:::no-loc(Blazor):::WebAssemblyLazyLoad`.</span><span class="sxs-lookup"><span data-stu-id="39d09-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `:::no-loc(Blazor):::WebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="39d09-113">Используйте имя сборки с расширением `.dll`.</span><span class="sxs-lookup"><span data-stu-id="39d09-113">Use the assembly name with the `.dll` extension.</span></span> <span data-ttu-id="39d09-114">Платформа :::no-loc(Blazor)::: предотвращает загрузку сборок, заданных этой группой элементов, при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="39d09-114">The :::no-loc(Blazor)::: framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="39d09-115">Следующий пример помечает большую пользовательскую сборку (`GrantImaharaRobotControls.dll`) для отложенной загрузки.</span><span class="sxs-lookup"><span data-stu-id="39d09-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="39d09-116">Если сборка, помеченная для отложенной загрузки, содержит зависимости, они также должны быть помечены для отложенной загрузки в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="39d09-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <:::no-loc(Blazor):::WebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="39d09-117">`Router`</span><span class="sxs-lookup"><span data-stu-id="39d09-117">`Router` component</span></span>

<span data-ttu-id="39d09-118">Компонент `Router` в :::no-loc(Blazor)::: определяет, какие сборки :::no-loc(Blazor)::: требуют компоненты, поддерживающие маршрутизацию.</span><span class="sxs-lookup"><span data-stu-id="39d09-118">:::no-loc(Blazor):::'s `Router` component designates which assemblies :::no-loc(Blazor)::: searches for routable components.</span></span> <span data-ttu-id="39d09-119">Компонент `Router` также отвечает за рендеринг компонента для маршрута, по которому переходит пользователь.</span><span class="sxs-lookup"><span data-stu-id="39d09-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="39d09-120">Компонент `Router` поддерживает функцию `OnNavigateAsync`, которую можно использовать в сочетании с отложенной загрузкой.</span><span class="sxs-lookup"><span data-stu-id="39d09-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="39d09-121">В компоненте `Router` приложения (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="39d09-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="39d09-122">Добавьте обратный вызов `OnNavigateAsync`.</span><span class="sxs-lookup"><span data-stu-id="39d09-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="39d09-123">Обработчик `OnNavigateAsync` вызывается, когда пользователь выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="39d09-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="39d09-124">попадает на маршрут в первый раз, перейдя к нему непосредственно из браузера;</span><span class="sxs-lookup"><span data-stu-id="39d09-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="39d09-125">переходит на новый маршрут с помощью ссылки или вызова <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="39d09-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="39d09-126">Если сборки с отложенной загрузкой содержат маршрутизируемые компоненты, добавьте в компонент [Список](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (например, с именем `lazyLoadedAssemblies`).</span><span class="sxs-lookup"><span data-stu-id="39d09-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="39d09-127">Сборки передаются обратно в коллекцию <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>, если они содержат маршрутизируемые компоненты.</span><span class="sxs-lookup"><span data-stu-id="39d09-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="39d09-128">Платформа выполняет поиск маршрутов в сборках и обновляет коллекцию маршрутов при обнаружении новых маршрутов.</span><span class="sxs-lookup"><span data-stu-id="39d09-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="39d09-129">Если обратный вызов `OnNavigateAsync` создает необработанное исключение, вызывается [пользовательский интерфейс ошибок :::no-loc(Blazor):::](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="39d09-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [:::no-loc(Blazor)::: error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="39d09-130">Логика загрузки сборок в `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="39d09-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="39d09-131">`OnNavigateAsync` имеет параметр `NavigationContext`, который предоставляет сведения о текущем асинхронном событии навигации, включая целевой путь (`Path`) и токен отмены (`CancellationToken`):</span><span class="sxs-lookup"><span data-stu-id="39d09-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="39d09-132">Свойство `Path` — это путь назначения пользователя относительно базового пути приложения, например `/robot`.</span><span class="sxs-lookup"><span data-stu-id="39d09-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="39d09-133">`CancellationToken` можно использовать для отслеживания отмены асинхронной задачи.</span><span class="sxs-lookup"><span data-stu-id="39d09-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="39d09-134">`OnNavigateAsync` автоматически отменяет текущую выполняемую задачу навигации, когда пользователь переходит на другую страницу.</span><span class="sxs-lookup"><span data-stu-id="39d09-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="39d09-135">В `OnNavigateAsync` реализуйте логику для определения загружаемых сборок.</span><span class="sxs-lookup"><span data-stu-id="39d09-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="39d09-136">Доступны следующие варианты:</span><span class="sxs-lookup"><span data-stu-id="39d09-136">Options include:</span></span>

* <span data-ttu-id="39d09-137">Условные проверки в методе `OnNavigateAsync`.</span><span class="sxs-lookup"><span data-stu-id="39d09-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="39d09-138">Таблица подстановки сопоставляет маршруты с именами сборок, которые либо вставляются в компонент, либо реализуются в блоке [`@code`](xref:mvc/views/razor#code).</span><span class="sxs-lookup"><span data-stu-id="39d09-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="39d09-139">`LazyAssemblyLoader` — предоставляемая платформой отдельная служба для загрузки сборок.</span><span class="sxs-lookup"><span data-stu-id="39d09-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="39d09-140">Вставка `LazyAssemblyLoader` в компонент `Router`:</span><span class="sxs-lookup"><span data-stu-id="39d09-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="39d09-141">`LazyAssemblyLoader` предоставляет метод `LoadAssembliesAsync`, который:</span><span class="sxs-lookup"><span data-stu-id="39d09-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="39d09-142">Использует взаимодействие JS для выборки сборок через сетевой вызов.</span><span class="sxs-lookup"><span data-stu-id="39d09-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="39d09-143">Загружает сборки в среду выполнения в WebAssembly в браузере.</span><span class="sxs-lookup"><span data-stu-id="39d09-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="39d09-144">Реализация отложенной загрузки на платформе поддерживает отложенную загрузку с предварительной отрисовкой в размещенном решении :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="39d09-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="39d09-145">Во время предварительной отрисовки предполагается, что будут загружены все сборки, включая те, которые отмечены для отложенной загрузки.</span><span class="sxs-lookup"><span data-stu-id="39d09-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="39d09-146">Вручную зарегистрируйте `LazyAssemblyLoader` в методе `Startup.ConfigureServices` проекта *сервера* (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="39d09-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="39d09-147">Взаимодействие пользователей с содержимым `<Navigating>`</span><span class="sxs-lookup"><span data-stu-id="39d09-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="39d09-148">При загрузке сборок, что может занять несколько секунд, компонент `Router` может показать пользователю, что происходит переход на страницу:</span><span class="sxs-lookup"><span data-stu-id="39d09-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="39d09-149">Добавьте директиву [`@using`](xref:mvc/views/razor#using) для пространства имен <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="39d09-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="39d09-150">Добавьте тег `<Navigating>` в компонент с разметкой, отображаемой во время событий перехода страницы.</span><span class="sxs-lookup"><span data-stu-id="39d09-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="39d09-151">Обработка отмены в `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="39d09-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="39d09-152">Объект `NavigationContext`, переданный в обратный вызов `OnNavigateAsync`, содержит `CancellationToken`, который задается при возникновении нового события навигации.</span><span class="sxs-lookup"><span data-stu-id="39d09-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="39d09-153">Обратный вызов `OnNavigateAsync` должен вызываться, если этот токен отмены установлен так, чтобы не выполнять обратный вызов `OnNavigateAsync` для устаревшей навигации.</span><span class="sxs-lookup"><span data-stu-id="39d09-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="39d09-154">Если пользователь переходит на маршрут A, а затем сразу же переходит на маршрут B, приложение не должно продолжать выполнять ответный вызов `OnNavigateAsync` для маршрута A:</span><span class="sxs-lookup"><span data-stu-id="39d09-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="39d09-155">Если не отбросить маршрут, если токен отмены в `NavigationContext` отменяется, то это может привести к непредсказуемому поведению, например рендерингу компонента из предыдущей навигации.</span><span class="sxs-lookup"><span data-stu-id="39d09-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="39d09-156">События `OnNavigateAsync` и переименованные файлы сборки</span><span class="sxs-lookup"><span data-stu-id="39d09-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="39d09-157">Загрузчик ресурсов использует имена сборок, определенные в файле `blazor.boot.json`.</span><span class="sxs-lookup"><span data-stu-id="39d09-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="39d09-158">Если [сборки переименованы](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), то имена сборок, используемые в методах `OnNavigateAsync`, и имена сборок в файле `blazor.boot.json` не синхронизированы.</span><span class="sxs-lookup"><span data-stu-id="39d09-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="39d09-159">Чтобы исправить это, сделайте следующее.</span><span class="sxs-lookup"><span data-stu-id="39d09-159">To rectify this:</span></span>

* <span data-ttu-id="39d09-160">Проверьте, выполняется ли приложение в рабочей среде при определении используемых имен сборок.</span><span class="sxs-lookup"><span data-stu-id="39d09-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="39d09-161">Сохраните имена переименованных сборок в отдельном файле и выполните чтение из этого файла, чтобы определить, какое имя сборки использовать в методах `LazyLoadAssemblyService` и `OnNavigateAsync`.</span><span class="sxs-lookup"><span data-stu-id="39d09-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="39d09-162">Полный пример</span><span class="sxs-lookup"><span data-stu-id="39d09-162">Complete example</span></span>

<span data-ttu-id="39d09-163">Следующий полный компонент `Router` демонстрирует загрузку сборки `GrantImaharaRobotControls.dll`, когда пользователь переходит к `/robot`.</span><span class="sxs-lookup"><span data-stu-id="39d09-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="39d09-164">Во время перехода страниц для пользователя отображается стилизованное сообщение.</span><span class="sxs-lookup"><span data-stu-id="39d09-164">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="39d09-165">Диагностика</span><span class="sxs-lookup"><span data-stu-id="39d09-165">Troubleshoot</span></span>

* <span data-ttu-id="39d09-166">Если происходит непредвиденная отрисовка (например, при отрисовке компонента из предыдущей навигации), убедитесь, что код создает исключение, если задан токен отмены.</span><span class="sxs-lookup"><span data-stu-id="39d09-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="39d09-167">Если сборки по-прежнему загружаются при запуске приложения, убедитесь, что в файле проекта сборка помечена для отложенной загрузки.</span><span class="sxs-lookup"><span data-stu-id="39d09-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39d09-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="39d09-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
