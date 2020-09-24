---
title: Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly
author: pranavkm
description: Советы по повышению производительности приложений ASP.NET Core Blazor WebAssembly и избежанию распространенных проблем с производительностью.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: d1ad646f82e5c9ba611a60fc9be8378bedef8dee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721727"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="ed218-103">Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ed218-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="ed218-104">Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="ed218-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="ed218-105">В этой статье приводятся рекомендации по обеспечению высокой производительности ASP.NET Core Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="ed218-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="ed218-106">Устранение лишних отрисовок компонентов</span><span class="sxs-lookup"><span data-stu-id="ed218-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="ed218-107">Алгоритм сравнения Blazor позволяет избежать повторной отрисовки компонентов, которые не изменялись.</span><span class="sxs-lookup"><span data-stu-id="ed218-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="ed218-108">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> для точного управления отрисовкой компонентов.</span><span class="sxs-lookup"><span data-stu-id="ed218-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="ed218-109">При создании компонента, который используется только в пользовательском интерфейсе и не изменяется после первоначальной отрисовки, настройте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> так, чтобы он возвращал значение `false`:</span><span class="sxs-lookup"><span data-stu-id="ed218-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="ed218-110">Большинству приложений не требуется детализированный контроль, но метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> можно использовать для выборочной отрисовки компонентов, отвечающих на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="ed218-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="ed218-111">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> также может потребоваться в сценариях с отрисовкой большого числа компонентов.</span><span class="sxs-lookup"><span data-stu-id="ed218-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="ed218-112">Например, рассмотрим сетку, в которой использование <xref:Microsoft.AspNetCore.Components.EventCallback> в одном компоненте одной ячейки сетки вызывает метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для всей сетки.</span><span class="sxs-lookup"><span data-stu-id="ed218-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="ed218-113">При вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> повторно отрисовываются все дочерние компоненты.</span><span class="sxs-lookup"><span data-stu-id="ed218-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="ed218-114">Если нужно повторно отрисовать только несколько ячеек, используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы не снижать производительность из-за ненужных операций.</span><span class="sxs-lookup"><span data-stu-id="ed218-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="ed218-115">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="ed218-115">In the following example:</span></span>

* <span data-ttu-id="ed218-116">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> переопределяется, и ему присваивается значение поля <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, которое при загрузке компонента изначально имеет значение `false`.</span><span class="sxs-lookup"><span data-stu-id="ed218-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="ed218-117">При нажатии кнопки методу <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> присваивается значение `true`, что приводит к повторной отрисовке компонента с обновленным `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="ed218-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="ed218-118">Сразу после повторной отрисовки <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> снова устанавливает для <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> значение `false`, чтобы предотвратить дальнейшую повторную отрисовку до тех пор, пока кнопка не будет нажата еще раз.</span><span class="sxs-lookup"><span data-stu-id="ed218-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="ed218-119">Для получения дополнительной информации см. <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="ed218-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="ed218-120">Виртуализация повторно используемых фрагментов</span><span class="sxs-lookup"><span data-stu-id="ed218-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="ed218-121">Компоненты обеспечивают удобный подход для создания многократно используемых фрагментов кода и разметки.</span><span class="sxs-lookup"><span data-stu-id="ed218-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="ed218-122">Как правило, рекомендуется создавать отдельные компоненты, которые наилучшим образом согласовываются с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="ed218-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="ed218-123">Одним из недостатков является то, что каждый дополнительный дочерний компонент увеличивает общее время, необходимое для отрисовки родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="ed218-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="ed218-124">Для большинства приложений этими дополнительными накладными расходами можно пренебречь.</span><span class="sxs-lookup"><span data-stu-id="ed218-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="ed218-125">Однако в случае с приложениями, которые создают большое количество компонентов, следует предусмотреть стратегии по снижению затрат на обработку, например путем ограничения количества отрисовываемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="ed218-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="ed218-126">Для получения дополнительной информации см. <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="ed218-126">For more information, see <xref:blazor/components/virtualization>.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="ed218-127">Избегайте использования взаимодействия с JavaScript для маршалирования данных</span><span class="sxs-lookup"><span data-stu-id="ed218-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="ed218-128">В Blazor WebAssembly вызов взаимодействия с JavaScript (JS) должен пересекать границу между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="ed218-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="ed218-129">Сериализация и десериализация содержимого в двух контекстах вызывают дополнительные накладные расходы на обработку в приложении.</span><span class="sxs-lookup"><span data-stu-id="ed218-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="ed218-130">Частые вызовы взаимодействия с JS часто негативно влияют на производительность.</span><span class="sxs-lookup"><span data-stu-id="ed218-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="ed218-131">Чтобы уменьшить объем маршалирования данных через границу, определите, может ли приложение объединить множество небольших фрагментов полезных данных в один большой фрагмент. Это позволит избежать частых переключений контекста между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="ed218-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="ed218-132">Использование System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="ed218-132">Use System.Text.Json</span></span>

