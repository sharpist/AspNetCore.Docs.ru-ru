---
title: Сложные сценарии ASP.NET Core Blazor
author: guardrex
description: Дополнительные сведения о сложных сценариях в Blazor, в том числе о том, как включить выполняемую вручную логику RenderTreeBuilder в приложение.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 4bd73acd821a8791d7f6cc93545edc2e39a6f2c7
ms.sourcegitcommit: 68d03d1aee8906b53bda66f8f1e0747efc3007e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88051788"
---
# <a name="aspnet-core-no-locblazor-advanced-scenarios"></a><span data-ttu-id="c49dc-103">Сложные сценарии ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c49dc-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="c49dc-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="c49dc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="no-locblazor-server-circuit-handler"></a><span data-ttu-id="c49dc-105">Обработчик цепи Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c49dc-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="c49dc-106">Blazor Server позволяет коду определить *обработчик канала*, чтобы выполнять код при изменении состояния канала пользователя.</span><span class="sxs-lookup"><span data-stu-id="c49dc-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="c49dc-107">Обработчик канала реализуется путем наследования от `CircuitHandler` и регистрации класса в контейнере службы приложения.</span><span class="sxs-lookup"><span data-stu-id="c49dc-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="c49dc-108">В следующем примере обработчик канала отслеживает открытые соединения SignalR:</span><span class="sxs-lookup"><span data-stu-id="c49dc-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="c49dc-109">Обработчики канала регистрируются с помощью DI.</span><span class="sxs-lookup"><span data-stu-id="c49dc-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="c49dc-110">Экземпляры с заданной областью создаются для каждого экземпляра канала.</span><span class="sxs-lookup"><span data-stu-id="c49dc-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="c49dc-111">С помощью `TrackingCircuitHandler` в предыдущем примере создается отдельная служба, поскольку необходимо отслеживать состояние всех каналов:</span><span class="sxs-lookup"><span data-stu-id="c49dc-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="c49dc-112">Если методы пользовательского обработчика канала создают необработанное исключение, это исключение является неустранимым для канала Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="c49dc-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="c49dc-113">Чтобы допускать исключения в коде обработчика или вызываемых методах, заключите код в один или несколько операторов [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) с обработкой ошибок и ведением журнала.</span><span class="sxs-lookup"><span data-stu-id="c49dc-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="c49dc-114">Когда канал завершается из-за того, что пользователь отключился и платформа очищает состояние канала, платформа удаляет область DI канала.</span><span class="sxs-lookup"><span data-stu-id="c49dc-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="c49dc-115">При удалении области удаляются все службы DI, ограниченные каналом и реализующие <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c49dc-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="c49dc-116">Если любая служба DI вызывает необработанное исключение во время удаления, платформа регистрирует исключение.</span><span class="sxs-lookup"><span data-stu-id="c49dc-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c49dc-117">Выполняемая вручную логика RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="c49dc-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c49dc-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> предоставляет методы для управления компонентами и элементами, включая создание компонентов вручную в коде C#.</span><span class="sxs-lookup"><span data-stu-id="c49dc-118"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c49dc-119">Использование <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> для создания компонентов является сложным сценарием.</span><span class="sxs-lookup"><span data-stu-id="c49dc-119">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="c49dc-120">Неправильно сформированный компонент (например, незакрытый тег разметки) может привести к неопределенному поведению.</span><span class="sxs-lookup"><span data-stu-id="c49dc-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c49dc-121">Рассмотрим следующий компонент `PetDetails`, который можно вручную встроить в другой компонент:</span><span class="sxs-lookup"><span data-stu-id="c49dc-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c49dc-122">В следующем примере цикл в методе `CreateComponent` создает три компонента `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c49dc-123">В методах <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> с порядковым номером порядковые номера представляют собой номера строк исходного кода.</span><span class="sxs-lookup"><span data-stu-id="c49dc-123">In <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods with a sequence number, sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c49dc-124">Алгоритм сравнения Blazor использует порядковые номера, соответствующие отдельным строкам кода, а не отдельным вызовам.</span><span class="sxs-lookup"><span data-stu-id="c49dc-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c49dc-125">При создании компонента с помощью методов <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> жестко задавайте аргументы для порядковых номеров.</span><span class="sxs-lookup"><span data-stu-id="c49dc-125">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c49dc-126">**Использование вычисления или счетчика для формирования порядкового номера может привести к снижению производительности.**</span><span class="sxs-lookup"><span data-stu-id="c49dc-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c49dc-127">Дополнительные сведения см. в разделе [Порядковые номера относятся к номерам строк кода, а не порядку выполнения](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order).</span><span class="sxs-lookup"><span data-stu-id="c49dc-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c49dc-128">Компонент `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="c49dc-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="c49dc-129">Типы в <xref:Microsoft.AspNetCore.Components.RenderTree> позволяют обрабатывать *результаты* операций отрисовки.</span><span class="sxs-lookup"><span data-stu-id="c49dc-129">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="c49dc-130">Это внутренние сведения реализации платформы Blazor.</span><span class="sxs-lookup"><span data-stu-id="c49dc-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="c49dc-131">Эти типы следует считать *нестабильными*. Они могут измениться в будущих выпусках.</span><span class="sxs-lookup"><span data-stu-id="c49dc-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c49dc-132">Порядковые номера относятся к номерам строк кода, а не порядку выполнения</span><span class="sxs-lookup"><span data-stu-id="c49dc-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c49dc-133">Файлы компонентов Razor (`.razor`) всегда компилируются.</span><span class="sxs-lookup"><span data-stu-id="c49dc-133">Razor component files (`.razor`) are always compiled.</span></span> <span data-ttu-id="c49dc-134">Компиляция потенциально лучше, чем интерпретация кода, поскольку шаг компиляции можно использовать для вставки сведений, повышающих производительность приложения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c49dc-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="c49dc-135">Ключевой пример этих усовершенствований связан с *порядковыми номерами*.</span><span class="sxs-lookup"><span data-stu-id="c49dc-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="c49dc-136">Порядковые номера указывают среде выполнения, какие выходные данные поступили из отдельных и упорядоченных строк кода.</span><span class="sxs-lookup"><span data-stu-id="c49dc-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c49dc-137">Среда выполнения использует эти сведения для эффективного сравнения деревьев в линейном времени, а это гораздо быстрее, чем при использовании общего алгоритма сравнения деревьев.</span><span class="sxs-lookup"><span data-stu-id="c49dc-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c49dc-138">Рассмотрим следующий файл компонента Razor (`.razor`):</span><span class="sxs-lookup"><span data-stu-id="c49dc-138">Consider the following Razor component (`.razor`) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c49dc-139">Предыдущий код компилируется примерно следующим образом:</span><span class="sxs-lookup"><span data-stu-id="c49dc-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c49dc-140">Когда код выполняется в первый раз, если `someFlag` имеет значение `true`, построитель получит следующее:</span><span class="sxs-lookup"><span data-stu-id="c49dc-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c49dc-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="c49dc-141">Sequence</span></span> | <span data-ttu-id="c49dc-142">Type</span><span class="sxs-lookup"><span data-stu-id="c49dc-142">Type</span></span>      | <span data-ttu-id="c49dc-143">Данные</span><span class="sxs-lookup"><span data-stu-id="c49dc-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c49dc-144">0</span><span class="sxs-lookup"><span data-stu-id="c49dc-144">0</span></span>        | <span data-ttu-id="c49dc-145">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-145">Text node</span></span> | <span data-ttu-id="c49dc-146">First</span><span class="sxs-lookup"><span data-stu-id="c49dc-146">First</span></span>  |
| <span data-ttu-id="c49dc-147">1</span><span class="sxs-lookup"><span data-stu-id="c49dc-147">1</span></span>        | <span data-ttu-id="c49dc-148">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-148">Text node</span></span> | <span data-ttu-id="c49dc-149">Second</span><span class="sxs-lookup"><span data-stu-id="c49dc-149">Second</span></span> |

