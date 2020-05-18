---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 81699158a161d0e9c9621235840979ebcd634a7e
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976705"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="82b67-103">Жизненный цикл ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="82b67-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="82b67-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="82b67-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="82b67-105">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="82b67-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="82b67-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="82b67-107">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="82b67-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="82b67-108">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="82b67-108">Component initialization methods</span></span>

<span data-ttu-id="82b67-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="82b67-110">Используйте `OnInitializedAsync`, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="82b67-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="82b67-111">Для синхронной операции переопределите `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="82b67-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="82b67-112">Чтобы выполнить асинхронную операцию, переопределите `OnInitializedAsync` и используйте в операции ключевое слово `await`:</span><span class="sxs-lookup"><span data-stu-id="82b67-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="82b67-113">Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/hosting-model-configuration#render-mode), вызывают `OnInitializedAsync` **_дважды_** :</span><span class="sxs-lookup"><span data-stu-id="82b67-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="82b67-114">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="82b67-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="82b67-115">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="82b67-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="82b67-116">Чтобы предотвратить выполнение кода разработчика в `OnInitializedAsync` дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="82b67-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="82b67-117">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов JavaScript, невозможны, так как соединение с браузером не установлено.</span><span class="sxs-lookup"><span data-stu-id="82b67-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="82b67-118">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="82b67-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="82b67-119">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="82b67-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="82b67-120">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="82b67-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="82b67-121">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="82b67-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="82b67-122">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="82b67-122">Before parameters are set</span></span>

<span data-ttu-id="82b67-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:</span><span class="sxs-lookup"><span data-stu-id="82b67-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="82b67-124"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит весь набор значений параметров при каждом вызове `SetParametersAsync`.</span><span class="sxs-lookup"><span data-stu-id="82b67-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="82b67-125">Реализация `SetParametersAsync` по умолчанию задает значение каждого свойства с атрибутом `[Parameter]` или `[CascadingParameter]`, имеющим соответствующее значение в `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="82b67-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="82b67-126">Параметры, у которых нет соответствующего значения в `ParameterView`, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="82b67-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="82b67-127">Если `base.SetParametersAync` не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="82b67-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="82b67-128">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="82b67-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="82b67-129">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="82b67-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="82b67-130">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="82b67-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="82b67-131">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="82b67-131">After parameters are set</span></span>

<span data-ttu-id="82b67-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:</span><span class="sxs-lookup"><span data-stu-id="82b67-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="82b67-133">Когда компонент инициализируется и получил первый набор параметров от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="82b67-134">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="82b67-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="82b67-135">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="82b67-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="82b67-136">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="82b67-136">Any complex-typed parameters.</span></span> <span data-ttu-id="82b67-137">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="82b67-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="82b67-138">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="82b67-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="82b67-139">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="82b67-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="82b67-140">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="82b67-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="82b67-141">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="82b67-141">After component render</span></span>

<span data-ttu-id="82b67-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="82b67-143">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="82b67-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="82b67-144">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="82b67-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="82b67-145">Параметр `firstRender` для `OnAfterRenderAsync` и `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="82b67-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="82b67-146">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="82b67-147">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="82b67-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="82b67-148">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="82b67-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="82b67-149">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из `OnAfterRenderAsync`, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="82b67-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="82b67-150">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="82b67-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="82b67-151">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="82b67-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="82b67-152">`OnAfterRender` и `OnAfterRenderAsync` *не вызываются при предварительной отрисовке на сервере.*</span><span class="sxs-lookup"><span data-stu-id="82b67-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="82b67-153">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="82b67-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="82b67-154">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="82b67-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="82b67-155">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="82b67-155">Suppress UI refreshing</span></span>

<span data-ttu-id="82b67-156">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="82b67-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="82b67-157">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="82b67-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="82b67-158">`ShouldRender` вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="82b67-159">Даже при переопределении `ShouldRender` компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="82b67-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="82b67-160">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="82b67-160">State changes</span></span>

<span data-ttu-id="82b67-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="82b67-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="82b67-162">В соответствующих случаях вызов `StateHasChanged` приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="82b67-163">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="82b67-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="82b67-164">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="82b67-165">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="82b67-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="82b67-166">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="82b67-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="82b67-167">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="82b67-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="82b67-168">В компоненте `FetchData` шаблонов Blazor `OnInitializedAsync` переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="82b67-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="82b67-169">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="82b67-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="82b67-170">Когда элемент `Task`, возвращенный `OnInitializedAsync`, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="82b67-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="82b67-171">*Pages/FetchData.razor* в шаблоне Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="82b67-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="82b67-172">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="82b67-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="82b67-173">Если компонент реализует <xref:System.IDisposable>, [метод Dispose](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="82b67-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="82b67-174">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="82b67-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="82b67-175">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="82b67-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="82b67-176">`StateHasChanged` может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="82b67-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="82b67-177">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="82b67-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="82b67-178">В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="82b67-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="82b67-179">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="82b67-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="82b67-180">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="82b67-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="82b67-181">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="82b67-181">Handle errors</span></span>

<span data-ttu-id="82b67-182">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="82b67-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="82b67-183">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="82b67-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="82b67-184">В приложении Blazor Server, если `RenderMode` имеет значение `ServerPrerendered`, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="82b67-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="82b67-185">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="82b67-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="82b67-186">Если метод жизненного цикла [OnInitialized{Async}](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:</span><span class="sxs-lookup"><span data-stu-id="82b67-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="82b67-187">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="82b67-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="82b67-188">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="82b67-188">After the server connection has been established.</span></span>

<span data-ttu-id="82b67-189">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="82b67-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="82b67-190">Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="82b67-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="82b67-191">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="82b67-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="82b67-192">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="82b67-193">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="82b67-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="82b67-194">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="82b67-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="82b67-195">Дополнительные сведения об элементе `RenderMode` см. в разделе <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="82b67-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="82b67-196">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="82b67-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="82b67-197">Отменяемая фоновая операция</span><span class="sxs-lookup"><span data-stu-id="82b67-197">Cancelable background work</span></span>

<span data-ttu-id="82b67-198">Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных.</span><span class="sxs-lookup"><span data-stu-id="82b67-198">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="82b67-199">В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций.</span><span class="sxs-lookup"><span data-stu-id="82b67-199">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="82b67-200">Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.</span><span class="sxs-lookup"><span data-stu-id="82b67-200">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="82b67-201">Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="82b67-201">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="82b67-202">Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.</span><span class="sxs-lookup"><span data-stu-id="82b67-202">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="82b67-203">Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.</span><span class="sxs-lookup"><span data-stu-id="82b67-203">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="82b67-204">Необходимо изменить приоритет выполняемых в данный момент задач.</span><span class="sxs-lookup"><span data-stu-id="82b67-204">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="82b67-205">Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.</span><span class="sxs-lookup"><span data-stu-id="82b67-205">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="82b67-206">Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре сроков выполнения фоновых рабочих элементов.</span><span class="sxs-lookup"><span data-stu-id="82b67-206">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="82b67-207">Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="82b67-207">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="82b67-208">Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="82b67-208">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="82b67-209">При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.</span><span class="sxs-lookup"><span data-stu-id="82b67-209">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="82b67-210">После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.</span><span class="sxs-lookup"><span data-stu-id="82b67-210">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="82b67-211">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="82b67-211">In the following example:</span></span>

* <span data-ttu-id="82b67-212">`await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.</span><span class="sxs-lookup"><span data-stu-id="82b67-212">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="82b67-213">`BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.</span><span class="sxs-lookup"><span data-stu-id="82b67-213">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
