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
# <a name="aspnet-core-blazor-lifecycle"></a>Жизненный цикл ASP.NET Core Blazor

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Платформа Blazor содержит синхронные и асинхронные методы жизненного цикла. Переопределяйте методы жизненного цикла для выполнения дополнительных операций с компонентами во время инициализации и отрисовки компонента.

## <a name="lifecycle-methods"></a>Методы жизненного цикла

### <a name="component-initialization-methods"></a>Методы инициализации компонента

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> вызываются, когда компонент инициализируется после получения начальных параметров от родительского компонента. Используйте `OnInitializedAsync`, когда компонент выполняет асинхронную операцию и должен обновляться после завершения операции.

Для синхронной операции переопределите `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Чтобы выполнить асинхронную операцию, переопределите `OnInitializedAsync` и используйте в операции ключевое слово `await`:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Приложения Blazor Server, которые [предварительно отрисовывают свое содержимое](xref:blazor/hosting-model-configuration#render-mode), вызывают `OnInitializedAsync` **_дважды_** :

* Один раз, когда компонент изначально отрисовывается статически как часть страницы.
* Второй раз, когда браузер устанавливает соединение с сервером.

Чтобы предотвратить выполнение кода разработчика в `OnInitializedAsync` дважды, см. раздел [Повторное подключение с отслеживанием состояния после предварительной отрисовки](#stateful-reconnection-after-prerendering).

Когда приложение Blazor Server выполняет предварительную отрисовку, некоторые действия, такие как вызов JavaScript, невозможны, так как соединение с браузером не установлено. При предварительной отрисовке компоненты могут отрисовываться иначе. Дополнительные сведения см. в разделе [Обнаружение предварительной отрисовки в приложении](#detect-when-the-app-is-prerendering).

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).

### <a name="before-parameters-are-set"></a>До указания параметров

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> задает параметры, предоставляемые родительским элементом компонента в дереве отрисовки:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> содержит весь набор значений параметров при каждом вызове `SetParametersAsync`.

Реализация `SetParametersAsync` по умолчанию задает значение каждого свойства с атрибутом `[Parameter]` или `[CascadingParameter]`, имеющим соответствующее значение в `ParameterView`. Параметры, у которых нет соответствующего значения в `ParameterView`, остаются неизменными.

Если `base.SetParametersAync` не вызывается, пользовательский код может интерпретировать значение входящих параметров любым необходимым образом. Например, нет необходимости назначать входящие параметры свойствам класса.

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).

### <a name="after-parameters-are-set"></a>После указания параметров

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> вызываются:

* Когда компонент инициализируется и получил первый набор параметров от родительского компонента.
* Когда родительский компонент повторно отрисовывается и предоставляет:
  * Только известные примитивные неизменяемые типы, у которых изменился по крайней мере один параметр.
  * Любые параметры сложных типов. Платформа не может определить, изменились ли значения параметров сложного типа внутри, поэтому она рассматривает набор параметров как измененный.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Асинхронная работа при применении параметров и значений свойств должна происходить во время события жизненного цикла `OnParametersSetAsync`.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).

### <a name="after-component-render"></a>После отрисовки компонента

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> и <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> вызываются после завершения отрисовки компонента. В этот момент указываются ссылки на элементы и компоненты. Используйте этот этап для выполнения дополнительных шагов инициализации с помощью отрисованного содержимого, например для активации сторонних библиотек JavaScript, которые работают с отрисованными элементами DOM.

Параметр `firstRender` для `OnAfterRenderAsync` и `OnAfterRender`:

* Устанавливается в значение `true` при первой отрисовке экземпляра компонента.
* Может использоваться, чтобы гарантировать однократное выполнение инициализации.

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
> Асинхронная работа сразу после отрисовки должна произойти во время события жизненного цикла `OnAfterRenderAsync`.
>
> Даже если вы возвращаете <xref:System.Threading.Tasks.Task> из `OnAfterRenderAsync`, платформа не планирует дальнейший цикл отрисовки компонента после завершения задачи. Это позволяет избежать бесконечного цикла отрисовки. Это поведение отличается от других методов жизненного цикла, которые планируют дальнейший цикл отрисовки после завершения возвращаемой задачи.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` и `OnAfterRenderAsync` *не вызываются при предварительной отрисовке на сервере.*