<span data-ttu-id="c49dc-150">Представьте, что `someFlag` становится `false`, и разметка снова преобразуется для просмотра.</span><span class="sxs-lookup"><span data-stu-id="c49dc-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c49dc-151">На этот раз построитель получает:</span><span class="sxs-lookup"><span data-stu-id="c49dc-151">This time, the builder receives:</span></span>

| <span data-ttu-id="c49dc-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="c49dc-152">Sequence</span></span> | <span data-ttu-id="c49dc-153">Type</span><span class="sxs-lookup"><span data-stu-id="c49dc-153">Type</span></span>       | <span data-ttu-id="c49dc-154">Данные</span><span class="sxs-lookup"><span data-stu-id="c49dc-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c49dc-155">1</span><span class="sxs-lookup"><span data-stu-id="c49dc-155">1</span></span>        | <span data-ttu-id="c49dc-156">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-156">Text node</span></span>  | <span data-ttu-id="c49dc-157">Second</span><span class="sxs-lookup"><span data-stu-id="c49dc-157">Second</span></span> |

<span data-ttu-id="c49dc-158">Когда среда выполнения выполняет сравнение, она видит, что элемент в последовательности `0` был удален, поэтому создает следующий тривиальный *сценарий изменения*:</span><span class="sxs-lookup"><span data-stu-id="c49dc-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c49dc-159">Удаление первого текстового узла.</span><span class="sxs-lookup"><span data-stu-id="c49dc-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="c49dc-160">Проблема с созданием порядковых номеров программным способом</span><span class="sxs-lookup"><span data-stu-id="c49dc-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="c49dc-161">Представьте себе, что вместо этого вы написали следующую логику отрисовки построителя деревьев:</span><span class="sxs-lookup"><span data-stu-id="c49dc-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c49dc-162">И теперь первыми выходными данными будет:</span><span class="sxs-lookup"><span data-stu-id="c49dc-162">Now, the first output is:</span></span>

