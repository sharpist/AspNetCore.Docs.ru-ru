---
<span data-ttu-id="7e1da-101">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-101">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-103">'Blazor'</span></span>
- <span data-ttu-id="7e1da-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-104">'Identity'</span></span>
- <span data-ttu-id="7e1da-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-106">'Razor'</span></span>
- <span data-ttu-id="7e1da-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="7e1da-108">Сложные сценарии ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="7e1da-108">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="7e1da-109">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="7e1da-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="7e1da-110">Обработчик цепи Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7e1da-110">Blazor Server circuit handler</span></span>

Blazor<span data-ttu-id="7e1da-111"> Server позволяет коду определить *обработчик канала*, чтобы выполнять код при изменении состояния канала пользователя.</span><span class="sxs-lookup"><span data-stu-id="7e1da-111"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="7e1da-112">Обработчик канала реализуется путем наследования от `CircuitHandler` и регистрации класса в контейнере службы приложения.</span><span class="sxs-lookup"><span data-stu-id="7e1da-112">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="7e1da-113">В следующем примере обработчик канала отслеживает открытые соединения SignalR:</span><span class="sxs-lookup"><span data-stu-id="7e1da-113">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="7e1da-114">Обработчики канала регистрируются с помощью DI.</span><span class="sxs-lookup"><span data-stu-id="7e1da-114">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="7e1da-115">Экземпляры с заданной областью создаются для каждого экземпляра канала.</span><span class="sxs-lookup"><span data-stu-id="7e1da-115">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="7e1da-116">С помощью `TrackingCircuitHandler` в предыдущем примере создается отдельная служба, поскольку необходимо отслеживать состояние всех каналов:</span><span class="sxs-lookup"><span data-stu-id="7e1da-116">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="7e1da-117">Если методы пользовательского обработчика канала создают необработанное исключение, это исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7e1da-117">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="7e1da-118">Чтобы допускать исключения в коде обработчика или вызываемых методах, заключите код в один или несколько операторов [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="7e1da-118">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="7e1da-119">Когда канал завершается из-за того, что пользователь отключился и платформа очищает состояние канала, платформа удаляет область DI канала.</span><span class="sxs-lookup"><span data-stu-id="7e1da-119">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="7e1da-120">При удалении области удаляются все службы DI, ограниченные каналом и реализующие <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="7e1da-120">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="7e1da-121">Если любая служба DI вызывает необработанное исключение во время удаления, платформа регистрирует исключение.</span><span class="sxs-lookup"><span data-stu-id="7e1da-121">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="7e1da-122">Выполняемая вручную логика RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="7e1da-122">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="7e1da-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> предоставляет методы для управления компонентами и элементами, включая создание компонентов вручную в коде C#.</span><span class="sxs-lookup"><span data-stu-id="7e1da-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="7e1da-124">Использование <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов является сложным сценарием.</span><span class="sxs-lookup"><span data-stu-id="7e1da-124">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="7e1da-125">Неправильно сформированный компонент (например, незакрытый тег разметки) может привести к неопределенному поведению.</span><span class="sxs-lookup"><span data-stu-id="7e1da-125">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="7e1da-126">Рассмотрим следующий компонент `PetDetails`, который можно вручную встроить в другой компонент:</span><span class="sxs-lookup"><span data-stu-id="7e1da-126">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="7e1da-127">В следующем примере цикл в методе `CreateComponent` создает три компонента `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-127">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="7e1da-128">При вызове методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов (`OpenComponent` и `AddAttribute`) порядковые номера представляют собой номера строк исходного кода.</span><span class="sxs-lookup"><span data-stu-id="7e1da-128">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="7e1da-129">Алгоритм сравнения Blazor использует порядковые номера, соответствующие отдельным строкам кода, а не отдельным вызовам.</span><span class="sxs-lookup"><span data-stu-id="7e1da-129">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="7e1da-130">При создании компонента с помощью методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> жестко задавайте аргументы для порядковых номеров.</span><span class="sxs-lookup"><span data-stu-id="7e1da-130">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="7e1da-131">**Использование вычисления или счетчика для формирования порядкового номера может привести к снижению производительности.**</span><span class="sxs-lookup"><span data-stu-id="7e1da-131">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="7e1da-132">Дополнительные сведения см. в разделе [Порядковые номера относятся к номерам строк кода, а не порядку выполнения](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="7e1da-132">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="7e1da-133">Компонент `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="7e1da-133">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="7e1da-134">Типы в <xref:Microsoft.AspNetCore.Components.RenderTree> позволяют обрабатывать *результаты* операций отрисовки.</span><span class="sxs-lookup"><span data-stu-id="7e1da-134">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="7e1da-135">Это внутренние сведения реализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="7e1da-135">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="7e1da-136">Эти типы следует считать *нестабильными*. Они могут измениться в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="7e1da-136">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="7e1da-137">Порядковые номера относятся к номерам строк кода, а не порядку выполнения</span><span class="sxs-lookup"><span data-stu-id="7e1da-137">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="7e1da-138">Файлы компонентов Razor (*RAZOR*) всегда компилируются.</span><span class="sxs-lookup"><span data-stu-id="7e1da-138">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="7e1da-139">Компиляция потенциально лучше, чем интерпретация кода, поскольку шаг компиляции можно использовать для вставки сведений, повышающих производительность приложения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="7e1da-139">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="7e1da-140">Ключевой пример этих усовершенствований связан с *порядковыми номерами*.</span><span class="sxs-lookup"><span data-stu-id="7e1da-140">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="7e1da-141">Порядковые номера указывают среде выполнения, какие выходные данные поступили из отдельных и упорядоченных строк кода.</span><span class="sxs-lookup"><span data-stu-id="7e1da-141">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="7e1da-142">Среда выполнения использует эти сведения для эффективного сравнения деревьев в линейном времени, а это гораздо быстрее, чем при использовании общего алгоритма сравнения деревьев.</span><span class="sxs-lookup"><span data-stu-id="7e1da-142">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="7e1da-143">Рассмотрим следующий файл компонента Razor (*RAZOR*):</span><span class="sxs-lookup"><span data-stu-id="7e1da-143">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="7e1da-144">Предыдущий код компилируется примерно следующим образом:</span><span class="sxs-lookup"><span data-stu-id="7e1da-144">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="7e1da-145">Когда код выполняется в первый раз, если `someFlag` имеет значение `true`, построитель получит следующее:</span><span class="sxs-lookup"><span data-stu-id="7e1da-145">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="7e1da-146">Sequence</span><span class="sxs-lookup"><span data-stu-id="7e1da-146">Sequence</span></span> | <span data-ttu-id="7e1da-147">Type</span><span class="sxs-lookup"><span data-stu-id="7e1da-147">Type</span></span>      | <span data-ttu-id="7e1da-148">Данные</span><span class="sxs-lookup"><span data-stu-id="7e1da-148">Data</span></span>   |
| :---
<span data-ttu-id="7e1da-149">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-149">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-151">'Blazor'</span></span>
- <span data-ttu-id="7e1da-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-152">'Identity'</span></span>
- <span data-ttu-id="7e1da-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-154">'Razor'</span></span>
- <span data-ttu-id="7e1da-155">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-155">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-156">---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-156">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-158">'Blazor'</span></span>
- <span data-ttu-id="7e1da-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-159">'Identity'</span></span>
- <span data-ttu-id="7e1da-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-161">'Razor'</span></span>
- <span data-ttu-id="7e1da-162">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7e1da-163">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-163">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-165">'Blazor'</span></span>
- <span data-ttu-id="7e1da-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-166">'Identity'</span></span>
- <span data-ttu-id="7e1da-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-168">'Razor'</span></span>
- <span data-ttu-id="7e1da-169">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-169">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-170">----- | :----: | | 0        | Текстовый узел | Первый  | | 1        | Текстовый узел | Второй |</span><span class="sxs-lookup"><span data-stu-id="7e1da-170">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="7e1da-171">Представьте, что `someFlag` становится `false`, и разметка снова преобразуется для просмотра.</span><span class="sxs-lookup"><span data-stu-id="7e1da-171">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="7e1da-172">На этот раз построитель получает:</span><span class="sxs-lookup"><span data-stu-id="7e1da-172">This time, the builder receives:</span></span>

