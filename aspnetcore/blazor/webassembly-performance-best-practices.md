---
title: Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly
author: pranavkm
description: Советы по повышению производительности приложений ASP.NET Core Blazor WebAssembly и избежанию распространенных проблем с производительностью.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: c5169231eec67a43830f761bff7585deff774613
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103491"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="b54de-103">Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b54de-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="b54de-104">Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)</span><span class="sxs-lookup"><span data-stu-id="b54de-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="b54de-105">В этой статье приводятся рекомендации по обеспечению производительности ASP.NET Core Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b54de-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="b54de-106">Устранение лишних отрисовок компонентов</span><span class="sxs-lookup"><span data-stu-id="b54de-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="b54de-107">Алгоритм сравнения Blazor позволяет избежать повторной отрисовки компонентов, которые не изменялись.</span><span class="sxs-lookup"><span data-stu-id="b54de-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="b54de-108">Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> для точного управления отрисовкой компонентов.</span><span class="sxs-lookup"><span data-stu-id="b54de-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="b54de-109">При создании компонента, который используется только в пользовательском интерфейсе и не изменяется после первоначальной отрисовки, настройте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> так, чтобы он возвращал значение `false`:</span><span class="sxs-lookup"><span data-stu-id="b54de-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="b54de-110">Большинству приложений не требуется детализированный контроль, однако метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> можно также использовать для выборочной отрисовки компонентов, отвечающих на события пользовательского интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b54de-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="b54de-111">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="b54de-111">In the following example:</span></span>

