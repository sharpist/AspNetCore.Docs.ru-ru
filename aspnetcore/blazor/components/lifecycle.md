---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
no-loc:
- appsettings.json
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
ms.openlocfilehash: 8a2dc802a1d05ead7445e350e3aef0ce7dfb2bb8
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981925"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="35d93-103">Жизненный цикл ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="35d93-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="35d93-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="35d93-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="35d93-105">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="35d93-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="35d93-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="35d93-107">На приведенных ниже схемах показан жизненный цикл Blazor.</span><span class="sxs-lookup"><span data-stu-id="35d93-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="35d93-108">Определение методов жизненного цикла с примерами приводится в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="35d93-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="35d93-109">События жизненного цикла компонента</span><span class="sxs-lookup"><span data-stu-id="35d93-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="35d93-110">Если компонент отрисовывается в первый раз по запросу:</span><span class="sxs-lookup"><span data-stu-id="35d93-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="35d93-111">Создайте экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-111">Create the component's instance.</span></span>
   * <span data-ttu-id="35d93-112">Выполните внедрение свойств.</span><span class="sxs-lookup"><span data-stu-id="35d93-112">Perform property injection.</span></span> <span data-ttu-id="35d93-113">Выполните [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="35d93-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="35d93-114">Вызовите [`OnInitialized{Async}`](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="35d93-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="35d93-115">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="35d93-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="35d93-116">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="35d93-116">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="35d93-117">Вызовите метод [`OnParametersSet{Async}`](#after-parameters-are-set) и выполните отрисовку компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="35d93-118">Если метод `OnParametersSetAsync` возвращает значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="35d93-118">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![События жизненного цикла компонента Razor в Blazor](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="35d93-120">Обработка событий модели DOM</span><span class="sxs-lookup"><span data-stu-id="35d93-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="35d93-121">Выполняется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="35d93-121">The event handler is run.</span></span>
1. <span data-ttu-id="35d93-122">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="35d93-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="35d93-123">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="35d93-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Обработка событий модели DOM](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="35d93-125">Жизненный цикл `Render`</span><span class="sxs-lookup"><span data-stu-id="35d93-125">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="35d93-126">Если это не первая отрисовка компонента или [`ShouldRender`](#suppress-ui-refreshing) принимает значение `false`, дальнейшие операции с компонентом выполнять не следует.</span><span class="sxs-lookup"><span data-stu-id="35d93-126">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="35d93-127">Выполните сборку отличий дерева отрисовки и отрисуйте компонент.</span><span class="sxs-lookup"><span data-stu-id="35d93-127">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="35d93-128">Подождите, пока модель DOM обновится.</span><span class="sxs-lookup"><span data-stu-id="35d93-128">Await the DOM to update.</span></span>
1. <span data-ttu-id="35d93-129">Вызовите [`OnAfterRender{Async}`](#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="35d93-129">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Жизненный цикл процесса отрисовки](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="35d93-131">Вызовы [`StateHasChanged`](#state-changes) разработчиком приводят к отрисовке.</span><span class="sxs-lookup"><span data-stu-id="35d93-131">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="35d93-132">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="35d93-132">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="35d93-133">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="35d93-133">Before parameters are set</span></span>

<span data-ttu-id="35d93-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:</span><span class="sxs-lookup"><span data-stu-id="35d93-134"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="35d93-135"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит набор значений параметров для компонента при каждом вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="35d93-135"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="35d93-136">Реализация <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> по умолчанию задает значение каждого свойства с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) или [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), имеющим соответствующее значение в <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="35d93-136">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="35d93-137">Параметры, у которых нет соответствующего значения в <xref:Microsoft.AspNetCore.Components.ParameterView>, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="35d93-137">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="35d93-138">Если [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="35d93-138">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="35d93-139">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="35d93-139">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="35d93-140">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="35d93-140">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="35d93-141">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="35d93-141">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="35d93-142">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="35d93-142">Component initialization methods</span></span>

<span data-ttu-id="35d93-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="35d93-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="35d93-144">Используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="35d93-144">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="35d93-145">Для синхронной операции переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="35d93-145">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="35d93-146">Чтобы выполнить асинхронную операцию, переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и используйте в операции оператор [`await`](/dotnet/csharp/language-reference/operators/await).</span><span class="sxs-lookup"><span data-stu-id="35d93-146">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="35d93-147">Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/fundamentals/additional-scenarios#render-mode), вызывают <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *дважды*:</span><span class="sxs-lookup"><span data-stu-id="35d93-147">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="35d93-148">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="35d93-148">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="35d93-149">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="35d93-149">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="35d93-150">Чтобы предотвратить выполнение кода разработчика в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="35d93-150">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="35d93-151">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="35d93-151">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="35d93-152">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="35d93-152">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="35d93-153">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="35d93-153">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="35d93-154">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="35d93-154">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="35d93-155">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="35d93-155">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="35d93-156">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="35d93-156">After parameters are set</span></span>

<span data-ttu-id="35d93-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:</span><span class="sxs-lookup"><span data-stu-id="35d93-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="35d93-158">После инициализации компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="35d93-158">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="35d93-159">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="35d93-159">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="35d93-160">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="35d93-160">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="35d93-161">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="35d93-161">Any complex-typed parameters.</span></span> <span data-ttu-id="35d93-162">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="35d93-162">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="35d93-163">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="35d93-163">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="35d93-164">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="35d93-164">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="35d93-165">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="35d93-165">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="35d93-166">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="35d93-166">After component render</span></span>

<span data-ttu-id="35d93-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="35d93-168">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="35d93-168">Element and component references are populated at this point.</span></span> <span data-ttu-id="35d93-169">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="35d93-169">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="35d93-170">Параметр `firstRender` для <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="35d93-170">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="35d93-171">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-171">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="35d93-172">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="35d93-172">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="35d93-173">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="35d93-173">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="35d93-174">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="35d93-174">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="35d93-175">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="35d93-175">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="35d93-176">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="35d93-176">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="35d93-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *не вызываются при предварительной отрисовке на сервере*.</span><span class="sxs-lookup"><span data-stu-id="35d93-177"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="35d93-178">Методы вызываются, когда компонент отрисовывается в интерактивном режиме после завершения предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="35d93-178">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="35d93-179">При предварительной отрисовке приложения происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="35d93-179">When the app prerenders:</span></span>

1. <span data-ttu-id="35d93-180">Компонент выполняется на сервере для создания статической HTML-разметки в HTTP-ответе.</span><span class="sxs-lookup"><span data-stu-id="35d93-180">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="35d93-181">На этом этапе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> не вызываются.</span><span class="sxs-lookup"><span data-stu-id="35d93-181">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="35d93-182">При запуске `blazor.server.js` или `blazor.webassembly.js` в браузере компонент перезапускается в режиме интерактивной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="35d93-182">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="35d93-183">После перезапуска компонента **вызываются** методы <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, так как приложение больше не находится на этапе предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="35d93-183">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="35d93-184">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="35d93-184">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="35d93-185">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="35d93-185">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="35d93-186">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="35d93-186">Suppress UI refreshing</span></span>

<span data-ttu-id="35d93-187">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="35d93-187">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="35d93-188">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="35d93-188">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="35d93-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-189"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="35d93-190">Даже при переопределении <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="35d93-190">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="35d93-191">Для получения дополнительной информации см. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span><span class="sxs-lookup"><span data-stu-id="35d93-191">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="35d93-192">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="35d93-192">State changes</span></span>

<span data-ttu-id="35d93-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="35d93-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="35d93-194">В соответствующих случаях вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-194">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="35d93-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для методов <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="35d93-195"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="35d93-196">Для получения дополнительной информации см. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="35d93-196">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="35d93-197">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="35d93-197">Handle incomplete async actions at render</span></span>

<span data-ttu-id="35d93-198">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-198">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="35d93-199">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="35d93-199">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="35d93-200">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="35d93-200">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="35d93-201">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="35d93-201">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="35d93-202">В компоненте `FetchData` шаблонов Blazor <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="35d93-202">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="35d93-203">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="35d93-203">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="35d93-204">Когда элемент `Task`, возвращенный <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="35d93-204">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="35d93-205">`Pages/FetchData.razor` в шаблоне Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="35d93-205">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="35d93-206">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="35d93-206">Handle errors</span></span>

<span data-ttu-id="35d93-207">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="35d93-207">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="35d93-208">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="35d93-208">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="35d93-209">В приложении Blazor Server, если <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> имеет значение <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="35d93-209">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="35d93-210">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="35d93-210">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="35d93-211">Если метод жизненного цикла [`OnInitialized{Async}`](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:</span><span class="sxs-lookup"><span data-stu-id="35d93-211">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="35d93-212">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="35d93-212">When the component is prerendered statically.</span></span>
* <span data-ttu-id="35d93-213">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="35d93-213">After the server connection has been established.</span></span>

<span data-ttu-id="35d93-214">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="35d93-214">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="35d93-215">Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="35d93-215">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="35d93-216">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="35d93-216">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="35d93-217">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-217">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="35d93-218">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="35d93-218">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="35d93-219">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="35d93-219">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="35d93-220">Дополнительные сведения об элементе <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> см. в разделе <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="35d93-220">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="35d93-221">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="35d93-221">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="35d93-222">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="35d93-222">Component disposal with IDisposable</span></span>

<span data-ttu-id="35d93-223">Если компонент реализует <xref:System.IDisposable>, [метод `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="35d93-223">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="35d93-224">Удаление можно выполнить в любое время, в том числе при [инициализации компонента](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="35d93-224">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="35d93-225">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="35d93-225">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="35d93-226">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="35d93-226">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="35d93-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="35d93-227"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="35d93-228">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="35d93-228">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="35d93-229">В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="35d93-229">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="35d93-230">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="35d93-230">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="35d93-231">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="35d93-231">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="35d93-232">Отменяемая фоновая операция</span><span class="sxs-lookup"><span data-stu-id="35d93-232">Cancelable background work</span></span>

<span data-ttu-id="35d93-233">Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных.</span><span class="sxs-lookup"><span data-stu-id="35d93-233">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="35d93-234">В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций.</span><span class="sxs-lookup"><span data-stu-id="35d93-234">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="35d93-235">Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.</span><span class="sxs-lookup"><span data-stu-id="35d93-235">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="35d93-236">Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="35d93-236">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="35d93-237">Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.</span><span class="sxs-lookup"><span data-stu-id="35d93-237">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="35d93-238">Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.</span><span class="sxs-lookup"><span data-stu-id="35d93-238">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="35d93-239">Необходимо изменить приоритет выполняемых в данный момент задач.</span><span class="sxs-lookup"><span data-stu-id="35d93-239">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="35d93-240">Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.</span><span class="sxs-lookup"><span data-stu-id="35d93-240">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="35d93-241">Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре графика выполнения фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="35d93-241">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="35d93-242">Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="35d93-242">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="35d93-243">Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="35d93-243">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="35d93-244">При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.</span><span class="sxs-lookup"><span data-stu-id="35d93-244">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="35d93-245">После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.</span><span class="sxs-lookup"><span data-stu-id="35d93-245">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="35d93-246">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="35d93-246">In the following example:</span></span>

* <span data-ttu-id="35d93-247">`await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.</span><span class="sxs-lookup"><span data-stu-id="35d93-247">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="35d93-248">`BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.</span><span class="sxs-lookup"><span data-stu-id="35d93-248">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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

## <a name="no-locblazor-server-reconnection-events"></a><span data-ttu-id="35d93-249">События повторного подключения Blazor Server</span><span class="sxs-lookup"><span data-stu-id="35d93-249">Blazor Server reconnection events</span></span>

<span data-ttu-id="35d93-250">События жизненного цикла компонента, описываемые в этой статье, работают отдельно от [обработчиков событий повторного подключения Blazor Server](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span><span class="sxs-lookup"><span data-stu-id="35d93-250">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/additional-scenarios#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="35d93-251">Когда приложение Blazor Server теряет подключение SignalR к клиенту, прерываются только операции обновления пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="35d93-251">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="35d93-252">Они возобновляются при восстановлении подключения.</span><span class="sxs-lookup"><span data-stu-id="35d93-252">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="35d93-253">Дополнительные сведения о конфигурации и событиях обработчика канала см. в статье <xref:blazor/fundamentals/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="35d93-253">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/additional-scenarios>.</span></span>