| <span data-ttu-id="7e1da-173">Sequence</span><span class="sxs-lookup"><span data-stu-id="7e1da-173">Sequence</span></span> | <span data-ttu-id="7e1da-174">Type</span><span class="sxs-lookup"><span data-stu-id="7e1da-174">Type</span></span>       | <span data-ttu-id="7e1da-175">Данные</span><span class="sxs-lookup"><span data-stu-id="7e1da-175">Data</span></span>   |
| :---
<span data-ttu-id="7e1da-176">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-176">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-178">'Blazor'</span></span>
- <span data-ttu-id="7e1da-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-179">'Identity'</span></span>
- <span data-ttu-id="7e1da-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-181">'Razor'</span></span>
- <span data-ttu-id="7e1da-182">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-182">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-183">---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-183">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-185">'Blazor'</span></span>
- <span data-ttu-id="7e1da-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-186">'Identity'</span></span>
- <span data-ttu-id="7e1da-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-188">'Razor'</span></span>
- <span data-ttu-id="7e1da-189">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7e1da-190">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-190">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-192">'Blazor'</span></span>
- <span data-ttu-id="7e1da-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-193">'Identity'</span></span>
- <span data-ttu-id="7e1da-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-195">'Razor'</span></span>
- <span data-ttu-id="7e1da-196">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7e1da-197">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-197">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-199">'Blazor'</span></span>
- <span data-ttu-id="7e1da-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-200">'Identity'</span></span>
- <span data-ttu-id="7e1da-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-202">'Razor'</span></span>
- <span data-ttu-id="7e1da-203">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-203">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-204">----- | :----: | | 1        | Текстовый узел  | Второй |</span><span class="sxs-lookup"><span data-stu-id="7e1da-204">----- | :----: | | 1        | Text node  | Second |</span></span>

