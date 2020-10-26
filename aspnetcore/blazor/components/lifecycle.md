---
title: 'Жизненный цикл ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Узнайте, как использовать методы жизненного цикла компонента :::no-loc(Razor)::: в приложениях ASP.NET Core :::no-loc(Blazor):::.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2020
no-loc:
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 035de12d17b676aac6af42e706f3741937d90fb3
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92153585"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="65092-103">Жизненный цикл ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="65092-103">ASP.NET Core :::no-loc(Blazor)::: lifecycle</span></span>

<span data-ttu-id="65092-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="65092-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="65092-105">Платформа :::no-loc(Blazor)::: содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="65092-105">The :::no-loc(Blazor)::: framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="65092-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="65092-107">На приведенных ниже схемах показан жизненный цикл :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="65092-107">The following diagrams illustrate the :::no-loc(Blazor)::: lifecycle.</span></span> <span data-ttu-id="65092-108">Определение методов жизненного цикла с примерами приводится в следующих разделах этой статьи.</span><span class="sxs-lookup"><span data-stu-id="65092-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="65092-109">События жизненного цикла компонента</span><span class="sxs-lookup"><span data-stu-id="65092-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="65092-110">Если компонент отрисовывается в первый раз по запросу:</span><span class="sxs-lookup"><span data-stu-id="65092-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="65092-111">Создайте экземпляр компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-111">Create the component's instance.</span></span>
   * <span data-ttu-id="65092-112">Выполните внедрение свойств.</span><span class="sxs-lookup"><span data-stu-id="65092-112">Perform property injection.</span></span> <span data-ttu-id="65092-113">Выполните [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="65092-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="65092-114">Вызовите [`OnInitialized{Async}`](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="65092-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="65092-115">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="65092-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65092-116">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="65092-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="65092-117">Вызовите [`OnParametersSet{Async}`](#after-parameters-are-set).</span><span class="sxs-lookup"><span data-stu-id="65092-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set).</span></span> <span data-ttu-id="65092-118">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="65092-118">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65092-119">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="65092-119">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>

