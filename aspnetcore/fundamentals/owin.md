---
title: Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core
author: ardalis
description: Сведения о том, как ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN), позволяющий ослабить связь веб-приложений с веб-серверами.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: d766ba3387edbfb9298b6f3cf8a485738b7d7139
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628603"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="21b1d-103">Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21b1d-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="21b1d-104">Авторы: [Стив Смит (Steve Smith)](https://ardalis.com/) и [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="21b1d-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="21b1d-105">ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="21b1d-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="21b1d-106">OWIN позволяет ослабить зависимость веб-приложений от веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="21b1d-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="21b1d-107">Он определяет стандартный способ использования ПО промежуточного в конвейере для обработки запросов и связанных с ними откликов.</span><span class="sxs-lookup"><span data-stu-id="21b1d-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="21b1d-108">Приложения ASP.NET Core и ПО промежуточного слоя могут взаимодействовать с приложениями, серверами и ПО промежуточного слоя, основанными на OWIN.</span><span class="sxs-lookup"><span data-stu-id="21b1d-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="21b1d-109">OWIN обеспечивает разделительный уровень, позволяющий совместно использовать две платформы с разнородными объектными моделями.</span><span class="sxs-lookup"><span data-stu-id="21b1d-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="21b1d-110">Пакет `Microsoft.AspNetCore.Owin` содержит две реализации адаптера:</span><span class="sxs-lookup"><span data-stu-id="21b1d-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="21b1d-111">ASP.NET Core в OWIN</span><span class="sxs-lookup"><span data-stu-id="21b1d-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="21b1d-112">OWIN в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21b1d-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="21b1d-113">Это позволяет размещать ASP.NET Core поверх сервера или узла, совместимого с OWIN, а также запускать другие совместимые с OWIN компоненты поверх ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21b1d-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="21b1d-114">Использование этих адаптеров сопряжено с потерями производительности.</span><span class="sxs-lookup"><span data-stu-id="21b1d-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="21b1d-115">Приложениям, использующим только компоненты ASP.NET Core, не следует использовать адаптеры или пакет `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="21b1d-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="21b1d-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="21b1d-117">Выполнение ПО промежуточного слоя OWIN в конвейере ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21b1d-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="21b1d-118">Поддержка OWIN для ASP.NET Core развертывается в составе пакета `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="21b1d-119">Вы можете импортировать поддержку OWIN в проект, установив этот пакет.</span><span class="sxs-lookup"><span data-stu-id="21b1d-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="21b1d-120">ПО промежуточного слоя OWIN соответствует [спецификации OWIN](https://owin.org/spec/spec/owin-1.0.0.html), где требуется задать интерфейс `Func<IDictionary<string, object>, Task>` и определенные ключи (такие как `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="21b1d-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="21b1d-121">Следующее простое ПО промежуточного слоя OWIN отображает сообщение "Hello World":</span><span class="sxs-lookup"><span data-stu-id="21b1d-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="21b1d-122">Сигнатура примера возвращает `Task` и принимает `IDictionary<string, object>`, как того требует OWIN.</span><span class="sxs-lookup"><span data-stu-id="21b1d-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="21b1d-123">Приведенный ниже код показывает, как добавить ПО промежуточного слоя `OwinHello` (показано выше) в конвейер ASP.NET Core с помощью метода расширения `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="21b1d-124">В конвейере OWIN можно настроить и другие действия.</span><span class="sxs-lookup"><span data-stu-id="21b1d-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="21b1d-125">Заголовки отклика можно изменять только до первой записи в поток отклика.</span><span class="sxs-lookup"><span data-stu-id="21b1d-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="21b1d-126">Из соображений производительности не рекомендуется выполнять несколько вызовов `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="21b1d-127">Компоненты OWIN лучше всего работают, когда сгруппированы друг с другом.</span><span class="sxs-lookup"><span data-stu-id="21b1d-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="21b1d-128">Использование размещения ASP.NET Core на сервере OWIN</span><span class="sxs-lookup"><span data-stu-id="21b1d-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="21b1d-129">На основанных на OWIN серверах можно размещать приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21b1d-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="21b1d-130">Одним из них является веб-сервер OWIN для .NET с именем [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="21b1d-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="21b1d-131">В пример для этой статьи я включил проект, который ссылается на Nowin и использует его для создания `IServer`, способного самостоятельно размещать ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21b1d-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="21b1d-132">`IServer` — это интерфейс, которому нужно свойство `Features` и метод `Start`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="21b1d-133">`Start` отвечает за настройку и запуск сервера, что в данном случае осуществляется с помощью ряда вызовов текучего API, которые задают адреса, полученные из IServerAddressesFeature путем анализа.</span><span class="sxs-lookup"><span data-stu-id="21b1d-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="21b1d-134">Обратите внимание, что текучая конфигурация переменной `_builder` указывает, что запросы будут обрабатывать `appFunc`, определенной ранее в этом методе.</span><span class="sxs-lookup"><span data-stu-id="21b1d-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="21b1d-135">Эта `Func` вызывается при каждом запросе на обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="21b1d-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="21b1d-136">Мы также добавим расширение `IWebHostBuilder`, чтобы упростить добавление и настройку сервера Nowin.</span><span class="sxs-lookup"><span data-stu-id="21b1d-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="21b1d-137">Благодаря ему вы сможете вызвать расширение в *Program.cs* для запуска приложения ASP.NET Core с помощью этого пользовательского сервера:</span><span class="sxs-lookup"><span data-stu-id="21b1d-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="21b1d-138">Дополнительные сведения о [серверах ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="21b1d-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="21b1d-139">Запуск ASP.NET Core на основанном на OWIN сервере и использование его поддержки WebSocket</span><span class="sxs-lookup"><span data-stu-id="21b1d-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="21b1d-140">Другим примером того, как ASP.NET Core может использовать функции сервера на основе OWIN, является доступ к таким функциям, как WebSocket.</span><span class="sxs-lookup"><span data-stu-id="21b1d-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="21b1d-141">Веб-сервер OWIN .NET, используемый в предыдущем примере, имеет встроенную поддержку веб-сокетов, что может использовать приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21b1d-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="21b1d-142">Приведенный ниже пример показывает простое веб-приложение, которое поддерживает веб-сокеты и выводит обратно все данные, отправляемые на сервер через WebSocket.</span><span class="sxs-lookup"><span data-stu-id="21b1d-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="21b1d-143">Этот [пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) настроен с использованием того же `NowinServer`, что и предыдущий. Единственное различие заключается в том, как приложение настроено в его методе `Configure`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="21b1d-144">Тест, использующий [простой клиент WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en), демонстрирует работу приложения:</span><span class="sxs-lookup"><span data-stu-id="21b1d-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Тестовый клиент WebSocket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="21b1d-146">Среда OWIN</span><span class="sxs-lookup"><span data-stu-id="21b1d-146">OWIN environment</span></span>

<span data-ttu-id="21b1d-147">Вы можете создать среду OWIN с помощью `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="21b1d-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="21b1d-148">Ключи OWIN</span><span class="sxs-lookup"><span data-stu-id="21b1d-148">OWIN keys</span></span>

<span data-ttu-id="21b1d-149">OWIN использует объект `IDictionary<string,object>` для передачи сведений через систему обмена запросами и откликами HTTP.</span><span class="sxs-lookup"><span data-stu-id="21b1d-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="21b1d-150">ASP.NET Core реализует указанные ниже ключи.</span><span class="sxs-lookup"><span data-stu-id="21b1d-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="21b1d-151">См. [основную спецификацию, расширения](https://owin.org/#spec), а также статью [Рекомендации по ключам OWIN и общие ключи](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="21b1d-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="21b1d-152">Данные запроса (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21b1d-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21b1d-153">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-153">Key</span></span>               | <span data-ttu-id="21b1d-154">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-154">Value (type)</span></span> | <span data-ttu-id="21b1d-155">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="21b1d-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="21b1d-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="21b1d-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="21b1d-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="21b1d-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="21b1d-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="21b1d-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="21b1d-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="21b1d-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="21b1d-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="21b1d-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="21b1d-164">Данные запроса (OWIN версии 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="21b1d-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="21b1d-165">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-165">Key</span></span>               | <span data-ttu-id="21b1d-166">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-166">Value (type)</span></span> | <span data-ttu-id="21b1d-167">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="21b1d-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="21b1d-169">Необязательный</span><span class="sxs-lookup"><span data-stu-id="21b1d-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="21b1d-170">Данные отклика (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21b1d-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21b1d-171">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-171">Key</span></span>               | <span data-ttu-id="21b1d-172">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-172">Value (type)</span></span> | <span data-ttu-id="21b1d-173">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="21b1d-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="21b1d-175">Необязательный</span><span class="sxs-lookup"><span data-stu-id="21b1d-175">Optional</span></span> |
| <span data-ttu-id="21b1d-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="21b1d-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="21b1d-177">Необязательный</span><span class="sxs-lookup"><span data-stu-id="21b1d-177">Optional</span></span> |
| <span data-ttu-id="21b1d-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="21b1d-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="21b1d-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="21b1d-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="21b1d-180">Другие данные (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="21b1d-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="21b1d-181">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-181">Key</span></span>               | <span data-ttu-id="21b1d-182">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-182">Value (type)</span></span> | <span data-ttu-id="21b1d-183">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21b1d-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="21b1d-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="21b1d-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="21b1d-186">Общие ключи</span><span class="sxs-lookup"><span data-stu-id="21b1d-186">Common keys</span></span>

| <span data-ttu-id="21b1d-187">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-187">Key</span></span>               | <span data-ttu-id="21b1d-188">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-188">Value (type)</span></span> | <span data-ttu-id="21b1d-189">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="21b1d-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="21b1d-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="21b1d-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="21b1d-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="21b1d-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="21b1d-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="21b1d-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="21b1d-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="21b1d-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="21b1d-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="21b1d-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="21b1d-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="21b1d-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="21b1d-198">SendFiles версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="21b1d-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="21b1d-199">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-199">Key</span></span>               | <span data-ttu-id="21b1d-200">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-200">Value (type)</span></span> | <span data-ttu-id="21b1d-201">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="21b1d-202">sendfile.SendAsync</span></span> | <span data-ttu-id="21b1d-203">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="21b1d-204">Для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="21b1d-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="21b1d-205">Opaque версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="21b1d-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="21b1d-206">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-206">Key</span></span>               | <span data-ttu-id="21b1d-207">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-207">Value (type)</span></span> | <span data-ttu-id="21b1d-208">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="21b1d-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="21b1d-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="21b1d-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="21b1d-211">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="21b1d-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="21b1d-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="21b1d-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21b1d-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="21b1d-214">WebSocket версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="21b1d-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="21b1d-215">Ключ</span><span class="sxs-lookup"><span data-stu-id="21b1d-215">Key</span></span>               | <span data-ttu-id="21b1d-216">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="21b1d-216">Value (type)</span></span> | <span data-ttu-id="21b1d-217">Описание</span><span class="sxs-lookup"><span data-stu-id="21b1d-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="21b1d-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="21b1d-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="21b1d-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="21b1d-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="21b1d-220">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="21b1d-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="21b1d-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="21b1d-222">Нет в спецификации</span><span class="sxs-lookup"><span data-stu-id="21b1d-222">Non-spec</span></span> |
| <span data-ttu-id="21b1d-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="21b1d-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="21b1d-224">См. [раздел 4.2.2 RFC6455](https://tools.ietf.org/html/rfc6455#section-4.2.2), шаг 5.5</span><span class="sxs-lookup"><span data-stu-id="21b1d-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="21b1d-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="21b1d-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="21b1d-226">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21b1d-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="21b1d-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="21b1d-228">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21b1d-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="21b1d-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="21b1d-230">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="21b1d-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="21b1d-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="21b1d-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="21b1d-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="21b1d-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="21b1d-233">Необязательный</span><span class="sxs-lookup"><span data-stu-id="21b1d-233">Optional</span></span> |
| <span data-ttu-id="21b1d-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="21b1d-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="21b1d-235">Необязательный</span><span class="sxs-lookup"><span data-stu-id="21b1d-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="21b1d-236">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="21b1d-236">Additional resources</span></span>

* [<span data-ttu-id="21b1d-237">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="21b1d-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="21b1d-238">Серверы</span><span class="sxs-lookup"><span data-stu-id="21b1d-238">Servers</span></span>](xref:fundamentals/servers/index)