| <span data-ttu-id="c49dc-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="c49dc-163">Sequence</span></span> | <span data-ttu-id="c49dc-164">Type</span><span class="sxs-lookup"><span data-stu-id="c49dc-164">Type</span></span>      | <span data-ttu-id="c49dc-165">Данные</span><span class="sxs-lookup"><span data-stu-id="c49dc-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c49dc-166">0</span><span class="sxs-lookup"><span data-stu-id="c49dc-166">0</span></span>        | <span data-ttu-id="c49dc-167">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-167">Text node</span></span> | <span data-ttu-id="c49dc-168">First</span><span class="sxs-lookup"><span data-stu-id="c49dc-168">First</span></span>  |
| <span data-ttu-id="c49dc-169">1</span><span class="sxs-lookup"><span data-stu-id="c49dc-169">1</span></span>        | <span data-ttu-id="c49dc-170">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-170">Text node</span></span> | <span data-ttu-id="c49dc-171">Second</span><span class="sxs-lookup"><span data-stu-id="c49dc-171">Second</span></span> |

<span data-ttu-id="c49dc-172">Этот результат идентичен предыдущему случаю, поэтому проблемы не возникают.</span><span class="sxs-lookup"><span data-stu-id="c49dc-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c49dc-173">`someFlag` имеет значение `false` во второй отрисовке, а выходные данные следующие:</span><span class="sxs-lookup"><span data-stu-id="c49dc-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c49dc-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="c49dc-174">Sequence</span></span> | <span data-ttu-id="c49dc-175">Type</span><span class="sxs-lookup"><span data-stu-id="c49dc-175">Type</span></span>      | <span data-ttu-id="c49dc-176">Данные</span><span class="sxs-lookup"><span data-stu-id="c49dc-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c49dc-177">0</span><span class="sxs-lookup"><span data-stu-id="c49dc-177">0</span></span>        | <span data-ttu-id="c49dc-178">Текстовый узел</span><span class="sxs-lookup"><span data-stu-id="c49dc-178">Text node</span></span> | <span data-ttu-id="c49dc-179">Second</span><span class="sxs-lookup"><span data-stu-id="c49dc-179">Second</span></span> |