<span data-ttu-id="7e1da-205">Когда среда выполнения выполняет сравнение, она видит, что элемент в последовательности `0` был удален, поэтому создает следующий тривиальный *сценарий изменения*:</span><span class="sxs-lookup"><span data-stu-id="7e1da-205">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="7e1da-206">Удаление первого текстового узла.</span><span class="sxs-lookup"><span data-stu-id="7e1da-206">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="7e1da-207">Проблема с созданием порядковых номеров программным способом</span><span class="sxs-lookup"><span data-stu-id="7e1da-207">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="7e1da-208">Представьте себе, что вместо этого вы написали следующую логику отрисовки построителя деревьев:</span><span class="sxs-lookup"><span data-stu-id="7e1da-208">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="7e1da-209">И теперь первыми выходными данными будет:</span><span class="sxs-lookup"><span data-stu-id="7e1da-209">Now, the first output is:</span></span>

| <span data-ttu-id="7e1da-210">Sequence</span><span class="sxs-lookup"><span data-stu-id="7e1da-210">Sequence</span></span> | <span data-ttu-id="7e1da-211">Type</span><span class="sxs-lookup"><span data-stu-id="7e1da-211">Type</span></span>      | <span data-ttu-id="7e1da-212">Данные</span><span class="sxs-lookup"><span data-stu-id="7e1da-212">Data</span></span>   |
| :---
<span data-ttu-id="7e1da-213">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-213">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-215">'Blazor'</span></span>
- <span data-ttu-id="7e1da-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-216">'Identity'</span></span>
- <span data-ttu-id="7e1da-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-218">'Razor'</span></span>
- <span data-ttu-id="7e1da-219">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-219">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-220">---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-220">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-222">'Blazor'</span></span>
- <span data-ttu-id="7e1da-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-223">'Identity'</span></span>
- <span data-ttu-id="7e1da-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-225">'Razor'</span></span>
- <span data-ttu-id="7e1da-226">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7e1da-227">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-227">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-229">'Blazor'</span></span>
- <span data-ttu-id="7e1da-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-230">'Identity'</span></span>
- <span data-ttu-id="7e1da-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-232">'Razor'</span></span>
- <span data-ttu-id="7e1da-233">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-233">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-234">----- | :----: | | 0        | Текстовый узел | Первый  | | 1        | Текстовый узел | Второй |</span><span class="sxs-lookup"><span data-stu-id="7e1da-234">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="7e1da-235">Этот результат идентичен предыдущему случаю, поэтому проблемы не возникают.</span><span class="sxs-lookup"><span data-stu-id="7e1da-235">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="7e1da-236">`someFlag` имеет значение `false` во второй отрисовке, а выходные данные следующие:</span><span class="sxs-lookup"><span data-stu-id="7e1da-236">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="7e1da-237">Sequence</span><span class="sxs-lookup"><span data-stu-id="7e1da-237">Sequence</span></span> | <span data-ttu-id="7e1da-238">Type</span><span class="sxs-lookup"><span data-stu-id="7e1da-238">Type</span></span>      | <span data-ttu-id="7e1da-239">Данные</span><span class="sxs-lookup"><span data-stu-id="7e1da-239">Data</span></span>   |
| :---
<span data-ttu-id="7e1da-240">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-240">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-241">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-241">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-242">'Blazor'</span></span>
- <span data-ttu-id="7e1da-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-243">'Identity'</span></span>
- <span data-ttu-id="7e1da-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-245">'Razor'</span></span>
- <span data-ttu-id="7e1da-246">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-246">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-247">---: | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-247">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-248">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-248">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-249">'Blazor'</span></span>
- <span data-ttu-id="7e1da-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-250">'Identity'</span></span>
- <span data-ttu-id="7e1da-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-252">'Razor'</span></span>
- <span data-ttu-id="7e1da-253">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="7e1da-254">title: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-254">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-255">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-255">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-256">'Blazor'</span></span>
- <span data-ttu-id="7e1da-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-257">'Identity'</span></span>
- <span data-ttu-id="7e1da-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-259">'Razor'</span></span>
- <span data-ttu-id="7e1da-260">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-260">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-261">----- | --- название: 'Сложные сценарии ASP.NET Core Blazor' автор: описание: 'Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.'</span><span class="sxs-lookup"><span data-stu-id="7e1da-261">----- | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="7e1da-262">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="7e1da-262">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7e1da-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-263">'Blazor'</span></span>
- <span data-ttu-id="7e1da-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7e1da-264">'Identity'</span></span>
- <span data-ttu-id="7e1da-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7e1da-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="7e1da-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7e1da-266">'Razor'</span></span>
- <span data-ttu-id="7e1da-267">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="7e1da-267">'SignalR' uid:</span></span> 