<span data-ttu-id="ed218-133">Реализация взаимодействия с JS в Blazor основана на <xref:System.Text.Json>, высокопроизводительной библиотеке сериализации JSON, занимающей мало места в памяти.</span><span class="sxs-lookup"><span data-stu-id="ed218-133">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="ed218-134">Использование <xref:System.Text.Json> не приводит к увеличению размера полезных данных в приложении, в отличие от добавления одной или нескольких альтернативных библиотек JSON.</span><span class="sxs-lookup"><span data-stu-id="ed218-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="ed218-135">Руководство по миграции см. в статье [Миграция с `Newtonsoft.Json` на `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="ed218-135">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="ed218-136">Использование синхронных и немаршалируемых интерфейсов API взаимодействия с JS, где они применимы</span><span class="sxs-lookup"><span data-stu-id="ed218-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="ed218-137">Blazor WebAssembly предлагает две дополнительные версии <xref:Microsoft.JSInterop.IJSRuntime>, помимо версии, доступной для приложений Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="ed218-137">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="ed218-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> позволяет синхронно совершать вызовы взаимодействия с JS, что требует меньше ресурсов, чем асинхронные вызовы:</span><span class="sxs-lookup"><span data-stu-id="ed218-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="ed218-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает немаршалируемые вызовы взаимодействия с JS:</span><span class="sxs-lookup"><span data-stu-id="ed218-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="ed218-140">Хотя использование <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает наименьшие издержки по сравнению с другими подходами к взаимодействию с JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими интерфейсами API, в настоящее время не документированы и могут подвернуться критическим изменениям в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="ed218-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="ed218-141">Уменьшение размера приложения</span><span class="sxs-lookup"><span data-stu-id="ed218-141">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="ed218-142">Обрезка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="ed218-142">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="ed218-143">[Обрезка неиспользуемых сборок в приложении Blazor WebAssembly](xref:blazor/host-and-deploy/configure-trimmer) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="ed218-143">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="ed218-144">По умолчанию средство обрезки выполняется при публикации приложения.</span><span class="sxs-lookup"><span data-stu-id="ed218-144">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="ed218-145">Чтобы воспользоваться обрезкой, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="ed218-145">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="ed218-146">Компоновка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="ed218-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="ed218-147">[Компоновка приложения Blazor WebAssembly](xref:blazor/host-and-deploy/configure-linker) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="ed218-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="ed218-148">По умолчанию компоновщик промежуточного языка (IL) включается только при сборке в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="ed218-148">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="ed218-149">Чтобы воспользоваться этой возможностью, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="ed218-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="ed218-150">Сборки с отложенной загрузкой</span><span class="sxs-lookup"><span data-stu-id="ed218-150">Lazy load assemblies</span></span>

<span data-ttu-id="ed218-151">Загрузка сборок во время выполнения на том этапе, когда они необходимы для маршрута.</span><span class="sxs-lookup"><span data-stu-id="ed218-151">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="ed218-152">Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="ed218-152">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="ed218-153">Сжатие</span><span class="sxs-lookup"><span data-stu-id="ed218-153">Compression</span></span>

<span data-ttu-id="ed218-154">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="ed218-154">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="ed218-155">Blazor использует сервер для согласования содержимого и предоставления статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="ed218-155">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="ed218-156">После развертывания приложения убедитесь в том, что приложение предоставляет сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="ed218-156">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="ed218-157">В браузере откройте вкладку "Сеть" в Средствах для разработчиков и убедитесь в том, что файлы предоставляются с `Content-Encoding: br` или `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="ed218-157">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="ed218-158">Если узел не предоставляет сжатые файлы, выполните инструкции в разделе <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="ed218-158">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="ed218-159">Отключение неиспользуемых функций</span><span class="sxs-lookup"><span data-stu-id="ed218-159">Disable unused features</span></span>

<span data-ttu-id="ed218-160">Среда выполнения Blazor WebAssembly включает в себя следующие функции .NET, которые можно отключить, если они не требуются приложению, для уменьшения размера полезных данных:</span><span class="sxs-lookup"><span data-stu-id="ed218-160">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="ed218-161">Для обеспечения правильности сведений о часовом поясе включается файл данных.</span><span class="sxs-lookup"><span data-stu-id="ed218-161">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="ed218-162">Если приложению не нужна эта функция, ее можно отключить, присвоив свойству MSBuild `BlazorEnableTimeZoneSupport` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="ed218-162">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="ed218-163">По умолчанию Blazor WebAssembly содержит ресурсы глобализации, необходимые для отображения значений, таких как даты и денежные единицы, на языке пользователя и с его региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="ed218-163">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="ed218-164">Если приложению не требуется локализация, можно [настроить поддержку инвариантных языка и региональных параметров](xref:blazor/globalization-localization), основанных на языке и региональных параметрах `en-US`:</span><span class="sxs-lookup"><span data-stu-id="ed218-164">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="ed218-165">Для правильной работы таких интерфейсов API, как <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>, включаются сведения о параметрах сортировки.</span><span class="sxs-lookup"><span data-stu-id="ed218-165">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="ed218-166">Если вы уверены, что приложению не нужны сведения о параметрах сортировки, эту функцию можно отключить, присвоив свойству MSBuild `BlazorWebAssemblyPreserveCollationData` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="ed218-166">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