<span data-ttu-id="c49dc-180">На этот раз алгоритм сравнения видит, что произошло *два* изменения, и создает следующий скрипт редактирования:</span><span class="sxs-lookup"><span data-stu-id="c49dc-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c49dc-181">Изменение значения первого текстового узла на `Second`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c49dc-182">Удаление второго текстового узла.</span><span class="sxs-lookup"><span data-stu-id="c49dc-182">Remove the second text node.</span></span>

<span data-ttu-id="c49dc-183">При формировании порядковых номеров теряются все полезные сведения о том, где в исходном коде находятся циклы и ветви `if/else`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c49dc-184">Это приводит к тому, что сравнение становится **в два раза больше**.</span><span class="sxs-lookup"><span data-stu-id="c49dc-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c49dc-185">Это упрощенный пример.</span><span class="sxs-lookup"><span data-stu-id="c49dc-185">This is a trivial example.</span></span> <span data-ttu-id="c49dc-186">В более реалистичных случаях со сложными и глубокими вложенными структурами, особенно с циклами, затраты на производительность обычно выше.</span><span class="sxs-lookup"><span data-stu-id="c49dc-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="c49dc-187">Вместо того чтобы сразу определять, какие блоки или ветви цикла были вставлены или удалены, алгоритм сравнения должен выполнять рекурсивный глубокий переход к деревьям отрисовки.</span><span class="sxs-lookup"><span data-stu-id="c49dc-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="c49dc-188">В результате, как правило, приходится создавать более длинные скрипты изменений, поскольку алгоритм сравнения не знает всей информации о том, как старые и новые структуры связаны друг с другом.</span><span class="sxs-lookup"><span data-stu-id="c49dc-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="c49dc-189">Рекомендации и выводы</span><span class="sxs-lookup"><span data-stu-id="c49dc-189">Guidance and conclusions</span></span>

* <span data-ttu-id="c49dc-190">Производительность приложения снижается, если порядковые номера создаются динамически.</span><span class="sxs-lookup"><span data-stu-id="c49dc-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c49dc-191">Платформа не может автоматически создавать собственные порядковые номера во время выполнения, поскольку необходимая информация не существует, если только она не получена во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="c49dc-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c49dc-192">Не записывайте длинные блоки логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, реализуемой вручную.</span><span class="sxs-lookup"><span data-stu-id="c49dc-192">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="c49dc-193">Используйте файлы `.razor` и дайте компилятору обрабатывать порядковые номера.</span><span class="sxs-lookup"><span data-stu-id="c49dc-193">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="c49dc-194">Если не удается избежать реализуемой вручную логики <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>, разделите длинные блоки кода на более мелкие части, заключенные в вызовы <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A>.</span><span class="sxs-lookup"><span data-stu-id="c49dc-194">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="c49dc-195">Каждый регион имеет собственное отдельное пространство порядковых номеров, поэтому вы можете снова начинать с нуля (или любого другого произвольного числа) внутри каждого региона.</span><span class="sxs-lookup"><span data-stu-id="c49dc-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="c49dc-196">Если порядковые номера задаются жестко, то для алгоритма сравнения требуется только, чтобы порядковые номера увеличивались.</span><span class="sxs-lookup"><span data-stu-id="c49dc-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c49dc-197">Начальное значение и пропуски несущественны.</span><span class="sxs-lookup"><span data-stu-id="c49dc-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c49dc-198">Кроме того, можно использовать номер строки кода в качестве порядкового номера или начать с нуля и увеличивать значение на единицы или сотни (или выбрать любой другой интервал).</span><span class="sxs-lookup"><span data-stu-id="c49dc-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="c49dc-199">Blazor использует порядковые номера, а другие платформы пользовательского интерфейса для сравнения деревьев не используют их.</span><span class="sxs-lookup"><span data-stu-id="c49dc-199">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c49dc-200">Сравнение выполняется гораздо быстрее, если используются порядковые номера, и Blazor имеет преимущество этапа компиляции, который автоматически обрабатывает порядковые номера для разработчиков, создающих файлы `.razor`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="perform-large-data-transfers-in-no-locblazor-server-apps"></a><span data-ttu-id="c49dc-201">Выполнение передачи больших объемов данных в приложениях Blazor Server</span><span class="sxs-lookup"><span data-stu-id="c49dc-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="c49dc-202">В некоторых сценариях необходимо передавать большие объемы данных между JavaScript и Blazor.</span><span class="sxs-lookup"><span data-stu-id="c49dc-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="c49dc-203">Как правило, передача больших данных происходит в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="c49dc-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="c49dc-204">API-интерфейсы файловой системы браузера используются для отправки или загрузки файла.</span><span class="sxs-lookup"><span data-stu-id="c49dc-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="c49dc-205">Требуется взаимодействие со сторонней библиотекой.</span><span class="sxs-lookup"><span data-stu-id="c49dc-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="c49dc-206">В Blazor Server есть ограничение, предотвращающее передачу единичных крупных сообщений, которые могут привести к проблемам с производительностью.</span><span class="sxs-lookup"><span data-stu-id="c49dc-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="c49dc-207">При разработке кода, который передает данные между JavaScript и Blazor, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="c49dc-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="c49dc-208">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="c49dc-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="c49dc-209">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c49dc-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="c49dc-210">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="c49dc-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="c49dc-211">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="c49dc-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="c49dc-212">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="c49dc-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="c49dc-213">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="c49dc-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="c49dc-214">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="c49dc-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="c49dc-215">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="c49dc-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="c49dc-216">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="c49dc-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="c49dc-217">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="c49dc-217">Consumed immediately.</span></span> <span data-ttu-id="c49dc-218">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="c49dc-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="c49dc-219">Следующий класс отправителя файлов обрабатывает взаимодействие JS с клиентом.</span><span class="sxs-lookup"><span data-stu-id="c49dc-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="c49dc-220">Класс отправителя использует взаимодействие JS, чтобы:</span><span class="sxs-lookup"><span data-stu-id="c49dc-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="c49dc-221">Опросить клиент для отправки сегмента данных.</span><span class="sxs-lookup"><span data-stu-id="c49dc-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="c49dc-222">Прервать транзакцию при истечении времени опроса.</span><span class="sxs-lookup"><span data-stu-id="c49dc-222">Abort the transaction if polling times out.</span></span>

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

