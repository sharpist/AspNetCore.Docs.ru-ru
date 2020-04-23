---
title: Жизненный цикл ASP.NET Core Blazor
author: guardrex
description: Узнайте, как использовать методы жизненного цикла компонента Razor в приложениях ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791456"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="f47a6-103">Жизненный цикл ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f47a6-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="f47a6-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="f47a6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f47a6-105">Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла.</span><span class="sxs-lookup"><span data-stu-id="f47a6-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="f47a6-106">Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="f47a6-107">Методы жизненного цикла</span><span class="sxs-lookup"><span data-stu-id="f47a6-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="f47a6-108">Методы инициализации компонента</span><span class="sxs-lookup"><span data-stu-id="f47a6-108">Component initialization methods</span></span>

<span data-ttu-id="f47a6-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="f47a6-110">Используйте `OnInitializedAsync`, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.</span><span class="sxs-lookup"><span data-stu-id="f47a6-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="f47a6-111">Для синхронной операции переопределите `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="f47a6-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="f47a6-112">Чтобы выполнить асинхронную операцию, переопределите `OnInitializedAsync` и используйте в операции ключевое слово `await`:</span><span class="sxs-lookup"><span data-stu-id="f47a6-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="f47a6-113">Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/hosting-model-configuration#render-mode), вызывают `OnInitializedAsync` **_дважды_** :</span><span class="sxs-lookup"><span data-stu-id="f47a6-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="f47a6-114">Один раз, когда компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="f47a6-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="f47a6-115">Второй раз, когда браузер устанавливает соединение с сервером.</span><span class="sxs-lookup"><span data-stu-id="f47a6-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="f47a6-116">Чтобы предотвратить выполнение кода разработчика в `OnInitializedAsync` дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).</span><span class="sxs-lookup"><span data-stu-id="f47a6-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="f47a6-117">Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов JavaScript, невозможны, так как соединение с браузером не установлено.</span><span class="sxs-lookup"><span data-stu-id="f47a6-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="f47a6-118">При предварительной отрисовке компоненты могут отрисовываться иначе.</span><span class="sxs-lookup"><span data-stu-id="f47a6-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="f47a6-119">Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="f47a6-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="f47a6-120">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="f47a6-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f47a6-121">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="f47a6-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="f47a6-122">До указания параметров</span><span class="sxs-lookup"><span data-stu-id="f47a6-122">Before parameters are set</span></span>

<span data-ttu-id="f47a6-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:</span><span class="sxs-lookup"><span data-stu-id="f47a6-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="f47a6-124"><xref:Microsoft.AspNetCore.Components.ParameterView> содержит весь набор значений параметров при каждом вызове `SetParametersAsync`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="f47a6-125">Реализация `SetParametersAsync` по умолчанию задает значение каждого свойства с атрибутом `[Parameter]` или `[CascadingParameter]`, имеющим соответствующее значение в `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="f47a6-126">Параметры, у которых нет соответствующего значения в `ParameterView`, остаются неизменными.</span><span class="sxs-lookup"><span data-stu-id="f47a6-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="f47a6-127">Если `base.SetParametersAync` не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом.</span><span class="sxs-lookup"><span data-stu-id="f47a6-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="f47a6-128">Например, нет необходимости назначать входящие параметры свойствам класса.</span><span class="sxs-lookup"><span data-stu-id="f47a6-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="f47a6-129">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="f47a6-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f47a6-130">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="f47a6-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="f47a6-131">После указания параметров</span><span class="sxs-lookup"><span data-stu-id="f47a6-131">After parameters are set</span></span>

<span data-ttu-id="f47a6-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> вызываются:</span><span class="sxs-lookup"><span data-stu-id="f47a6-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="f47a6-133">Когда компонент инициализируется и получил первый набор параметров от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="f47a6-134">Когда родительский компонент повторно отрисовывается и предоставляет:</span><span class="sxs-lookup"><span data-stu-id="f47a6-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="f47a6-135">Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.</span><span class="sxs-lookup"><span data-stu-id="f47a6-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="f47a6-136">Любые параметры сложных типов.</span><span class="sxs-lookup"><span data-stu-id="f47a6-136">Any complex-typed parameters.</span></span> <span data-ttu-id="f47a6-137">Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.</span><span class="sxs-lookup"><span data-stu-id="f47a6-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="f47a6-138">Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="f47a6-139">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="f47a6-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f47a6-140">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="f47a6-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="f47a6-141">После отрисовки компонента</span><span class="sxs-lookup"><span data-stu-id="f47a6-141">After component render</span></span>

<span data-ttu-id="f47a6-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> вызываются после завершения отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="f47a6-143">В этот момент указываются ссылки на элементы и компоненты.</span><span class="sxs-lookup"><span data-stu-id="f47a6-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="f47a6-144">Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.</span><span class="sxs-lookup"><span data-stu-id="f47a6-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="f47a6-145">Параметр `firstRender` для `OnAfterRenderAsync` и `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="f47a6-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="f47a6-146">Устанавливается в значение `true` при первой отрисовке экземпляра компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="f47a6-147">Может использоваться, чтобы гарантировать однократное выполнение инициализации.</span><span class="sxs-lookup"><span data-stu-id="f47a6-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="f47a6-148">Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="f47a6-149">Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из `OnAfterRenderAsync`, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи.</span><span class="sxs-lookup"><span data-stu-id="f47a6-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="f47a6-150">Это позволяет избежать бесконечного цикла отрисовки.</span><span class="sxs-lookup"><span data-stu-id="f47a6-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="f47a6-151">Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.</span><span class="sxs-lookup"><span data-stu-id="f47a6-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="f47a6-152">`OnAfterRender` и `OnAfterRenderAsync` *не вызываются при предварительной отрисовке на сервере.*</span><span class="sxs-lookup"><span data-stu-id="f47a6-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="f47a6-153">Если настроены какие-либо обработчики событий, отсоедините их при удалении.</span><span class="sxs-lookup"><span data-stu-id="f47a6-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="f47a6-154">Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).</span><span class="sxs-lookup"><span data-stu-id="f47a6-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="f47a6-155">Подавление обновления пользовательского интерфейса</span><span class="sxs-lookup"><span data-stu-id="f47a6-155">Suppress UI refreshing</span></span>

