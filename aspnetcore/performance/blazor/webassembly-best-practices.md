---
Title: "ASP.NET Core Blazor рекомендации по повышению производительности веб сборки" Автор: описание: "советы по повышению производительности в ASP.NET Core приложениях веб – Blazor сборки и устранении распространенных проблем с производительностью".
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a>Рекомендации по производительности ASP.NET Coreной Blazor сборки

По [получения кришнамурси](https://github.com/pranavkm)

В этой статье приводятся рекомендации по ASP.NET Core Blazor рекомендациям по производительности сборки.

## <a name="avoid-unnecessary-component-renders"></a>Избегайте визуализации ненужных компонентов

Blazorпозволяет избежать повторного отображения компонента, когда алгоритм воспринимает, что компонент не изменился. Переопределение <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> для точного управления отрисовкой компонентов.

При создании компонента только пользовательского интерфейса, который никогда не изменяется после первоначального рендеринга, настройте <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> для возврата `false` :

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Большинству приложений не требуется детализированный контроль, но их <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> также можно использовать для выборочного отображения компонента, отвечающего на событие пользовательского интерфейса.

В следующем примере:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>переопределяется и задается в качестве значения <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> поля, которое изначально `false` загружается при загрузке компонента.
* Если кнопка выбрана, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> для параметра задается значение `true` , которое заставляет компонент перерисовываться с обновленным `currentCount` .
* Сразу после <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> повторной визуализации устанавливает значение <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> обратно, `false` чтобы предотвратить дальнейшее повторное отображение до тех пор, пока не будет выбрана следующая кнопка.

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

Для получения дополнительной информации см. <xref:blazor/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Виртуализация повторно используемых фрагментов

Компоненты предлагают удобный подход к созданию многократно используемых фрагментов кода и разметки. Как правило, рекомендуется создавать отдельные компоненты, которые наилучшим образом согласовываются с требованиями приложения. Одно из предостережений заключается в том, что каждый дополнительный дочерний компонент вносит вклад в общее время, необходимое для визуализации родительского компонента. Для большинства приложений дополнительные издержки незначительны. Приложения, которые создают большое количество компонентов, должны использовать стратегии для снижения затрат на обработку, например для ограничения количества готовых к просмотру компонентов.

Например, сетка или список, которые отображают сотни строк, содержащих компоненты, являются ресурсоемкими для визуализации. Рассмотрите возможность виртуализации макета сетки или списка, чтобы подмножество компонентов отображалось в любое заданное время. Пример подготовки к просмотру подмножества компонентов см. в следующих компонентах [примера приложения виртуализации (в репозитории ASPNET/Samples GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`Component ([Shared/виртуализировать. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): компонент, написанный на языке C#, реализующий <xref:Microsoft.AspNetCore.Components.ComponentBase> для визуализации набора строк данных погоды на основе прокрутки пользователя.
* `FetchData`Component ([pages/FetchData. Razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): использует `Virtualize` компонент для вывода 25 строк данных о погоде за раз.

## <a name="avoid-javascript-interop-to-marshal-data"></a>Избегайте взаимодействия JavaScript для маршалирования данных

В Blazor этой сборке вызов взаимодействия JavaScript (JS) должен пройти через границу Assembly-JS. Сериализация и десериализация содержимого в двух контекстах создает дополнительную нагрузку на обработку приложения. Частые вызовы взаимодействия JS часто негативно влияют на производительность. Чтобы уменьшить объем маршалинга данных по границе, определите, может ли приложение консолидировать большое количество небольших полезных данных в одну большую полезную нагрузку, чтобы избежать большого объема переключения контекста между сборками и JS.

## <a name="use-systemtextjson"></a>Использование System. Text. JSON

BlazorРеализация взаимодействия JS основывается на <xref:System.Text.Json> высокопроизводительной библиотеке СЕРИАЛИЗАЦИИ JSON с нехваткой выделения памяти. Использование <xref:System.Text.Json> не приводит к дополнительным размерам полезных данных приложения, чем добавление одной или нескольких альтернативных библиотек JSON.

Инструкции по миграции см. в статье [Миграция из Newtonsoft. JSON в System. Text. JSON](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Используйте синхронные и неупакованные API взаимодействия JS, если это уместно

BlazorВеб – сборка предлагает две дополнительные версии <xref:Microsoft.JSInterop.IJSRuntime> по сравнению с одной версией, доступной для Blazor серверных приложений:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime>позволяет синхронно вызывать вызовы взаимодействия JS, что требует меньше ресурсов, чем асинхронные версии:

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

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>разрешает неупакованные вызовы взаимодействия JS:

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
  > Хотя использование <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> имеет минимальные издержки при подходе к взаимодействию JS, интерфейсы API JavaScript, необходимые для взаимодействия с этими API-интерфейсами, в настоящее время не документированы и подвержены критическим изменениям в будущих выпусках.

## <a name="reduce-app-size"></a>Уменьшить размер приложения

### <a name="intermediate-language-il-linking"></a>Компоновка промежуточного языка (IL)

[ Blazor Связывание Приложение "сборка](xref:host-and-deploy/blazor/configure-linker) " уменьшает размер приложения, обрезая неиспользуемый код в двоичных файлах приложения. По умолчанию компоновщик включается только при построении в `Release` конфигурации. Чтобы воспользоваться этим преимуществом, опубликуйте приложение для развертывания с помощью команды [DotNet Publish](/dotnet/core/tools/dotnet-publish) с параметром [-c |--Configuration](/dotnet/core/tools/dotnet-publish#options) , имеющим значение `Release` :

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a>Отключить неиспользуемые функции

BlazorСреда выполнения этой сборки включает следующие функции .NET, которые могут быть отключены, если приложение не требует их для меньшего размера полезных данных:

* Файл данных включается, чтобы сделать сведения о часовом поясе правильными. Если приложению не нужна эта функция, попробуйте отключить ее, задав `BlazorEnableTimeZoneSupport` для свойства MSBuild в файле проекта приложения `false` :

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* Сведения о параметрах сортировки включаются для того, чтобы интерфейсы API <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> работали правильно. Если вы уверены, что приложению не требуются данные параметров сортировки, рассмотрите возможность отключения, задав `BlazorWebAssemblyPreserveCollationData` для свойства MSBuild в файле проекта приложения `false` :

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