<span data-ttu-id="c49dc-223">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="c49dc-223">In the preceding example:</span></span>

* <span data-ttu-id="c49dc-224">Для `maxBase64SegmentSize` задано значение `8192`, которое вычисляется на основе `maxBase64SegmentSize = segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-224">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="c49dc-225">Низкоуровневые API управления памятью .NET Core используются для хранения сегментов памяти на сервере в `uploadedSegments`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="c49dc-226">Для отправки через взаимодействие с JS используется метод `ReceiveFile`:</span><span class="sxs-lookup"><span data-stu-id="c49dc-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="c49dc-227">Размер файла определяется в байтах с помощью взаимодействия JS с `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-227">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="c49dc-228">Количество принимаемых сегментов вычисляется и сохраняется в `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="c49dc-229">Сегменты запрашиваются в цикле `for` через взаимодействие JS с `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="c49dc-229">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="c49dc-230">Все сегменты, кроме последнего, должны иметь размер 8192 байт перед декодированием.</span><span class="sxs-lookup"><span data-stu-id="c49dc-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="c49dc-231">Клиент должен отправлять данные эффективно.</span><span class="sxs-lookup"><span data-stu-id="c49dc-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="c49dc-232">Для каждого полученного сегмента проверки выполняются перед декодированием с помощью <xref:System.Convert.TryFromBase64String%2A>.</span><span class="sxs-lookup"><span data-stu-id="c49dc-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="c49dc-233">Поток с данными возвращается в виде нового <xref:System.IO.Stream> (`SegmentedStream`) после завершения передачи.</span><span class="sxs-lookup"><span data-stu-id="c49dc-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="c49dc-234">Класс сегментированного потока предоставляет список сегментов как недоступный для поиска <xref:System.IO.Stream> только для чтения:</span><span class="sxs-lookup"><span data-stu-id="c49dc-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

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

<span data-ttu-id="c49dc-235">Следующий код реализует функции JavaScript для получения данных:</span><span class="sxs-lookup"><span data-stu-id="c49dc-235">The following code implements JavaScript functions to receive the data:</span></span>

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