<span data-ttu-id="7e1da-268">--- | | 0        | Текстовый узел | Второй |</span><span class="sxs-lookup"><span data-stu-id="7e1da-268">--- | | 0        | Text node | Second |</span></span>

<span data-ttu-id="7e1da-269">На этот раз алгоритм сравнения видит, что произошло *два* изменения, и создает следующий скрипт редактирования:</span><span class="sxs-lookup"><span data-stu-id="7e1da-269">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="7e1da-270">Изменение значения первого текстового узла на `Second`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-270">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="7e1da-271">Удаление второго текстового узла.</span><span class="sxs-lookup"><span data-stu-id="7e1da-271">Remove the second text node.</span></span>

<span data-ttu-id="7e1da-272">При формировании порядковых номеров теряются все полезные сведения о том, где в исходном коде находятся циклы и ветви `if/else`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-272">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="7e1da-273">Это приводит к тому, что сравнение становится **в два раза больше**.</span><span class="sxs-lookup"><span data-stu-id="7e1da-273">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="7e1da-274">Это упрощенный пример.</span><span class="sxs-lookup"><span data-stu-id="7e1da-274">This is a trivial example.</span></span> <span data-ttu-id="7e1da-275">В более реалистичных случаях со сложными и глубокими вложенными структурами, особенно с циклами, затраты на производительность обычно выше.</span><span class="sxs-lookup"><span data-stu-id="7e1da-275">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="7e1da-276">Вместо того чтобы сразу определять, какие блоки или ветви цикла были вставлены или удалены, алгоритм сравнения должен выполнять рекурсивный глубокий переход к деревьям отрисовки.</span><span class="sxs-lookup"><span data-stu-id="7e1da-276">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="7e1da-277">В результате, как правило, приходится создавать более длинные скрипты изменений, поскольку алгоритм сравнения не знает всей информации о том, как старые и новые структуры связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="7e1da-277">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="7e1da-278">Рекомендации и выводы</span><span class="sxs-lookup"><span data-stu-id="7e1da-278">Guidance and conclusions</span></span>

