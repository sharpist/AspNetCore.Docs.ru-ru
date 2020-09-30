---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 00573f87b65e53a7bfd9cc2aed1d2ed7772b9a4a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847615"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="62a73-103">Жизненный цикл ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="62a73-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="62a73-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="62a73-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="62a73-105">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="62a73-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="62a73-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="62a73-107">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="62a73-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="62a73-108">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="62a73-108">Before parameters are set</span></span>

<span data-ttu-id="62a73-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:</span><span class="sxs-lookup"><span data-stu-id="62a73-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="62a73-110"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит набор значений параметров для компонента при каждом вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="62a73-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="62a73-111">Реализация <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> по умолчанию задает значение каждого свойства с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) или [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), имеющим соответствующее значение в <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="62a73-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="62a73-112">Параметры, у которых нет соответствующего значения в <xref:Microsoft.AspNetCore.Components.ParameterView>, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="62a73-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="62a73-113">Если [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="62a73-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="62a73-114">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="62a73-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="62a73-115">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="62a73-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="62a73-116">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="62a73-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="62a73-117">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="62a73-117">Component initialization methods</span></span>

<span data-ttu-id="62a73-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="62a73-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="62a73-119">Используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="62a73-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="62a73-120">Для синхронной операции переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="62a73-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="62a73-121">Чтобы выполнить асинхронную операцию, переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и используйте в операции оператор [`await`](/dotnet/csharp/language-reference/operators/await).</span><span class="sxs-lookup"><span data-stu-id="62a73-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="62a73-122">Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/fundamentals/additional-scenarios#render-mode), вызывают <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_дважды_**:</span><span class="sxs-lookup"><span data-stu-id="62a73-122">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="62a73-123">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="62a73-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="62a73-124">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="62a73-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="62a73-125">Чтобы предотвратить выполнение кода разработчика в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="62a73-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="62a73-126">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="62a73-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="62a73-127">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="62a73-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="62a73-128">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="62a73-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="62a73-129">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="62a73-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="62a73-130">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="62a73-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="62a73-131">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="62a73-131">After parameters are set</span></span>

<span data-ttu-id="62a73-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:</span><span class="sxs-lookup"><span data-stu-id="62a73-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="62a73-133">После инициализации компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="62a73-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="62a73-134">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="62a73-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="62a73-135">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="62a73-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="62a73-136">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="62a73-136">Any complex-typed parameters.</span></span> <span data-ttu-id="62a73-137">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="62a73-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="62a73-138">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="62a73-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="62a73-139">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="62a73-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="62a73-140">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="62a73-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="62a73-141">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="62a73-141">After component render</span></span>

<span data-ttu-id="62a73-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="62a73-143">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="62a73-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="62a73-144">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="62a73-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="62a73-145">Параметр `firstRender` для <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="62a73-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="62a73-146">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="62a73-147">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="62a73-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="62a73-148">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="62a73-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="62a73-149">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="62a73-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="62a73-150">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="62a73-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="62a73-151">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="62a73-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="62a73-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *не вызываются при предварительной отрисовке на сервере*.</span><span class="sxs-lookup"><span data-stu-id="62a73-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="62a73-153">Методы вызываются, когда компонент отрисовывается в интерактивном режиме после завершения предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="62a73-153">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="62a73-154">При предварительной отрисовке приложения происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="62a73-154">When the app prerenders:</span></span>

1. <span data-ttu-id="62a73-155">Компонент выполняется на сервере для создания статической HTML-разметки в HTTP-ответе.</span><span class="sxs-lookup"><span data-stu-id="62a73-155">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="62a73-156">На этом этапе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> не вызываются.</span><span class="sxs-lookup"><span data-stu-id="62a73-156">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="62a73-157">При запуске `blazor.server.js` или `blazor.webassembly.js` в браузере компонент перезапускается в режиме интерактивной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="62a73-157">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="62a73-158">После перезапуска компонента **вызываются** методы <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, так как приложение больше не находится на этапе предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="62a73-158">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="62a73-159">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="62a73-159">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="62a73-160">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="62a73-160">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="62a73-161">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="62a73-161">Suppress UI refreshing</span></span>