Если настроены какие-либо обработчики событий, отсоедините их при удалении. Дополнительные сведения см. в разделе [Удаление компонентов с помощью IDisposable](#component-disposal-with-idisposable).

### <a name="suppress-ui-refreshing"></a>Подавление обновления пользовательского интерфейса

Переопределите <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, чтобы отключить обновление пользовательского интерфейса. Если реализация возвращает `true`, пользовательский интерфейс обновляется:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` вызывается каждый раз при отрисовке компонента.

Даже при переопределении `ShouldRender` компонент всегда проходит первоначальную отрисовку.

## <a name="state-changes"></a>Изменения состояния

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> уведомляет компонент о том, что его состояние изменилось. В соответствующих случаях вызов `StateHasChanged` приводит к повторной отрисовке компонента.

## <a name="handle-incomplete-async-actions-at-render"></a>Обработка незавершенных асинхронных действий при отрисовке

Асинхронные действия, выполняемые в событиях жизненного цикла, могут не завершиться до отрисовки компонента. Во время выполнения метода жизненного цикла объекты могут быть `null` или заполнены данными не полностью. Предоставьте логику отрисовки для подтверждения инициализации объектов. Отрисуйте элементы пользовательского интерфейса заполнителя (например, сообщение загрузки), пока объекты имеют значение `null`.

В компоненте `FetchData` шаблонов Blazor `OnInitializedAsync` переопределяется для асинхронного получения данных прогноза (`forecasts`). Если `forecasts` имеет значение `null`, пользователю выводится сообщение о загрузке. Когда элемент `Task`, возвращенный `OnInitializedAsync`, завершается, компонент отрисовывается повторно с обновленным состоянием.

*Pages/FetchData.razor* в шаблоне Blazor Server:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Освобождение компонентов с помощью IDisposable

Если компонент реализует <xref:System.IDisposable>, [метод Dispose](/dotnet/standard/garbage-collection/implementing-dispose) вызывается при удалении компонента из пользовательского интерфейса. Следующий компонент использует `@implements IDisposable` и метод `Dispose`:

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
> Вызов <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> в `Dispose` не поддерживается. `StateHasChanged` может вызываться в процессе уничтожения отрисовщика, поэтому запрос обновлений пользовательского интерфейса на этом этапе не поддерживается.

Отмена подписки на обработчики событий из событий .NET. В следующих примерах [формы Blazor](xref:blazor/forms-validation) показано, как отсоединить обработчик событий в методе `Dispose`.

* Подход с использованием частного поля и лямбда-выражения

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Подход с использованием частного метода

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Обработка ошибок

Сведения об обработке ошибок во время выполнения метода жизненного цикла см. в разделе <xref:blazor/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Повторное подключение с отслеживанием состояния после предварительной отрисовки

В приложении Blazor Server, если `RenderMode` имеет значение `ServerPrerendered`, компонент изначально отрисовывается статически как часть страницы. После того как браузер установит соединение с сервером, компонент отрисовывается *снова* и становится интерактивным. Если метод жизненного цикла [OnInitialized{Async}](#component-initialization-methods) для инициализации компонента присутствует, он выполняется *дважды*:

* Когда компонент предварительно отрисовывается статически.
* После установления соединения с сервером.

Это может привести к заметному изменению данных, отображаемых в пользовательском интерфейсе, когда компонент отрисовывается окончательно.

Чтобы избежать сценария двойной отрисовки в приложении Blazor Server, выполните следующие действия:

* Передайте идентификатор, который можно использовать для кэширования состояния во время предварительной отрисовки и получения состояния после перезапуска приложения.
* Используйте идентификатор во время предварительной отрисовки, чтобы сохранить состояние компонента.
* Используйте идентификатор после предварительной отрисовки, чтобы получить кэшированное состояние.

В следующем коде показан обновленный элемент `WeatherForecastService` в приложении Blazor Server на основе шаблона без двойной отрисовки:

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

Дополнительные сведения об элементе `RenderMode` см. в разделе <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Обнаружение предварительной отрисовки приложения

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a>Отменяемая фоновая операция

Компоненты часто выполняют длительные фоновые операции, например осуществление сетевых вызовов (<xref:System.Net.Http.HttpClient>) и взаимодействие с базами данных. В целях экономии системных ресурсов в ряде ситуаций желательно отключить выполнение фоновых операций. Например, фоновые асинхронные операции не останавливаются автоматически, когда пользователь выходит из компонента.

Ниже перечислены другие причины, по которым может потребоваться отмена фоновых рабочих элементов.

* Выполнение фоновой задачи было начато с ошибочными входными данными или параметрами обработки.
* Текущий набор выполняемых фоновых рабочих элементов должен быть заменен новым набором.
* Необходимо изменить приоритет выполняемых в данный момент задач.
* Необходимо завершить работу приложения, чтобы повторно развернуть его на сервере.
* Ресурсы сервера становятся ограниченными, в связи с чем возникает необходимость в пересмотре сроков выполнения фоновых рабочих элементов.

Чтобы реализовать механизм отменяемой фоновой операции в компоненте, выполните следующие действия.

* Используйте <xref:System.Threading.CancellationTokenSource> и <xref:System.Threading.CancellationToken>.
* При [удалении компонента](#component-disposal-with-idisposable) и в любой момент, когда требуется выполнить отмену путем отмены токена вручную, вызовите [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A), чтобы сообщить о необходимости отмены фоновой операции.
* После возврата асинхронного вызова вызовите <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> для токена.

В следующем примере:

* `await Task.Delay(5000, cts.Token);` представляет длительную асинхронную фоновую операцию.
* `BackgroundResourceMethod` представляет длительный фоновый метод, который не должен запускаться, если `Resource` удаляется перед вызовом метода.

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
