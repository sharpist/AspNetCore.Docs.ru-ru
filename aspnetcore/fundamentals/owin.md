---
title: Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core
author: ardalis
description: Сведения о том, как ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN), позволяющий ослабить связь веб-приложений с веб-серверами.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- appsettings.json
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
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060681"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="67889-103">Открытый веб-интерфейс для .NET (OWIN) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67889-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="67889-104">Авторы: [Стив Смит (Steve Smith)](https://ardalis.com/) и [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="67889-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="67889-105">ASP.NET Core поддерживает открытый веб-интерфейс для .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="67889-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="67889-106">OWIN позволяет ослабить зависимость веб-приложений от веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="67889-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="67889-107">Он определяет стандартный способ использования ПО промежуточного в конвейере для обработки запросов и связанных с ними откликов.</span><span class="sxs-lookup"><span data-stu-id="67889-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="67889-108">Приложения ASP.NET Core и ПО промежуточного слоя могут взаимодействовать с приложениями, серверами и ПО промежуточного слоя, основанными на OWIN.</span><span class="sxs-lookup"><span data-stu-id="67889-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="67889-109">OWIN обеспечивает разделительный уровень, позволяющий совместно использовать две платформы с разнородными объектными моделями.</span><span class="sxs-lookup"><span data-stu-id="67889-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="67889-110">Пакет `Microsoft.AspNetCore.Owin` содержит две реализации адаптера:</span><span class="sxs-lookup"><span data-stu-id="67889-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="67889-111">ASP.NET Core в OWIN</span><span class="sxs-lookup"><span data-stu-id="67889-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="67889-112">OWIN в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67889-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="67889-113">Это позволяет размещать ASP.NET Core поверх сервера или узла, совместимого с OWIN, а также запускать другие совместимые с OWIN компоненты поверх ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67889-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="67889-114">Использование этих адаптеров сопряжено с потерями производительности.</span><span class="sxs-lookup"><span data-stu-id="67889-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="67889-115">Приложениям, использующим только компоненты ASP.NET Core, не следует использовать адаптеры или пакет `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="67889-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="67889-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="67889-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="67889-117">Выполнение ПО промежуточного слоя OWIN в конвейере ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67889-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="67889-118">Поддержка OWIN для ASP.NET Core развертывается в составе пакета `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="67889-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="67889-119">Вы можете импортировать поддержку OWIN в проект, установив этот пакет.</span><span class="sxs-lookup"><span data-stu-id="67889-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="67889-120">ПО промежуточного слоя OWIN соответствует [спецификации OWIN](https://owin.org/spec/spec/owin-1.0.0.html), где требуется задать интерфейс `Func<IDictionary<string, object>, Task>` и определенные ключи (такие как `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="67889-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="67889-121">Следующее простое ПО промежуточного слоя OWIN отображает сообщение "Hello World":</span><span class="sxs-lookup"><span data-stu-id="67889-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="67889-122">Сигнатура примера возвращает `Task` и принимает `IDictionary<string, object>`, как того требует OWIN.</span><span class="sxs-lookup"><span data-stu-id="67889-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="67889-123">Приведенный ниже код показывает, как добавить ПО промежуточного слоя `OwinHello` (показано выше) в конвейер ASP.NET Core с помощью метода расширения `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="67889-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="67889-124">В конвейере OWIN можно настроить и другие действия.</span><span class="sxs-lookup"><span data-stu-id="67889-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="67889-125">Заголовки отклика можно изменять только до первой записи в поток отклика.</span><span class="sxs-lookup"><span data-stu-id="67889-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="67889-126">Из соображений производительности не рекомендуется выполнять несколько вызовов `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="67889-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="67889-127">Компоненты OWIN лучше всего работают, когда сгруппированы друг с другом.</span><span class="sxs-lookup"><span data-stu-id="67889-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="67889-128">Запуск ASP.NET Core на основанном на OWIN сервере и использование его поддержки WebSocket</span><span class="sxs-lookup"><span data-stu-id="67889-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="67889-129">Другим примером того, как ASP.NET Core может использовать функции сервера на основе OWIN, является доступ к таким функциям, как WebSocket.</span><span class="sxs-lookup"><span data-stu-id="67889-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="67889-130">Веб-сервер OWIN .NET, используемый в предыдущем примере, имеет встроенную поддержку веб-сокетов, что может использовать приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="67889-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="67889-131">Приведенный ниже пример показывает простое веб-приложение, которое поддерживает веб-сокеты и выводит обратно все данные, отправляемые на сервер через WebSocket.</span><span class="sxs-lookup"><span data-stu-id="67889-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="67889-132">Среда OWIN</span><span class="sxs-lookup"><span data-stu-id="67889-132">OWIN environment</span></span>

<span data-ttu-id="67889-133">Вы можете создать среду OWIN с помощью `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="67889-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="67889-134">Ключи OWIN</span><span class="sxs-lookup"><span data-stu-id="67889-134">OWIN keys</span></span>

<span data-ttu-id="67889-135">OWIN использует объект `IDictionary<string,object>` для передачи сведений через систему обмена запросами и откликами HTTP.</span><span class="sxs-lookup"><span data-stu-id="67889-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="67889-136">ASP.NET Core реализует указанные ниже ключи.</span><span class="sxs-lookup"><span data-stu-id="67889-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="67889-137">См. [основную спецификацию, расширения](https://owin.org/#spec), а также статью [Рекомендации по ключам OWIN и общие ключи](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="67889-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="67889-138">Данные запроса (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="67889-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="67889-139">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-139">Key</span></span>               | <span data-ttu-id="67889-140">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-140">Value (type)</span></span> | <span data-ttu-id="67889-141">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="67889-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="67889-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="67889-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="67889-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="67889-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="67889-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="67889-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="67889-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="67889-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="67889-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="67889-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="67889-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="67889-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="67889-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="67889-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="67889-150">Данные запроса (OWIN версии 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="67889-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="67889-151">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-151">Key</span></span>               | <span data-ttu-id="67889-152">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-152">Value (type)</span></span> | <span data-ttu-id="67889-153">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="67889-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="67889-155">Необязательный</span><span class="sxs-lookup"><span data-stu-id="67889-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="67889-156">Данные отклика (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="67889-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="67889-157">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-157">Key</span></span>               | <span data-ttu-id="67889-158">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-158">Value (type)</span></span> | <span data-ttu-id="67889-159">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="67889-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="67889-161">Необязательный</span><span class="sxs-lookup"><span data-stu-id="67889-161">Optional</span></span> |
| <span data-ttu-id="67889-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="67889-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="67889-163">Необязательный</span><span class="sxs-lookup"><span data-stu-id="67889-163">Optional</span></span> |
| <span data-ttu-id="67889-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="67889-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="67889-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="67889-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="67889-166">Другие данные (OWIN версии 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="67889-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="67889-167">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-167">Key</span></span>               | <span data-ttu-id="67889-168">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-168">Value (type)</span></span> | <span data-ttu-id="67889-169">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="67889-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="67889-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="67889-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="67889-172">Общие ключи</span><span class="sxs-lookup"><span data-stu-id="67889-172">Common keys</span></span>

| <span data-ttu-id="67889-173">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-173">Key</span></span>               | <span data-ttu-id="67889-174">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-174">Value (type)</span></span> | <span data-ttu-id="67889-175">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="67889-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="67889-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="67889-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="67889-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="67889-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="67889-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="67889-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="67889-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="67889-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="67889-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="67889-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="67889-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="67889-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="67889-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="67889-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="67889-184">SendFiles версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="67889-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="67889-185">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-185">Key</span></span>               | <span data-ttu-id="67889-186">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-186">Value (type)</span></span> | <span data-ttu-id="67889-187">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="67889-188">sendfile.SendAsync</span></span> | <span data-ttu-id="67889-189">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="67889-190">Для каждого запроса</span><span class="sxs-lookup"><span data-stu-id="67889-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="67889-191">Opaque версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="67889-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="67889-192">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-192">Key</span></span>               | <span data-ttu-id="67889-193">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-193">Value (type)</span></span> | <span data-ttu-id="67889-194">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="67889-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="67889-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="67889-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="67889-197">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="67889-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="67889-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="67889-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="67889-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="67889-200">WebSocket версии 0.3.0</span><span class="sxs-lookup"><span data-stu-id="67889-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="67889-201">Ключ</span><span class="sxs-lookup"><span data-stu-id="67889-201">Key</span></span>               | <span data-ttu-id="67889-202">Значение (тип)</span><span class="sxs-lookup"><span data-stu-id="67889-202">Value (type)</span></span> | <span data-ttu-id="67889-203">Описание</span><span class="sxs-lookup"><span data-stu-id="67889-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="67889-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="67889-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="67889-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="67889-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="67889-206">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="67889-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="67889-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="67889-208">Нет в спецификации</span><span class="sxs-lookup"><span data-stu-id="67889-208">Non-spec</span></span> |
| <span data-ttu-id="67889-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="67889-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="67889-210">См. [раздел 4.2.2 RFC6455](https://tools.ietf.org/html/rfc6455#section-4.2.2), шаг 5.5</span><span class="sxs-lookup"><span data-stu-id="67889-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="67889-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="67889-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="67889-212">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="67889-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="67889-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="67889-214">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="67889-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="67889-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="67889-216">См. описание [сигнатуры делегата](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="67889-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="67889-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="67889-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="67889-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="67889-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="67889-219">Необязательный</span><span class="sxs-lookup"><span data-stu-id="67889-219">Optional</span></span> |
| <span data-ttu-id="67889-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="67889-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="67889-221">Необязательный</span><span class="sxs-lookup"><span data-stu-id="67889-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="67889-222">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="67889-222">Additional resources</span></span>

* [<span data-ttu-id="67889-223">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="67889-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="67889-224">Серверы</span><span class="sxs-lookup"><span data-stu-id="67889-224">Servers</span></span>](xref:fundamentals/servers/index)