<span data-ttu-id="62a73-162">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="62a73-162">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="62a73-163">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="62a73-163">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="62a73-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="62a73-165">Даже при переопределении <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="62a73-165">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="62a73-166">Для получения дополнительной информации см. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="62a73-166">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="62a73-167">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="62a73-167">State changes</span></span>

<span data-ttu-id="62a73-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="62a73-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="62a73-169">В соответствующих случаях вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-169">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="62a73-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для методов <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="62a73-170"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="62a73-171">Для получения дополнительной информации см. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="62a73-171">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="62a73-172">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="62a73-172">Handle incomplete async actions at render</span></span>

<span data-ttu-id="62a73-173">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-173">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="62a73-174">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="62a73-174">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="62a73-175">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="62a73-175">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="62a73-176">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="62a73-176">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="62a73-177">В компоненте `FetchData` шаблонов Blazor <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="62a73-177">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="62a73-178">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="62a73-178">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="62a73-179">Когда элемент `Task`, возвращенный <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="62a73-179">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="62a73-180">`Pages/FetchData.razor` в шаблоне Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="62a73-180">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="62a73-181">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="62a73-181">Handle errors</span></span>

<span data-ttu-id="62a73-182">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="62a73-182">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="62a73-183">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="62a73-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="62a73-184">В приложении Blazor Server, если <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> имеет значение <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="62a73-184">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="62a73-185">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="62a73-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="62a73-186">Если метод жизненного цикла [`OnInitialized{Async}`](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:</span><span class="sxs-lookup"><span data-stu-id="62a73-186">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="62a73-187">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="62a73-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="62a73-188">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="62a73-188">After the server connection has been established.</span></span>

<span data-ttu-id="62a73-189">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="62a73-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="62a73-190">Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="62a73-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="62a73-191">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="62a73-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="62a73-192">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="62a73-193">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="62a73-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="62a73-194">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="62a73-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="62a73-195">Дополнительные сведения об элементе <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> см. в разделе <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="62a73-195">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="62a73-196">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="62a73-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="62a73-197">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="62a73-197">Component disposal with IDisposable</span></span>

<span data-ttu-id="62a73-198">Если компонент реализует <xref:System.IDisposable>, [метод `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="62a73-198">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="62a73-199">Удаление можно выполнить в любое время, в том числе при [инициализации компонента](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="62a73-199">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="62a73-200">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="62a73-200">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="62a73-201">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="62a73-201">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="62a73-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="62a73-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="62a73-203">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="62a73-203">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="62a73-204">В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="62a73-204">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="62a73-205">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="62a73-205">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="62a73-206">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="62a73-206">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="62a73-207">Отменяемая фоновая операция</span><span class="sxs-lookup"><span data-stu-id="62a73-207">Cancelable background work</span></span>

<span data-ttu-id="62a73-208">Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных.</span><span class="sxs-lookup"><span data-stu-id="62a73-208">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="62a73-209">В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций.</span><span class="sxs-lookup"><span data-stu-id="62a73-209">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="62a73-210">Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.</span><span class="sxs-lookup"><span data-stu-id="62a73-210">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="62a73-211">Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="62a73-211">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="62a73-212">Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.</span><span class="sxs-lookup"><span data-stu-id="62a73-212">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="62a73-213">Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.</span><span class="sxs-lookup"><span data-stu-id="62a73-213">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="62a73-214">Необходимо изменить приоритет выполняемых в данный момент задач.</span><span class="sxs-lookup"><span data-stu-id="62a73-214">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="62a73-215">Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.</span><span class="sxs-lookup"><span data-stu-id="62a73-215">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="62a73-216">Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре графика выполнения фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="62a73-216">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="62a73-217">Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="62a73-217">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="62a73-218">Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="62a73-218">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="62a73-219">При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.</span><span class="sxs-lookup"><span data-stu-id="62a73-219">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="62a73-220">После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.</span><span class="sxs-lookup"><span data-stu-id="62a73-220">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="62a73-221">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="62a73-221">In the following example:</span></span>

* <span data-ttu-id="62a73-222">`await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.</span><span class="sxs-lookup"><span data-stu-id="62a73-222">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="62a73-223">`BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.</span><span class="sxs-lookup"><span data-stu-id="62a73-223">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