* <span data-ttu-id="7e1da-279">Производительность приложения снижается, если порядковые номера создаются динамически.</span><span class="sxs-lookup"><span data-stu-id="7e1da-279">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="7e1da-280">Платформа не может автоматически создавать собственные порядковые номера во время выполнения, поскольку необходимая информация не существует, если только она не получена во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="7e1da-280">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="7e1da-281">Не записывайте длинные блоки логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, реализуемой вручную.</span><span class="sxs-lookup"><span data-stu-id="7e1da-281">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="7e1da-282">Используйте файлы *RAZOR* и дайте компилятору обрабатывать порядковые номера.</span><span class="sxs-lookup"><span data-stu-id="7e1da-282">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="7e1da-283">Если не удается избежать реализуемой вручную логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, разделите длинные блоки кода на более мелкие части, заключенные в вызовы <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>.</span><span class="sxs-lookup"><span data-stu-id="7e1da-283">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="7e1da-284">Каждый регион имеет собственное отдельное пространство порядковых номеров, поэтому вы можете снова начинать с нуля (или любого другого произвольного числа) внутри каждого региона.</span><span class="sxs-lookup"><span data-stu-id="7e1da-284">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="7e1da-285">Если порядковые номера задаются жестко, то для алгоритма сравнения требуется только, чтобы порядковые номера увеличивались.</span><span class="sxs-lookup"><span data-stu-id="7e1da-285">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="7e1da-286">Начальное значение и пропуски несущественны.</span><span class="sxs-lookup"><span data-stu-id="7e1da-286">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="7e1da-287">Кроме того, можно использовать номер строки кода в качестве порядкового номера или начать с нуля и увеличивать значение на единицы или сотни (или выбрать любой другой интервал).</span><span class="sxs-lookup"><span data-stu-id="7e1da-287">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="7e1da-288"> использует порядковые номера, а другие платформы пользовательского интерфейса для сравнения деревьев не используют их.</span><span class="sxs-lookup"><span data-stu-id="7e1da-288"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="7e1da-289">Сравнение выполняется гораздо быстрее, если используются порядковые номера, и Blazor имеет преимущество этапа компиляции, который автоматически обрабатывает порядковые номера для разработчиков, создающих файлы *RAZOR*.</span><span class="sxs-lookup"><span data-stu-id="7e1da-289">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="7e1da-290">Выполнение передачи больших объемов данных в приложениях Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7e1da-290">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="7e1da-291">В некоторых сценариях необходимо передавать большие объемы данных между JavaScript и Blazor.</span><span class="sxs-lookup"><span data-stu-id="7e1da-291">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="7e1da-292">Как правило, передача больших данных происходит в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="7e1da-292">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="7e1da-293">API-интерфейсы файловой системы браузера используются для отправки или загрузки файла.</span><span class="sxs-lookup"><span data-stu-id="7e1da-293">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="7e1da-294">Требуется взаимодействие со сторонней библиотекой.</span><span class="sxs-lookup"><span data-stu-id="7e1da-294">Interop with a third party library is required.</span></span>

