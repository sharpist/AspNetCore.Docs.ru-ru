---
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>Сложные сценарии ASP.NET Core Blazor

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

## <a name="blazor-server-circuit-handler"></a>Обработчик цепи Blazor Server

Blazor Server позволяет коду определить *обработчик канала*, чтобы выполнять код при изменении состояния канала пользователя. Обработчик канала реализуется путем наследования от `CircuitHandler` и регистрации класса в контейнере службы приложения. В следующем примере обработчик канала отслеживает открытые соединения SignalR:

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

Обработчики канала регистрируются с помощью DI. Экземпляры с заданной областью создаются для каждого экземпляра канала. С помощью `TrackingCircuitHandler` в предыдущем примере создается отдельная служба, поскольку необходимо отслеживать состояние всех каналов:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

Если методы пользовательского обработчика канала создают необработанное исключение, это исключение является неустранимым для канала Blazor Server. Чтобы допускать исключения в коде обработчика или вызываемых методах, заключите код в один или несколько операторов [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.

Когда канал завершается из-за того, что пользователь отключился и платформа очищает состояние канала, платформа удаляет область DI канала. При удалении области удаляются все службы DI, ограниченные каналом и реализующие <xref:System.IDisposable?displayProperty=fullName>. Если любая служба DI вызывает необработанное исключение во время удаления, платформа регистрирует исключение.

## <a name="manual-rendertreebuilder-logic"></a>Выполняемая вручную логика RenderTreeBuilder

<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> предоставляет методы для управления компонентами и элементами, включая создание компонентов вручную в коде C#.

> [!NOTE]
> Использование <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов является сложным сценарием. Неправильно сформированный компонент (например, незакрытый тег разметки) может привести к неопределенному поведению.

Рассмотрим следующий компонент `PetDetails`, который можно вручную встроить в другой компонент:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

В следующем примере цикл в методе `CreateComponent` создает три компонента `PetDetails`. При вызове методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов (`OpenComponent` и `AddAttribute`) порядковые номера представляют собой номера строк исходного кода. Алгоритм сравнения Blazor использует порядковые номера, соответствующие отдельным строкам кода, а не отдельным вызовам. При создании компонента с помощью методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> жестко задавайте аргументы для порядковых номеров. **Использование вычисления или счетчика для формирования порядкового номера может привести к снижению производительности.** Дополнительные сведения см. в разделе [Порядковые номера относятся к номерам строк кода, а не порядку выполнения](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).

Компонент `BuiltContent`:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Типы в <xref:Microsoft.AspNetCore.Components.RenderTree> позволяют обрабатывать *результаты* операций отрисовки. Это внутренние сведения реализации платформы Blazor. Эти типы следует считать *нестабильными*. Они могут измениться в будущих выпусках.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Порядковые номера относятся к номерам строк кода, а не порядку выполнения

Файлы компонентов Razor (*RAZOR*) всегда компилируются. Компиляция потенциально лучше, чем интерпретация кода, поскольку шаг компиляции можно использовать для вставки сведений, повышающих производительность приложения во время выполнения.

Ключевой пример этих усовершенствований связан с *порядковыми номерами*. Порядковые номера указывают среде выполнения, какие выходные данные поступили из отдельных и упорядоченных строк кода. Среда выполнения использует эти сведения для эффективного сравнения деревьев в линейном времени, а это гораздо быстрее, чем при использовании общего алгоритма сравнения деревьев.

Рассмотрим следующий файл компонента Razor (*RAZOR*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Предыдущий код компилируется примерно следующим образом:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Когда код выполняется в первый раз, если `someFlag` имеет значение `true`, построитель получит следующее:

| Sequence | Type      | Данные   |
| :---
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

----- | :----: | | 0        | Текстовый узел | Первый  | | 1        | Текстовый узел | Второй |

Представьте, что `someFlag` становится `false`, и разметка снова преобразуется для просмотра. На этот раз построитель получает:

| Sequence | Type       | Данные   |
| :---
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

----- | :----: | | 1        | Текстовый узел  | Второй |

Когда среда выполнения выполняет сравнение, она видит, что элемент в последовательности `0` был удален, поэтому создает следующий тривиальный *сценарий изменения*:

* Удаление первого текстового узла.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Проблема с созданием порядковых номеров программным способом

Представьте себе, что вместо этого вы написали следующую логику отрисовки построителя деревьев:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

И теперь первыми выходными данными будет:

| Sequence | Type      | Данные   |
| :---
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

----- | :----: | | 0        | Текстовый узел | Первый  | | 1        | Текстовый узел | Второй |

Этот результат идентичен предыдущему случаю, поэтому проблемы не возникают. `someFlag` имеет значение `false` во второй отрисовке, а выходные данные следующие:

| Sequence | Type      | Данные   |
| :---
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

----- | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

--- | | 0        | Текстовый узел | Второй |

На этот раз алгоритм сравнения видит, что произошло *два* изменения, и создает следующий скрипт редактирования:

* Изменение значения первого текстового узла на `Second`.
* Удаление второго текстового узла.

При формировании порядковых номеров теряются все полезные сведения о том, где в исходном коде находятся циклы и ветви `if/else`. Это приводит к тому, что сравнение становится **в два раза больше**.

Это упрощенный пример. В более реалистичных случаях со сложными и глубокими вложенными структурами, особенно с циклами, затраты на производительность обычно выше. Вместо того чтобы сразу определять, какие блоки или ветви цикла были вставлены или удалены, алгоритм сравнения должен выполнять рекурсивный глубокий переход к деревьям отрисовки. В результате, как правило, приходится создавать более длинные скрипты изменений, поскольку алгоритм сравнения не знает всей информации о том, как старые и новые структуры связаны друг с другом.

### <a name="guidance-and-conclusions"></a>Рекомендации и выводы

* Производительность приложения снижается, если порядковые номера создаются динамически.
* Платформа не может автоматически создавать собственные порядковые номера во время выполнения, поскольку необходимая информация не существует, если только она не получена во время компиляции.
* Не записывайте длинные блоки логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, реализуемой вручную. Используйте файлы *RAZOR* и дайте компилятору обрабатывать порядковые номера. Если не удается избежать реализуемой вручную логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, разделите длинные блоки кода на более мелкие части, заключенные в вызовы <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>. Каждый регион имеет собственное отдельное пространство порядковых номеров, поэтому вы можете снова начинать с нуля (или любого другого произвольного числа) внутри каждого региона.
* Если порядковые номера задаются жестко, то для алгоритма сравнения требуется только, чтобы порядковые номера увеличивались. Начальное значение и пропуски несущественны. Кроме того, можно использовать номер строки кода в качестве порядкового номера или начать с нуля и увеличивать значение на единицы или сотни (или выбрать любой другой интервал). 
* Blazor использует порядковые номера, а другие платформы пользовательского интерфейса для сравнения деревьев не используют их. Сравнение выполняется гораздо быстрее, если используются порядковые номера, и Blazor имеет преимущество этапа компиляции, который автоматически обрабатывает порядковые номера для разработчиков, создающих файлы *RAZOR*.

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a>Выполнение передачи больших объемов данных в приложениях Blazor Server

В некоторых сценариях необходимо передавать большие объемы данных между JavaScript и Blazor. Как правило, передача больших данных происходит в следующих случаях:

* API-интерфейсы файловой системы браузера используются для отправки или загрузки файла.
* Требуется взаимодействие со сторонней библиотекой.

В Blazor Server есть ограничение, предотвращающее передачу единичных крупных сообщений, которые могут привести к проблемам с производительностью.

При разработке кода, который передает данные между JavaScript и Blazor, учитывайте следующие рекомендации:

* Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.
* Не выделяйте большие объекты в коде C# и JavaScript.
* Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.
* Освободите занятую память при завершении или отмене процесса.
* Применяйте следующие дополнительные требования в целях безопасности:
  * Объявите максимальный размер файла или данных, который может быть передан.
  * Объявите минимальную скорость передачи от клиента к серверу.
* После получения данных сервером данные могут быть:
  * Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.
  * Использованы немедленно. Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.

Следующий класс отправителя файлов обрабатывает взаимодействие JS с клиентом. Класс отправителя использует взаимодействие JS, чтобы:

* Опросить клиент для отправки сегмента данных.
* Прервать транзакцию при истечении времени опроса.

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

В предшествующем примере:

* Для `maxBase64SegmentSize` задано значение `8192`, которое вычисляется на основе `maxBase64SegmentSize = segmentSize * 4 / 3`.
* Низкоуровневые API управления памятью .NET Core используются для хранения сегментов памяти на сервере в `uploadedSegments`.
* Для отправки через взаимодействие с JS используется метод `ReceiveFile`:
  * Размер файла определяется в байтах с помощью взаимодействия JS с `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.
  * Количество принимаемых сегментов вычисляется и сохраняется в `numberOfSegments`.
  * Сегменты запрашиваются в цикле `for` через взаимодействие JS с `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`. Все сегменты, кроме последнего, должны иметь размер 8192 байт перед декодированием. Клиент должен отправлять данные эффективно.
  * Для каждого полученного сегмента проверки выполняются перед декодированием с помощью <xref:System.Convert.TryFromBase64String%2A>.
  * Поток с данными возвращается в виде нового <xref:System.IO.Stream> (`SegmentedStream`) после завершения передачи.

Класс сегментированного потока предоставляет список сегментов как недоступный для поиска <xref:System.IO.Stream> только для чтения:

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

Следующий код реализует функции JavaScript для получения данных:

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