<span data-ttu-id="f47a6-156">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*>, чтобы отключить обновление пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="f47a6-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="f47a6-157">Если реализация возвращает `true`, пользовательский интерфейс обновляется:</span><span class="sxs-lookup"><span data-stu-id="f47a6-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="f47a6-158">`ShouldRender` вызывается каждый раз при отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="f47a6-159">Даже при переопределении `ShouldRender` компонент всегда проходит первоначальную отрисовку.</span><span class="sxs-lookup"><span data-stu-id="f47a6-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="f47a6-160">Изменения состояния</span><span class="sxs-lookup"><span data-stu-id="f47a6-160">State changes</span></span>

<span data-ttu-id="f47a6-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> уведомляет компонент о том, что его состояние изменилось.</span><span class="sxs-lookup"><span data-stu-id="f47a6-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="f47a6-162">В соответствующих случаях вызов `StateHasChanged` приводит к повторной отрисовке компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="f47a6-163">Обработка незавершенных асинхронных действий при отрисовке</span><span class="sxs-lookup"><span data-stu-id="f47a6-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="f47a6-164">Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="f47a6-165">Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью.</span><span class="sxs-lookup"><span data-stu-id="f47a6-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="f47a6-166">Предоставьте логику отрисовки для подтверждения инициализации объектов.</span><span class="sxs-lookup"><span data-stu-id="f47a6-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="f47a6-167">Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="f47a6-168">В компоненте `FetchData` шаблонов Blazor `OnInitializedAsync` переопределяется для асинхронного получения данных прогноза (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="f47a6-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="f47a6-169">Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке.</span><span class="sxs-lookup"><span data-stu-id="f47a6-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="f47a6-170">Когда элемент `Task`, возвращенный `OnInitializedAsync`, завершается, компонент отрисовывается повторно с обновленным состоянием.</span><span class="sxs-lookup"><span data-stu-id="f47a6-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="f47a6-171">*Pages/FetchData.razor* в шаблоне Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="f47a6-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="f47a6-172">Освобождение компонентов с помощью IDisposable</span><span class="sxs-lookup"><span data-stu-id="f47a6-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="f47a6-173">Если компонент реализует <xref:System.IDisposable>, [метод Dispose](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="f47a6-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="f47a6-174">Следующий компонент использует `@implements IDisposable` и метод `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="f47a6-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="f47a6-175">Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> в `Dispose` не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="f47a6-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="f47a6-176">`StateHasChanged` может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="f47a6-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="f47a6-177">Отмена подписки на обработчики событий из событий .NET.</span><span class="sxs-lookup"><span data-stu-id="f47a6-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="f47a6-178">В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="f47a6-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="f47a6-179">Подход с использованием частного поля и лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="f47a6-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="f47a6-180">Подход с использованием частного метода</span><span class="sxs-lookup"><span data-stu-id="f47a6-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="f47a6-181">Обработка ошибок</span><span class="sxs-lookup"><span data-stu-id="f47a6-181">Handle errors</span></span>

<span data-ttu-id="f47a6-182">Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="f47a6-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="f47a6-183">Повторное подключение с отслеживанием состояния после предварительной отрисовки</span><span class="sxs-lookup"><span data-stu-id="f47a6-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="f47a6-184">В приложении Blazor Server, если `RenderMode` имеет значение `ServerPrerendered`, компонент изначально отрисовывается статически как часть страницы.</span><span class="sxs-lookup"><span data-stu-id="f47a6-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="f47a6-185">После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным.</span><span class="sxs-lookup"><span data-stu-id="f47a6-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="f47a6-186">Если метод жизненного цикла [OnInitialized{Async}](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:</span><span class="sxs-lookup"><span data-stu-id="f47a6-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="f47a6-187">Когда компонент предварительно отрисовывается статически.</span><span class="sxs-lookup"><span data-stu-id="f47a6-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="f47a6-188">После установления соединения с сервером.</span><span class="sxs-lookup"><span data-stu-id="f47a6-188">After the server connection has been established.</span></span>

<span data-ttu-id="f47a6-189">Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.</span><span class="sxs-lookup"><span data-stu-id="f47a6-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="f47a6-190">Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="f47a6-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="f47a6-191">Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.</span><span class="sxs-lookup"><span data-stu-id="f47a6-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="f47a6-192">Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.</span><span class="sxs-lookup"><span data-stu-id="f47a6-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="f47a6-193">Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.</span><span class="sxs-lookup"><span data-stu-id="f47a6-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="f47a6-194">В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:</span><span class="sxs-lookup"><span data-stu-id="f47a6-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="f47a6-195">Дополнительные сведения об элементе `RenderMode` см. в разделе <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="f47a6-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="f47a6-196">Обнаружение предварительной отрисовки приложения</span><span class="sxs-lookup"><span data-stu-id="f47a6-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