<span data-ttu-id="7e1da-295">В Blazor Server есть ограничение, предотвращающее передачу единичных крупных сообщений, которые могут привести к проблемам с производительностью.</span><span class="sxs-lookup"><span data-stu-id="7e1da-295">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="7e1da-296">При разработке кода, который передает данные между JavaScript и Blazor, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="7e1da-296">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="7e1da-297">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="7e1da-297">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="7e1da-298">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7e1da-298">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="7e1da-299">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="7e1da-299">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="7e1da-300">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="7e1da-300">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="7e1da-301">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="7e1da-301">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="7e1da-302">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="7e1da-302">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="7e1da-303">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="7e1da-303">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="7e1da-304">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="7e1da-304">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="7e1da-305">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="7e1da-305">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="7e1da-306">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="7e1da-306">Consumed immediately.</span></span> <span data-ttu-id="7e1da-307">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="7e1da-307">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="7e1da-308">Следующий класс отправителя файлов обрабатывает взаимодействие JS с клиентом.</span><span class="sxs-lookup"><span data-stu-id="7e1da-308">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="7e1da-309">Класс отправителя использует взаимодействие JS, чтобы:</span><span class="sxs-lookup"><span data-stu-id="7e1da-309">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="7e1da-310">Опросить клиент для отправки сегмента данных.</span><span class="sxs-lookup"><span data-stu-id="7e1da-310">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="7e1da-311">Прервать транзакцию при истечении времени опроса.</span><span class="sxs-lookup"><span data-stu-id="7e1da-311">Abort the transaction if polling times out.</span></span>

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

<span data-ttu-id="7e1da-312">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="7e1da-312">In the preceding example:</span></span>

* <span data-ttu-id="7e1da-313">Для `maxBase64SegmentSize` задано значение `8192`, которое вычисляется на основе `maxBase64SegmentSize = segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-313">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="7e1da-314">Низкоуровневые API управления памятью .NET Core используются для хранения сегментов памяти на сервере в `uploadedSegments`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-314">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="7e1da-315">Для отправки через взаимодействие с JS используется метод `ReceiveFile`:</span><span class="sxs-lookup"><span data-stu-id="7e1da-315">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="7e1da-316">Размер файла определяется в байтах с помощью взаимодействия JS с `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-316">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="7e1da-317">Количество принимаемых сегментов вычисляется и сохраняется в `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-317">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="7e1da-318">Сегменты запрашиваются в цикле `for` через взаимодействие JS с `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="7e1da-318">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="7e1da-319">Все сегменты, кроме последнего, должны иметь размер 8192 байт перед декодированием.</span><span class="sxs-lookup"><span data-stu-id="7e1da-319">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="7e1da-320">Клиент должен отправлять данные эффективно.</span><span class="sxs-lookup"><span data-stu-id="7e1da-320">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="7e1da-321">Для каждого полученного сегмента проверки выполняются перед декодированием с помощью <xref:System.Convert.TryFromBase64String%2A>.</span><span class="sxs-lookup"><span data-stu-id="7e1da-321">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="7e1da-322">Поток с данными возвращается в виде нового <xref:System.IO.Stream> (`SegmentedStream`) после завершения передачи.</span><span class="sxs-lookup"><span data-stu-id="7e1da-322">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="7e1da-323">Класс сегментированного потока предоставляет список сегментов как недоступный для поиска <xref:System.IO.Stream> только для чтения:</span><span class="sxs-lookup"><span data-stu-id="7e1da-323">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

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

<span data-ttu-id="7e1da-324">Следующий код реализует функции JavaScript для получения данных:</span><span class="sxs-lookup"><span data-stu-id="7e1da-324">The following code implements JavaScript functions to receive the data:</span></span>

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
