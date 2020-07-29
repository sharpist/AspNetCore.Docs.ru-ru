---
title: Тестирование ПО промежуточного слоя ASP.NET Core
author: tratcher
description: Узнайте, как протестировать ПО промежуточного слоя ASP.NET Core с помощью TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: test/middleware
ms.openlocfilehash: f4ed16b136da37c093a72a8866301a188a8518a2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406489"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="64cef-103">Тестирование ПО промежуточного слоя ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64cef-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="64cef-104">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="64cef-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="64cef-105">ПО промежуточного слоя можно протестировать изолированно с <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="64cef-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="64cef-106">Оно предоставляет следующие возможности.</span><span class="sxs-lookup"><span data-stu-id="64cef-106">It allows you to:</span></span>

* <span data-ttu-id="64cef-107">Создание экземпляров конвейера приложения, содержащего только те компоненты, которые необходимо протестировать.</span><span class="sxs-lookup"><span data-stu-id="64cef-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="64cef-108">Отправка пользовательских запросов для проверки поведения ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="64cef-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="64cef-109">Преимущества:</span><span class="sxs-lookup"><span data-stu-id="64cef-109">Advantages:</span></span>

* <span data-ttu-id="64cef-110">Запросы отправляются в памяти, а не сериализуются по сети.</span><span class="sxs-lookup"><span data-stu-id="64cef-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="64cef-111">Это позволяет избежать дополнительных проблем, таких как управление портами и сертификаты HTTPS.</span><span class="sxs-lookup"><span data-stu-id="64cef-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="64cef-112">Исключения в ПО промежуточного слоя могут напрямую возвращаться к вызывающему тесту.</span><span class="sxs-lookup"><span data-stu-id="64cef-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="64cef-113">Можно настроить структуры данных сервера, например <xref:Microsoft.AspNetCore.Http.HttpContext>, непосредственно в тесте.</span><span class="sxs-lookup"><span data-stu-id="64cef-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="64cef-114">Настройка TestServer</span><span class="sxs-lookup"><span data-stu-id="64cef-114">Set up the TestServer</span></span>

<span data-ttu-id="64cef-115">В тестовом проекте создайте тест.</span><span class="sxs-lookup"><span data-stu-id="64cef-115">In the test project, create a test:</span></span>

* <span data-ttu-id="64cef-116">Создайте и запустите узел, который использует <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="64cef-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="64cef-117">Добавьте все необходимые службы, используемые ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="64cef-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="64cef-118">Добавьте в проект пакет NuGet [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/).</span><span class="sxs-lookup"><span data-stu-id="64cef-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="64cef-119">Настройте конвейер обработки для использования ПО промежуточного слоя для теста.</span><span class="sxs-lookup"><span data-stu-id="64cef-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="64cef-120">Отправка запросов с помощью HttpClient</span><span class="sxs-lookup"><span data-stu-id="64cef-120">Send requests with HttpClient</span></span>
<span data-ttu-id="64cef-121">Отправьте запрос с помощью <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="64cef-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="64cef-122">Утвердите результат.</span><span class="sxs-lookup"><span data-stu-id="64cef-122">Assert the result.</span></span> <span data-ttu-id="64cef-123">Сначала следует сделать утверждение, противоположное ожидаемому результату.</span><span class="sxs-lookup"><span data-stu-id="64cef-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="64cef-124">Первое выполнение с ложноположительным утверждением подтверждает, что тест завершается ошибкой, если ПО промежуточного слоя выполняется правильно.</span><span class="sxs-lookup"><span data-stu-id="64cef-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="64cef-125">Запустите тест и убедитесь, что тест не пройден.</span><span class="sxs-lookup"><span data-stu-id="64cef-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="64cef-126">В следующем примере ПО промежуточного слоя должно вернуть код состояния 404 (*Не найдено*) при запросе корневой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="64cef-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="64cef-127">Выполните первый тестовый запуск с `Assert.NotEqual( ... );`, который должен завершиться ошибкой.</span><span class="sxs-lookup"><span data-stu-id="64cef-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="64cef-128">Измените утверждение, чтобы проверить ПО промежуточного слоя в нормальных условиях работы.</span><span class="sxs-lookup"><span data-stu-id="64cef-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="64cef-129">В последнем тесте используется `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="64cef-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="64cef-130">Запустите тест еще раз, чтобы убедиться, что он пройден.</span><span class="sxs-lookup"><span data-stu-id="64cef-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="64cef-131">Отправка запросов с помощью HttpContext</span><span class="sxs-lookup"><span data-stu-id="64cef-131">Send requests with HttpContext</span></span>

