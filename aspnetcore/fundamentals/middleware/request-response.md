---
title: Операции запросов и ответов в ASP.NET Core
author: jkotalik
description: Узнайте, как читать текст запроса и писать текст ответа в ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: f16bc7ec61c10600fe72a763fef96987210fbe76
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776003"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="1f584-103">Операции запросов и ответов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f584-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="1f584-104">Автор [Джастин Коталик (Justin Kotalik)](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="1f584-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="1f584-105">В этой статье объясняется, как читать текст запроса и писать текст ответа.</span><span class="sxs-lookup"><span data-stu-id="1f584-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="1f584-106">Возможно, вам потребуется написать код для этих операций при создании ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="1f584-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="1f584-107">В других случаях писать такой код обычно не нужно, так как эти операции обрабатываются MVC и Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="1f584-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="1f584-108">Существует две абстракции для текста запросов и ответов: <xref:System.IO.Stream> и <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="1f584-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="1f584-109">При чтении запроса [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) — это <xref:System.IO.Stream>, а `HttpRequest.BodyReader` — это <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="1f584-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="1f584-110">При записи ответа [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) — это <xref:System.IO.Stream>, а `HttpResponse.BodyWriter` — это <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="1f584-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="1f584-111">Рекомендуется использовать [конвейеры](/dotnet/standard/io/pipelines), а не потоки.</span><span class="sxs-lookup"><span data-stu-id="1f584-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="1f584-112">Потоки удобнее использовать для некоторых простых операций, но производительность конвейеров выше и с ними проще работать в большинстве сценариев.</span><span class="sxs-lookup"><span data-stu-id="1f584-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="1f584-113">Начиная с ASP.NET Core преимущество отдается внутреннему использованию конвейеров вместо потоков.</span><span class="sxs-lookup"><span data-stu-id="1f584-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="1f584-114">Примеры:</span><span class="sxs-lookup"><span data-stu-id="1f584-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="1f584-115">Потоки не удаляются из платформы.</span><span class="sxs-lookup"><span data-stu-id="1f584-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="1f584-116">С ними продолжают работать в .NET, и многие типы потоков не имеют эквивалентного конвейера, например `FileStreams` и `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="1f584-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="1f584-117">Примеры потоков</span><span class="sxs-lookup"><span data-stu-id="1f584-117">Stream examples</span></span>

<span data-ttu-id="1f584-118">Предположим, необходимо создать ПО промежуточного слоя, которое считывает весь текст запроса как список строк с разделением на новые строки.</span><span class="sxs-lookup"><span data-stu-id="1f584-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="1f584-119">Реализация простого потока может выглядеть следующим образом:</span><span class="sxs-lookup"><span data-stu-id="1f584-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="1f584-120">Этот код работает, но есть определенные проблемы:</span><span class="sxs-lookup"><span data-stu-id="1f584-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="1f584-121">Перед добавлением `StringBuilder` в коде создается другая строка (`encodedString`), которая сразу отбрасывается.</span><span class="sxs-lookup"><span data-stu-id="1f584-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="1f584-122">Этот процесс выполняется для всех байтов в потоке, и в результате выделяется дополнительный объем памяти для всего текста запроса.</span><span class="sxs-lookup"><span data-stu-id="1f584-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="1f584-123">Код считывает всю строку перед разделением на новые строки.</span><span class="sxs-lookup"><span data-stu-id="1f584-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="1f584-124">Более эффективным вариантом является поиск новых строк в массиве байтов.</span><span class="sxs-lookup"><span data-stu-id="1f584-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="1f584-125">Ниже приведен пример, в котором устранены некоторые предыдущие проблемы.</span><span class="sxs-lookup"><span data-stu-id="1f584-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="1f584-126">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="1f584-126">This preceding example:</span></span>

* <span data-ttu-id="1f584-127">Не создается буфер для всего текста запроса в `StringBuilder`, если нет символов новой строки.</span><span class="sxs-lookup"><span data-stu-id="1f584-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="1f584-128">В строке не вызывается `Split`.</span><span class="sxs-lookup"><span data-stu-id="1f584-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="1f584-129">Но некоторые проблемы при этом остаются:</span><span class="sxs-lookup"><span data-stu-id="1f584-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="1f584-130">Если символы новой строки разрежены, большая часть текста запроса буферизуется в строке.</span><span class="sxs-lookup"><span data-stu-id="1f584-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="1f584-131">Строки (`remainingString`) по-прежнему создаются в коде и добавляются в буфер строки, что приводит к выделению дополнительной памяти.</span><span class="sxs-lookup"><span data-stu-id="1f584-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="1f584-132">Эти проблемы можно решить, но ценой усложнения кода при незначительных его улучшениях.</span><span class="sxs-lookup"><span data-stu-id="1f584-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="1f584-133">Конвейеры позволяют решить эти проблемы при минимальной сложности кода.</span><span class="sxs-lookup"><span data-stu-id="1f584-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="1f584-134">Конвейеры</span><span class="sxs-lookup"><span data-stu-id="1f584-134">Pipelines</span></span>

<span data-ttu-id="1f584-135">В следующем примере показано, как тот же сценарий можно обработать с помощью `PipeReader`.</span><span class="sxs-lookup"><span data-stu-id="1f584-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="1f584-136">В этом примере устранены многие проблемы, характерные для реализации посредством потоков.</span><span class="sxs-lookup"><span data-stu-id="1f584-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="1f584-137">В буфере строки теперь нет необходимости, так как `PipeReader` обрабатывает байты, которые не использовались.</span><span class="sxs-lookup"><span data-stu-id="1f584-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="1f584-138">Кодированные строки напрямую добавляются в список возвращенных строк.</span><span class="sxs-lookup"><span data-stu-id="1f584-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="1f584-139">Создание строк не связано с выделением памяти, кроме памяти, используемой строкой (за исключением вызова `ToArray()`).</span><span class="sxs-lookup"><span data-stu-id="1f584-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="1f584-140">Адаптеры</span><span class="sxs-lookup"><span data-stu-id="1f584-140">Adapters</span></span>

<span data-ttu-id="1f584-141">Свойства `Body` и `BodyReader/BodyWriter` доступны для `HttpRequest` и `HttpResponse`.</span><span class="sxs-lookup"><span data-stu-id="1f584-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="1f584-142">Если назначить `Body` другому потоку, новый набор адаптеров автоматически адаптирует каждый тип к другому.</span><span class="sxs-lookup"><span data-stu-id="1f584-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="1f584-143">Если назначить `HttpRequest.Body` новому потоку, `HttpRequest.BodyReader` автоматически назначается новому `PipeReader`, который создает оболочку для `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="1f584-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="1f584-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="1f584-144">StartAsync</span></span>

<span data-ttu-id="1f584-145">Метод `HttpResponse.StartAsync` используется для указания того, что заголовки являются неизменяемыми, а также для запуска обратных вызовов `OnStarting`.</span><span class="sxs-lookup"><span data-stu-id="1f584-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="1f584-146">При использования Kestrel в качестве сервера вызов `StartAsync` перед применением `PipeReader` гарантирует, что память, возвращенная `GetMemory`, относится к внутреннему методу <xref:System.IO.Pipelines.Pipe> Kestrel, а не к внешнему буферу.</span><span class="sxs-lookup"><span data-stu-id="1f584-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f584-147">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f584-147">Additional resources</span></span>

* [<span data-ttu-id="1f584-148">Ознакомительная статья о библиотеке System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="1f584-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
