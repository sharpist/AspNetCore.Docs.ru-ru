---
title: Тестирование ПО промежуточного слоя ASP.NET Core
author: tratcher
description: Узнайте, как протестировать ПО промежуточного слоя ASP.NET Core с помощью TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876425"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="d1fe8-103">Тестирование ПО промежуточного слоя ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1fe8-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="d1fe8-104">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="d1fe8-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="d1fe8-105">ПО промежуточного слоя можно протестировать изолированно с <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="d1fe8-106">Оно предоставляет следующие возможности.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-106">It allows you to:</span></span>

* <span data-ttu-id="d1fe8-107">Создание экземпляров конвейера приложения, содержащего только те компоненты, которые необходимо протестировать.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="d1fe8-108">Отправка пользовательских запросов для проверки поведения ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="d1fe8-109">Преимущества:</span><span class="sxs-lookup"><span data-stu-id="d1fe8-109">Advantages:</span></span>

* <span data-ttu-id="d1fe8-110">Запросы отправляются в памяти, а не сериализуются по сети.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="d1fe8-111">Это позволяет избежать дополнительных проблем, таких как управление портами и сертификаты HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="d1fe8-112">Исключения в ПО промежуточного слоя могут напрямую возвращаться к вызывающему тесту.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="d1fe8-113">Можно настроить структуры данных сервера, например <xref:Microsoft.AspNetCore.Http.HttpContext>, непосредственно в тесте.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="d1fe8-114">Настройка TestServer</span><span class="sxs-lookup"><span data-stu-id="d1fe8-114">Set up the TestServer</span></span>

<span data-ttu-id="d1fe8-115">В тестовом проекте создайте тест.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-115">In the test project, create a test:</span></span>

* <span data-ttu-id="d1fe8-116">Создайте и запустите узел, который использует <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="d1fe8-117">Добавьте все необходимые службы, используемые ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="d1fe8-118">Настройте конвейер обработки для использования ПО промежуточного слоя для теста.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="d1fe8-119">Отправка запросов с помощью HttpClient</span><span class="sxs-lookup"><span data-stu-id="d1fe8-119">Send requests with HttpClient</span></span>
<span data-ttu-id="d1fe8-120">Отправьте запрос с помощью <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="d1fe8-121">Утвердите результат.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-121">Assert the result.</span></span> <span data-ttu-id="d1fe8-122">Сначала следует сделать утверждение, противоположное ожидаемому результату.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="d1fe8-123">Первое выполнение с ложноположительным утверждением подтверждает, что тест завершается ошибкой, если ПО промежуточного слоя выполняется правильно.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="d1fe8-124">Запустите тест и убедитесь, что тест не пройден.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="d1fe8-125">В следующем примере ПО промежуточного слоя должно вернуть код состояния 404 (*Не найдено*) при запросе корневой конечной точки.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="d1fe8-126">Выполните первый тестовый запуск с `Assert.NotEqual( ... );`, который должен завершиться ошибкой.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="d1fe8-127">Измените утверждение, чтобы проверить ПО промежуточного слоя в нормальных условиях работы.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="d1fe8-128">В последнем тесте используется `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="d1fe8-129">Запустите тест еще раз, чтобы убедиться, что он пройден.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="d1fe8-130">Отправка запросов с помощью HttpContext</span><span class="sxs-lookup"><span data-stu-id="d1fe8-130">Send requests with HttpContext</span></span>

<span data-ttu-id="d1fe8-131">Тестовое приложение также может отправить запрос с помощью [SendAsync (Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="d1fe8-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="d1fe8-132">В следующем примере выполняется несколько проверок, когда `https://example.com/A/Path/?and=query` обрабатывается в ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="d1fe8-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="d1fe8-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> разрешает прямую настройку объекта <xref:Microsoft.AspNetCore.Http.HttpContext> вместо использования абстракций <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="d1fe8-134">Используйте <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> для управления структурами, доступными только на сервере, например [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) или [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="d1fe8-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="d1fe8-135">Как и в предыдущем примере, где ожидалась ошибка *404 — не найдено*, проверьте противоположность каждой инструкции `Assert` в предыдущем тесте.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="d1fe8-136">Проверка покажет, что тест действительно завершается ошибкой, если ПО промежуточного слоя работает нормально.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="d1fe8-137">Убедившись, что тест дает ложноположительный результат, установите окончательные инструкции `Assert` для ожидаемых условий и значений теста.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="d1fe8-138">Запустите тест еще раз, чтобы убедиться, что он пройден.</span><span class="sxs-lookup"><span data-stu-id="d1fe8-138">Run it again to confirm that the test passes.</span></span>
