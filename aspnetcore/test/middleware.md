---
title: Тестирование ПО промежуточного слоя ASP.NET Core
author: tratcher
description: Узнайте, как протестировать ПО промежуточного слоя ASP.NET Core с помощью TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: f4ed16b136da37c093a72a8866301a188a8518a2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406489"
---
# <a name="test-aspnet-core-middleware"></a>Тестирование ПО промежуточного слоя ASP.NET Core

Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)

ПО промежуточного слоя можно протестировать изолированно с <xref:Microsoft.AspNetCore.TestHost.TestServer>. Оно предоставляет следующие возможности.

* Создание экземпляров конвейера приложения, содержащего только те компоненты, которые необходимо протестировать.
* Отправка пользовательских запросов для проверки поведения ПО промежуточного слоя.

Преимущества:

* Запросы отправляются в памяти, а не сериализуются по сети.
* Это позволяет избежать дополнительных проблем, таких как управление портами и сертификаты HTTPS.
* Исключения в ПО промежуточного слоя могут напрямую возвращаться к вызывающему тесту.
* Можно настроить структуры данных сервера, например <xref:Microsoft.AspNetCore.Http.HttpContext>, непосредственно в тесте.

## <a name="set-up-the-testserver"></a>Настройка TestServer

В тестовом проекте создайте тест.

* Создайте и запустите узел, который использует <xref:Microsoft.AspNetCore.TestHost.TestServer>.
* Добавьте все необходимые службы, используемые ПО промежуточного слоя.
* Добавьте в проект пакет NuGet [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/).
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Настройте конвейер обработки для использования ПО промежуточного слоя для теста.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Отправка запросов с помощью HttpClient
Отправьте запрос с помощью <xref:System.Net.Http.HttpClient>.

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Утвердите результат. Сначала следует сделать утверждение, противоположное ожидаемому результату. Первое выполнение с ложноположительным утверждением подтверждает, что тест завершается ошибкой, если ПО промежуточного слоя выполняется правильно. Запустите тест и убедитесь, что тест не пройден.

В следующем примере ПО промежуточного слоя должно вернуть код состояния 404 (*Не найдено*) при запросе корневой конечной точки. Выполните первый тестовый запуск с `Assert.NotEqual( ... );`, который должен завершиться ошибкой.

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Измените утверждение, чтобы проверить ПО промежуточного слоя в нормальных условиях работы. В последнем тесте используется `Assert.Equal( ... );`. Запустите тест еще раз, чтобы убедиться, что он пройден.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Отправка запросов с помощью HttpContext

Тестовое приложение также может отправить запрос с помощью [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). В следующем примере выполняется несколько проверок, когда `https://example.com/A/Path/?and=query` обрабатывается в ПО промежуточного слоя:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> разрешает прямую настройку объекта <xref:Microsoft.AspNetCore.Http.HttpContext> вместо использования абстракций <xref:System.Net.Http.HttpClient>. Используйте <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> для управления структурами, доступными только на сервере, например [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) или [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Как и в предыдущем примере, где ожидалась ошибка *404 — не найдено*, проверьте противоположность каждой инструкции `Assert` в предыдущем тесте. Проверка покажет, что тест действительно завершается ошибкой, если ПО промежуточного слоя работает нормально. Убедившись, что тест дает ложноположительный результат, установите окончательные инструкции `Assert` для ожидаемых условий и значений теста. Запустите тест еще раз, чтобы убедиться, что он пройден.

## <a name="testserver-limitations"></a>Ограничения TestServer

TestServer:

* используется для репликации поведения сервера для проверки ПО промежуточного слоя.
* ***Не*** пытается выполнить репликацию всего поведения <xref:System.Net.Http.HttpClient>.
* Пытается предоставить клиенту максимально возможный контроль над сервером и обеспечить максимальную возможность отслеживания того, что происходит на сервере. Например, он может вызывать исключения, которые обычно не вызываются `HttpClient`, чтобы напрямую передавать состояние сервера.
* Не задает некоторые связанные с транспортом заголовки по умолчанию, так как они обычно не относятся к ПО промежуточного слоя. Дополнительные сведения см. в следующем разделе.

### <a name="content-length-and-transfer-encoding-headers"></a>Заголовки Content-Length и Transfer-Encoding

TestServer ***не*** задает связанные с транспортом запросы или заголовки ответа, такие как [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) и [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding). Приложения не должны зависеть от этих заголовков, так как их использование определяется клиентом, сценарием и протоколом. Если `Content-Length` и `Transfer-Encoding` требуются для тестирования конкретного сценария, их можно указать в тесте при создании <xref:System.Net.Http.HttpRequestMessage> или <xref:Microsoft.AspNetCore.Http.HttpContext>. См. сведения см. в следующих проблемах GitHub:

* [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore#18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore#13273](https://github.com/dotnet/aspnetcore/issues/13273)
