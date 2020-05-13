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
# <a name="request-and-response-operations-in-aspnet-core"></a>Операции запросов и ответов в ASP.NET Core

Автор [Джастин Коталик (Justin Kotalik)](https://github.com/jkotalik)

В этой статье объясняется, как читать текст запроса и писать текст ответа. Возможно, вам потребуется написать код для этих операций при создании ПО промежуточного слоя. В других случаях писать такой код обычно не нужно, так как эти операции обрабатываются MVC и Razor Pages.

Существует две абстракции для текста запросов и ответов: <xref:System.IO.Stream> и <xref:System.IO.Pipelines.Pipe>. При чтении запроса [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) — это <xref:System.IO.Stream>, а `HttpRequest.BodyReader` — это <xref:System.IO.Pipelines.PipeReader>. При записи ответа [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) — это <xref:System.IO.Stream>, а `HttpResponse.BodyWriter` — это <xref:System.IO.Pipelines.PipeWriter>.

Рекомендуется использовать [конвейеры](/dotnet/standard/io/pipelines), а не потоки. Потоки удобнее использовать для некоторых простых операций, но производительность конвейеров выше и с ними проще работать в большинстве сценариев. Начиная с ASP.NET Core преимущество отдается внутреннему использованию конвейеров вместо потоков. Примеры:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Потоки не удаляются из платформы. С ними продолжают работать в .NET, и многие типы потоков не имеют эквивалентного конвейера, например `FileStreams` и `ResponseCompression`.

## <a name="stream-examples"></a>Примеры потоков

Предположим, необходимо создать ПО промежуточного слоя, которое считывает весь текст запроса как список строк с разделением на новые строки. Реализация простого потока может выглядеть следующим образом:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Этот код работает, но есть определенные проблемы:

* Перед добавлением `StringBuilder` в коде создается другая строка (`encodedString`), которая сразу отбрасывается. Этот процесс выполняется для всех байтов в потоке, и в результате выделяется дополнительный объем памяти для всего текста запроса.
* Код считывает всю строку перед разделением на новые строки. Более эффективным вариантом является поиск новых строк в массиве байтов.

Ниже приведен пример, в котором устранены некоторые предыдущие проблемы.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

В предшествующем примере:

* Не создается буфер для всего текста запроса в `StringBuilder`, если нет символов новой строки.
* В строке не вызывается `Split`.

Но некоторые проблемы при этом остаются:

* Если символы новой строки разрежены, большая часть текста запроса буферизуется в строке.
* Строки (`remainingString`) по-прежнему создаются в коде и добавляются в буфер строки, что приводит к выделению дополнительной памяти.

Эти проблемы можно решить, но ценой усложнения кода при незначительных его улучшениях. Конвейеры позволяют решить эти проблемы при минимальной сложности кода.

## <a name="pipelines"></a>Конвейеры

В следующем примере показано, как тот же сценарий можно обработать с помощью `PipeReader`.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

В этом примере устранены многие проблемы, характерные для реализации посредством потоков.

* В буфере строки теперь нет необходимости, так как `PipeReader` обрабатывает байты, которые не использовались.
* Кодированные строки напрямую добавляются в список возвращенных строк.
* Создание строк не связано с выделением памяти, кроме памяти, используемой строкой (за исключением вызова `ToArray()`).

## <a name="adapters"></a>Адаптеры

Свойства `Body` и `BodyReader/BodyWriter` доступны для `HttpRequest` и `HttpResponse`. Если назначить `Body` другому потоку, новый набор адаптеров автоматически адаптирует каждый тип к другому. Если назначить `HttpRequest.Body` новому потоку, `HttpRequest.BodyReader` автоматически назначается новому `PipeReader`, который создает оболочку для `HttpRequest.Body`.

## <a name="startasync"></a>StartAsync

Метод `HttpResponse.StartAsync` используется для указания того, что заголовки являются неизменяемыми, а также для запуска обратных вызовов `OnStarting`. При использования Kestrel в качестве сервера вызов `StartAsync` перед применением `PipeReader` гарантирует, что память, возвращенная `GetMemory`, относится к внутреннему методу <xref:System.IO.Pipelines.Pipe> Kestrel, а не к внешнему буферу.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Ознакомительная статья о библиотеке System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