<span data-ttu-id="64cef-132">Тестовое приложение также может отправить запрос с помощью [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="64cef-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="64cef-133">В следующем примере выполняется несколько проверок, когда `https://example.com/A/Path/?and=query` обрабатывается в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="64cef-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="64cef-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> разрешает прямую настройку объекта <xref:Microsoft.AspNetCore.Http.HttpContext> вместо использования абстракций <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="64cef-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="64cef-135">Используйте <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> для управления структурами, доступными только на сервере, например [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) или [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="64cef-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="64cef-136">Как и в предыдущем примере, где ожидалась ошибка *404 — не найдено*, проверьте противоположность каждой инструкции `Assert` в предыдущем тесте.</span><span class="sxs-lookup"><span data-stu-id="64cef-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="64cef-137">Проверка покажет, что тест действительно завершается ошибкой, если ПО промежуточного слоя работает нормально.</span><span class="sxs-lookup"><span data-stu-id="64cef-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="64cef-138">Убедившись, что тест дает ложноположительный результат, установите окончательные инструкции `Assert` для ожидаемых условий и значений теста.</span><span class="sxs-lookup"><span data-stu-id="64cef-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="64cef-139">Запустите тест еще раз, чтобы убедиться, что он пройден.</span><span class="sxs-lookup"><span data-stu-id="64cef-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="64cef-140">Ограничения TestServer</span><span class="sxs-lookup"><span data-stu-id="64cef-140">TestServer limitations</span></span>

<span data-ttu-id="64cef-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="64cef-141">TestServer:</span></span>

* <span data-ttu-id="64cef-142">используется для репликации поведения сервера для проверки ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="64cef-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="64cef-143">***Не*** пытается выполнить репликацию всего поведения <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="64cef-143">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="64cef-144">Пытается предоставить клиенту максимально возможный контроль над сервером и обеспечить максимальную возможность отслеживания того, что происходит на сервере.</span><span class="sxs-lookup"><span data-stu-id="64cef-144">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="64cef-145">Например, он может вызывать исключения, которые обычно не вызываются `HttpClient`, чтобы напрямую передавать состояние сервера.</span><span class="sxs-lookup"><span data-stu-id="64cef-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="64cef-146">Не задает некоторые связанные с транспортом заголовки по умолчанию, так как они обычно не относятся к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="64cef-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="64cef-147">Дополнительные сведения см. в следующем разделе.</span><span class="sxs-lookup"><span data-stu-id="64cef-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="64cef-148">Заголовки Content-Length и Transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="64cef-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="64cef-149">TestServer ***не*** задает связанные с транспортом запросы или заголовки ответа, такие как [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) и [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="64cef-149">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="64cef-150">Приложения не должны зависеть от этих заголовков, так как их использование определяется клиентом, сценарием и протоколом.</span><span class="sxs-lookup"><span data-stu-id="64cef-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="64cef-151">Если `Content-Length` и `Transfer-Encoding` требуются для тестирования конкретного сценария, их можно указать в тесте при создании <xref:System.Net.Http.HttpRequestMessage> или <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="64cef-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="64cef-152">См. сведения см. в следующих проблемах GitHub:</span><span class="sxs-lookup"><span data-stu-id="64cef-152">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="64cef-153">dotnet/aspnetcore#21677</span><span class="sxs-lookup"><span data-stu-id="64cef-153">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="64cef-154">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="64cef-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="64cef-155">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="64cef-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