![События жизненного цикла компонента :::no-loc(Razor)::: в :::no-loc(Blazor):::](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="65092-121">Обработка событий модели DOM</span><span class="sxs-lookup"><span data-stu-id="65092-121">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="65092-122">Выполняется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="65092-122">The event handler is run.</span></span>
1. <span data-ttu-id="65092-123">Если возвращается значение <xref:System.Threading.Tasks.Task>, ожидается <xref:System.Threading.Tasks.Task>, а затем компонент отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="65092-123">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="65092-124">Если значение <xref:System.Threading.Tasks.Task> не возвращается, компонент отрисовывается сразу.</span><span class="sxs-lookup"><span data-stu-id="65092-124">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![Обработка событий модели DOM](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="65092-126">Жизненный цикл `Render`</span><span class="sxs-lookup"><span data-stu-id="65092-126">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="65092-127">Если это не первая отрисовка компонента или [`ShouldRender`](#suppress-ui-refreshing) принимает значение `false`, дальнейшие операции с компонентом выполнять не следует.</span><span class="sxs-lookup"><span data-stu-id="65092-127">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="65092-128">Выполните сборку отличий дерева отрисовки и отрисуйте компонент.</span><span class="sxs-lookup"><span data-stu-id="65092-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="65092-129">Подождите, пока модель DOM обновится.</span><span class="sxs-lookup"><span data-stu-id="65092-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="65092-130">Вызовите [`OnAfterRender{Async}`](#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="65092-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![Жизненный цикл процесса отрисовки](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="65092-132">Вызовы [`StateHasChanged`](#state-changes) разработчиком приводят к отрисовке.</span><span class="sxs-lookup"><span data-stu-id="65092-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="65092-133">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="65092-133">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="65092-134">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="65092-134">Before parameters are set</span></span>

<span data-ttu-id="65092-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:</span><span class="sxs-lookup"><span data-stu-id="65092-135"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="65092-136"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит набор значений параметров для компонента при каждом вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65092-136"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="65092-137">Реализация <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> по умолчанию задает значение каждого свойства с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) или [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute), имеющим соответствующее значение в <xref:Microsoft.AspNetCore.Components.ParameterView>.</span><span class="sxs-lookup"><span data-stu-id="65092-137">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="65092-138">Параметры, у которых нет соответствующего значения в <xref:Microsoft.AspNetCore.Components.ParameterView>, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="65092-138">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="65092-139">Если [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="65092-139">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="65092-140">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="65092-140">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="65092-141">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="65092-141">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65092-142">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="65092-142">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="65092-143">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="65092-143">Component initialization methods</span></span>

<span data-ttu-id="65092-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65092-144"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="65092-145">Используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="65092-145">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="65092-146">Для синхронной операции переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="65092-146">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="65092-147">Чтобы выполнить асинхронную операцию, переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и используйте в операции оператор [`await`](/dotnet/csharp/language-reference/operators/await).</span><span class="sxs-lookup"><span data-stu-id="65092-147">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="65092-148">Приложения :::no-loc(Blazor Server):::, которые [предварительно отрисовывают свое содержимое](xref:blazor/fundamentals/additional-scenarios#render-mode), вызывают <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_дважды_** :</span><span class="sxs-lookup"><span data-stu-id="65092-148">:::no-loc(Blazor Server)::: apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_** :</span></span>

* <span data-ttu-id="65092-149">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="65092-149">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="65092-150">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="65092-150">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="65092-151">Чтобы предотвратить выполнение кода разработчика в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="65092-151">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="65092-152">Когда приложение :::no-loc(Blazor Server)::: выполняет предварительную отрисовку, некоторые действия, такие как вызов в JavaScript, невозможны, так как подключение к браузеру не установлено.</span><span class="sxs-lookup"><span data-stu-id="65092-152">While a :::no-loc(Blazor Server)::: app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="65092-153">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="65092-153">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="65092-154">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="65092-154">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="65092-155">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="65092-155">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65092-156">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="65092-156">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="65092-157">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="65092-157">After parameters are set</span></span>

<span data-ttu-id="65092-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:</span><span class="sxs-lookup"><span data-stu-id="65092-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="65092-159">После инициализации компонента в <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> или <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65092-159">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="65092-160">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="65092-160">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="65092-161">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="65092-161">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="65092-162">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="65092-162">Any complex-typed parameters.</span></span> <span data-ttu-id="65092-163">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="65092-163">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="65092-164">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65092-164">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="65092-165">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="65092-165">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65092-166">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="65092-166">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="65092-167">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="65092-167">After component render</span></span>

<span data-ttu-id="65092-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-168"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="65092-169">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="65092-169">Element and component references are populated at this point.</span></span> <span data-ttu-id="65092-170">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="65092-170">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="65092-171">Параметр `firstRender` для <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span><span class="sxs-lookup"><span data-stu-id="65092-171">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="65092-172">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-172">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="65092-173">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="65092-173">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="65092-174">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65092-174">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="65092-175">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="65092-175">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="65092-176">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="65092-176">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="65092-177">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="65092-177">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="65092-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *не вызываются при предварительной отрисовке на сервере* .</span><span class="sxs-lookup"><span data-stu-id="65092-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server* .</span></span> <span data-ttu-id="65092-179">Методы вызываются, когда компонент отрисовывается в интерактивном режиме после завершения предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="65092-179">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="65092-180">При предварительной отрисовке приложения происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="65092-180">When the app prerenders:</span></span>

1. <span data-ttu-id="65092-181">Компонент выполняется на сервере для создания статической HTML-разметки в HTTP-ответе.</span><span class="sxs-lookup"><span data-stu-id="65092-181">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="65092-182">На этом этапе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> не вызываются.</span><span class="sxs-lookup"><span data-stu-id="65092-182">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="65092-183">При запуске `blazor.server.js` или `blazor.webassembly.js` в браузере компонент перезапускается в режиме интерактивной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="65092-183">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="65092-184">После перезапуска компонента **вызываются** методы <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, так как приложение больше не находится на этапе предварительной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="65092-184">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="65092-185">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="65092-185">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="65092-186">Дополнительные сведения см. в разделе [Удаление компонентов с помощью `IDisposable`](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="65092-186">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="65092-187">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="65092-187">Suppress UI refreshing</span></span>

<span data-ttu-id="65092-188">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="65092-188">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="65092-189">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="65092-189">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="65092-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-190"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="65092-191">Даже при переопределении <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="65092-191">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="65092-192">Для получения дополнительной информации см. <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="65092-192">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="65092-193">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="65092-193">State changes</span></span>

<span data-ttu-id="65092-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="65092-194"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="65092-195">В соответствующих случаях вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-195">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="65092-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для методов <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="65092-196"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="65092-197">Для получения дополнительной информации см. <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="65092-197">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="65092-198">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="65092-198">Handle incomplete async actions at render</span></span>

<span data-ttu-id="65092-199">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-199">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="65092-200">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="65092-200">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="65092-201">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="65092-201">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="65092-202">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="65092-202">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="65092-203">В компоненте `FetchData` шаблонов :::no-loc(Blazor)::: <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="65092-203">In the `FetchData` component of the :::no-loc(Blazor)::: templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="65092-204">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="65092-204">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="65092-205">Когда элемент `Task`, возвращенный <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="65092-205">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="65092-206">`Pages/FetchData.razor` в шаблоне :::no-loc(Blazor Server)::::</span><span class="sxs-lookup"><span data-stu-id="65092-206">`Pages/FetchData.razor` in the :::no-loc(Blazor Server)::: template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="65092-207">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="65092-207">Handle errors</span></span>

<span data-ttu-id="65092-208">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="65092-208">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="65092-209">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="65092-209">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="65092-210">В приложении :::no-loc(Blazor Server):::, если <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> имеет значение <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="65092-210">In a :::no-loc(Blazor Server)::: app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="65092-211">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="65092-211">Once the browser establishes a connection back to the server, the component is rendered *again* , and the component is now interactive.</span></span> <span data-ttu-id="65092-212">Если метод жизненного цикла [`OnInitialized{Async}`](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды* :</span><span class="sxs-lookup"><span data-stu-id="65092-212">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice* :</span></span>

* <span data-ttu-id="65092-213">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="65092-213">When the component is prerendered statically.</span></span>
* <span data-ttu-id="65092-214">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="65092-214">After the server connection has been established.</span></span>

<span data-ttu-id="65092-215">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="65092-215">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="65092-216">Чтобы избежать сценария двойной отрисовки в приложении :::no-loc(Blazor Server):::, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="65092-216">To avoid the double-rendering scenario in a :::no-loc(Blazor Server)::: app:</span></span>

* <span data-ttu-id="65092-217">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="65092-217">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="65092-218">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-218">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="65092-219">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="65092-219">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="65092-220">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении :::no-loc(Blazor Server)::: на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="65092-220">The following code demonstrates an updated `WeatherForecastService` in a template-based :::no-loc(Blazor Server)::: app that avoids the double rendering:</span></span>

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

<span data-ttu-id="65092-221">Дополнительные сведения об элементе <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> см. в разделе <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="65092-221">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="65092-222">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="65092-222">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="65092-223">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="65092-223">Component disposal with IDisposable</span></span>

<span data-ttu-id="65092-224">Если компонент реализует <xref:System.IDisposable>, [метод `Dispose`](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="65092-224">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="65092-225">Удаление можно выполнить в любое время, в том числе при [инициализации компонента](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="65092-225">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="65092-226">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="65092-226">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="65092-227">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="65092-227">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="65092-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="65092-228"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="65092-229">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="65092-229">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="65092-230">В следующих примерах [формы :::no-loc(Blazor):::](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="65092-230">The following [:::no-loc(Blazor)::: form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="65092-231">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="65092-231">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="65092-232">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="65092-232">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="65092-233">Отменяемая фоновая операция</span><span class="sxs-lookup"><span data-stu-id="65092-233">Cancelable background work</span></span>

<span data-ttu-id="65092-234">Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных.</span><span class="sxs-lookup"><span data-stu-id="65092-234">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="65092-235">В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций.</span><span class="sxs-lookup"><span data-stu-id="65092-235">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="65092-236">Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.</span><span class="sxs-lookup"><span data-stu-id="65092-236">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="65092-237">Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="65092-237">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="65092-238">Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.</span><span class="sxs-lookup"><span data-stu-id="65092-238">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="65092-239">Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.</span><span class="sxs-lookup"><span data-stu-id="65092-239">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="65092-240">Необходимо изменить приоритет выполняемых в данный момент задач.</span><span class="sxs-lookup"><span data-stu-id="65092-240">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="65092-241">Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.</span><span class="sxs-lookup"><span data-stu-id="65092-241">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="65092-242">Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре графика выполнения фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="65092-242">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="65092-243">Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="65092-243">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="65092-244">Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="65092-244">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="65092-245">При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.</span><span class="sxs-lookup"><span data-stu-id="65092-245">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="65092-246">После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.</span><span class="sxs-lookup"><span data-stu-id="65092-246">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="65092-247">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="65092-247">In the following example:</span></span>

* <span data-ttu-id="65092-248">`await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.</span><span class="sxs-lookup"><span data-stu-id="65092-248">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="65092-249">`BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.</span><span class="sxs-lookup"><span data-stu-id="65092-249">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
