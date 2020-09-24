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
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Рекомендации по повышению производительности ASP.NET Core Blazor WebAssembly

Автор: [Пранав Кришнамурти](https://github.com/pranavkm) (Pranav Krishnamoorthy)

В этой статье приводятся рекомендации по обеспечению высокой производительности ASP.NET Core Blazor WebAssembly.

## <a name="avoid-unnecessary-component-renders"></a>Устранение лишних отрисовок компонентов

Алгоритм сравнения Blazor позволяет избежать повторной отрисовки компонентов, которые не изменялись. Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> для точного управления отрисовкой компонентов.

При создании компонента, который используется только в пользовательском интерфейсе и не изменяется после первоначальной отрисовки, настройте метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> так, чтобы он возвращал значение `false`:

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Большинству приложений не требуется детализированный контроль, но метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> можно использовать для выборочной отрисовки компонентов, отвечающих на события пользовательского интерфейса. Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> также может потребоваться в сценариях с отрисовкой большого числа компонентов. Например, рассмотрим сетку, в которой использование <xref:Microsoft.AspNetCore.Components.EventCallback> в одном компоненте одной ячейки сетки вызывает метод <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> для всей сетки. При вызове <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> повторно отрисовываются все дочерние компоненты. Если нужно повторно отрисовать только несколько ячеек, используйте <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы не снижать производительность из-за ненужных операций.

В следующем примере:

* Метод <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> переопределяется, и ему присваивается значение поля <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, которое при загрузке компонента изначально имеет значение `false`.
* При нажатии кнопки методу <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> присваивается значение `true`, что приводит к повторной отрисовке компонента с обновленным `currentCount`.
* Сразу после повторной отрисовки <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> снова устанавливает для <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> значение `false`, чтобы предотвратить дальнейшую повторную отрисовку до тех пор, пока кнопка не будет нажата еще раз.

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

Для получения дополнительной информации см. <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Виртуализация повторно используемых фрагментов

Компоненты обеспечивают удобный подход для создания многократно используемых фрагментов кода и разметки. Как правило, рекомендуется создавать отдельные компоненты, которые наилучшим образом согласовываются с требованиями приложения. Одним из недостатков является то, что каждый дополнительный дочерний компонент увеличивает общее время, необходимое для отрисовки родительского компонента. Для большинства приложений этими дополнительными накладными расходами можно пренебречь. Однако в случае с приложениями, которые создают большое количество компонентов, следует предусмотреть стратегии по снижению затрат на обработку, например путем ограничения количества отрисовываемых компонентов.

Для получения дополнительной информации см. <xref:blazor/components/virtualization>.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Избегайте использования взаимодействия с JavaScript для маршалирования данных

В Blazor WebAssembly вызов взаимодействия с JavaScript (JS) должен пересекать границу между WebAssembly и JS. Сериализация и десериализация содержимого в двух контекстах вызывают дополнительные накладные расходы на обработку в приложении. Частые вызовы взаимодействия с JS часто негативно влияют на производительность. Чтобы уменьшить объем маршалирования данных через границу, определите, может ли приложение объединить множество небольших фрагментов полезных данных в один большой фрагмент. Это позволит избежать частых переключений контекста между WebAssembly и JS.

## <a name="use-systemtextjson"></a>Использование System.Text.Json

Реализация взаимодействия с JS в Blazor основана на <xref:System.Text.Json>, высокопроизводительной библиотеке сериализации JSON, занимающей мало места в памяти. Использование <xref:System.Text.Json> не приводит к увеличению размера полезных данных в приложении, в отличие от добавления одной или нескольких альтернативных библиотек JSON.

Руководство по миграции см. в статье [Миграция с `Newtonsoft.Json` на `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Использование синхронных и немаршалируемых интерфейсов API взаимодействия с JS, где они применимы

Blazor WebAssembly предлагает две дополнительные версии <xref:Microsoft.JSInterop.IJSRuntime>, помимо версии, доступной для приложений Blazor Server.

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> позволяет синхронно совершать вызовы взаимодействия с JS, что требует меньше ресурсов, чем асинхронные вызовы:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает немаршалируемые вызовы взаимодействия с JS:

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
  > Хотя использование <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> обеспечивает наименьшие издержки по сравнению с другими подходами к взаимодействию с JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими интерфейсами API, в настоящее время не документированы и могут подвернуться критическим изменениям в будущих выпусках.

## <a name="reduce-app-size"></a>Уменьшение размера приложения

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>Обрезка промежуточного языка (IL)

[Обрезка неиспользуемых сборок в приложении Blazor WebAssembly](xref:blazor/host-and-deploy/configure-trimmer) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения. По умолчанию средство обрезки выполняется при публикации приложения. Чтобы воспользоваться обрезкой, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Компоновка промежуточного языка (IL)

[Компоновка приложения Blazor WebAssembly](xref:blazor/host-and-deploy/configure-linker) уменьшает размер приложения за счет удаления неиспользуемого кода в двоичных файлах приложения. По умолчанию компоновщик промежуточного языка (IL) включается только при сборке в конфигурации `Release`. Чтобы воспользоваться этой возможностью, опубликуйте приложение для развертывания с помощью команды [`dotnet publish`](/dotnet/core/tools/dotnet-publish) с параметром [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), имеющим значение `Release`:

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Сборки с отложенной загрузкой

Загрузка сборок во время выполнения на том этапе, когда они необходимы для маршрута. Для получения дополнительной информации см. <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Сжатие

При публикации приложения Blazor WebAssembly выходные данные статически сжимаются, чтобы уменьшить размер приложения и исключить издержки на сжатие среды выполнения. Blazor использует сервер для согласования содержимого и предоставления статически сжатых файлов.

После развертывания приложения убедитесь в том, что приложение предоставляет сжатые файлы. В браузере откройте вкладку "Сеть" в Средствах для разработчиков и убедитесь в том, что файлы предоставляются с `Content-Encoding: br` или `Content-Encoding: gz`. Если узел не предоставляет сжатые файлы, выполните инструкции в разделе <xref:blazor/host-and-deploy/webassembly#compression>.

### <a name="disable-unused-features"></a>Отключение неиспользуемых функций

Среда выполнения Blazor WebAssembly включает в себя следующие функции .NET, которые можно отключить, если они не требуются приложению, для уменьшения размера полезных данных:

* Для обеспечения правильности сведений о часовом поясе включается файл данных. Если приложению не нужна эта функция, ее можно отключить, присвоив свойству MSBuild `BlazorEnableTimeZoneSupport` в файле проекта приложения значение `false`:

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* По умолчанию Blazor WebAssembly содержит ресурсы глобализации, необходимые для отображения значений, таких как даты и денежные единицы, на языке пользователя и с его региональными параметрами. Если приложению не требуется локализация, можно [настроить поддержку инвариантных языка и региональных параметров](xref:blazor/globalization-localization), основанных на языке и региональных параметрах `en-US`:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Для правильной работы таких интерфейсов API, как <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType>, включаются сведения о параметрах сортировки. Если вы уверены, что приложению не нужны сведения о параметрах сортировки, эту функцию можно отключить, присвоив свойству MSBuild `BlazorWebAssemblyPreserveCollationData` в файле проекта приложения значение `false`:

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