* <span data-ttu-id="b54de-112">Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> переопределяется, и ему присваивается значение поля <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, которое при загрузке компонента изначально имеет значение `false`.</span><span class="sxs-lookup"><span data-stu-id="b54de-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="b54de-113">При нажатии кнопки методу <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> присваивается значение `true`, что приводит к повторной отрисовке компонента с обновленным `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="b54de-113">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="b54de-114">Сразу после повторной отрисовки <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> снова устанавливает для <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> значение `false`, чтобы предотвратить дальнейшую повторную отрисовку до тех пор, пока кнопка не будет нажата еще раз.</span><span class="sxs-lookup"><span data-stu-id="b54de-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="b54de-115">Для получения дополнительной информации см. <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="b54de-115">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="b54de-116">Виртуализация повторно используемых фрагментов</span><span class="sxs-lookup"><span data-stu-id="b54de-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="b54de-117">Компоненты обеспечивают удобный подход для создания многократно используемых фрагментов кода и разметки.</span><span class="sxs-lookup"><span data-stu-id="b54de-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="b54de-118">Как правило, рекомендуется создавать отдельные компоненты, которые наилучшим образом согласовываются с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="b54de-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="b54de-119">Одним из недостатков является то, что каждый дополнительный дочерний компонент увеличивает общее время, необходимое для отрисовки родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="b54de-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="b54de-120">Для большинства приложений этими дополнительными накладными расходами можно пренебречь.</span><span class="sxs-lookup"><span data-stu-id="b54de-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="b54de-121">Однако в случае с приложениями, которые создают большое количество компонентов, следует предусмотреть стратегии по снижению затрат на обработку, например путем ограничения количества отрисовываемых компонентов.</span><span class="sxs-lookup"><span data-stu-id="b54de-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="b54de-122">Так, отрисовка сетки или списка с сотнями строк, содержащих компоненты, является ресурсоемкой задачей.</span><span class="sxs-lookup"><span data-stu-id="b54de-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="b54de-123">Вы можете виртуализировать макет сетки или списка, чтобы в любой момент времени отрисовывалось только подмножество компонентов.</span><span class="sxs-lookup"><span data-stu-id="b54de-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="b54de-124">Примером отрисовки подмножества компонентов могут служить следующие компоненты в [примере приложения с виртуализацией (в репозитории GitHub aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="b54de-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="b54de-125">компонент `Virtualize` ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)) — компонент на C#, который реализует <xref:Microsoft.AspNetCore.Components.ComponentBase> для отрисовки набора строк данных по погоде при выполнении прокрутки пользователем;</span><span class="sxs-lookup"><span data-stu-id="b54de-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="b54de-126">Компонент `FetchData` ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): использует компонент `Virtualize` для вывода 25 строк данных по погоде за раз.</span><span class="sxs-lookup"><span data-stu-id="b54de-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="b54de-127">Избегайте использования взаимодействия с JavaScript для маршалирования данных</span><span class="sxs-lookup"><span data-stu-id="b54de-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="b54de-128">В Blazor WebAssembly вызов взаимодействия с JavaScript (JS) должен пересекать границу между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="b54de-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="b54de-129">Сериализация и десериализация содержимого в двух контекстах вызывают дополнительные накладные расходы на обработку в приложении.</span><span class="sxs-lookup"><span data-stu-id="b54de-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="b54de-130">Частые вызовы взаимодействия с JS часто негативно влияют на производительность.</span><span class="sxs-lookup"><span data-stu-id="b54de-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="b54de-131">Чтобы уменьшить объем маршалирования данных через границу, определите, может ли приложение объединить множество небольших фрагментов полезных данных в один большой фрагмент. Это позволит избежать частых переключений контекста между WebAssembly и JS.</span><span class="sxs-lookup"><span data-stu-id="b54de-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="b54de-132">Использование System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="b54de-132">Use System.Text.Json</span></span>

<span data-ttu-id="b54de-133">Реализация взаимодействия с JS в Blazor основана на <xref:System.Text.Json>, высокопроизводительной библиотеке сериализации JSON, занимающей мало места в памяти.</span><span class="sxs-lookup"><span data-stu-id="b54de-133">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="b54de-134">Использование <xref:System.Text.Json> не приводит к увеличению размера полезных данных в приложении, в отличие от добавления одной или нескольких альтернативных библиотек JSON.</span><span class="sxs-lookup"><span data-stu-id="b54de-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="b54de-135">Указания по миграции см. в статье [Миграция из Newtonsoft.Json в System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="b54de-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="b54de-136">Использование синхронных и немаршалируемых интерфейсов API взаимодействия с JS, где они применимы</span><span class="sxs-lookup"><span data-stu-id="b54de-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="b54de-137"> WebAssembly предлагает две дополнительные версии <xref:Microsoft.JSInterop.IJSRuntime>, помимо версии, доступной для серверных приложений Blazor.</span><span class="sxs-lookup"><span data-stu-id="b54de-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="b54de-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> позволяет синхронно совершать вызовы взаимодействия с JS, что требует меньше ресурсов, чем асинхронные вызовы:</span><span class="sxs-lookup"><span data-stu-id="b54de-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="b54de-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает немаршалируемые вызовы взаимодействия с JS:</span><span class="sxs-lookup"><span data-stu-id="b54de-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="b54de-140">Хотя использование <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает наименьшие издержки по сравнению с другими подходами к взаимодействию с JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими интерфейсами API, в настоящее время не документированы и могут подвернуться критическим изменениям в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="b54de-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="b54de-141">Уменьшение размера приложения</span><span class="sxs-lookup"><span data-stu-id="b54de-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="b54de-142">Компоновка промежуточного языка (IL)</span><span class="sxs-lookup"><span data-stu-id="b54de-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="b54de-143">[Компоновка приложения Blazor WebAssembly](xref:blazor/host-and-deploy/configure-linker) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения.</span><span class="sxs-lookup"><span data-stu-id="b54de-143">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="b54de-144">По умолчанию компоновщик включается только при сборке в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="b54de-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="b54de-145">Чтобы воспользоваться этой возможностью, опубликуйте приложение для развертывания с помощью команды [dotnet publish](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:</span><span class="sxs-lookup"><span data-stu-id="b54de-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="b54de-146">Сжатие</span><span class="sxs-lookup"><span data-stu-id="b54de-146">Compression</span></span>

<span data-ttu-id="b54de-147">При публикации приложения Blazor WebAssembly выходные данные статически сжимаются во время публикации, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="b54de-147">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="b54de-148"> использует сервер для согласования содержимого и предоставления статически сжатых файлов.</span><span class="sxs-lookup"><span data-stu-id="b54de-148"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="b54de-149">После развертывания приложения убедитесь в том, что приложение предоставляет сжатые файлы.</span><span class="sxs-lookup"><span data-stu-id="b54de-149">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="b54de-150">В браузере откройте вкладку "Сеть" в Средствах для разработчиков и убедитесь в том, что файлы предоставляются с `Content-Encoding: br` или `Content-Encoding: gz`.</span><span class="sxs-lookup"><span data-stu-id="b54de-150">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="b54de-151">Если узел не предоставляет сжатые файлы, выполните инструкции в разделе <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="b54de-151">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="b54de-152">Отключение неиспользуемых функций</span><span class="sxs-lookup"><span data-stu-id="b54de-152">Disable unused features</span></span>

<span data-ttu-id="b54de-153">Среда выполнения Blazor WebAssembly включает в себя следующие функции .NET, которые можно отключить, если они не требуются приложению для уменьшения размера полезных данных:</span><span class="sxs-lookup"><span data-stu-id="b54de-153">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="b54de-154">Для обеспечения правильности сведений о часовом поясе включается файл данных.</span><span class="sxs-lookup"><span data-stu-id="b54de-154">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="b54de-155">Если приложению не нужна эта функция, ее можно отключить, присвоив свойству MSBuild `BlazorEnableTimeZoneSupport` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="b54de-155">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="b54de-156">Для правильной работы таких интерфейсов API, как <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>, включаются сведения о параметрах сортировки.</span><span class="sxs-lookup"><span data-stu-id="b54de-156">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="b54de-157">Если вы уверены, что приложению не нужны сведения о параметрах сортировки, эту функцию можно отключить, присвоив свойству MSBuild `BlazorWebAssemblyPreserveCollationData` в файле проекта приложения значение `false`:</span><span class="sxs-lookup"><span data-stu-id="b54de-157">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
