---
название: автор: описание: monikerRange: ms.author: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="grpc-client-factory-integration-in-net-core"></a>Интеграция производства клиента gRPC в .NET Core

Интеграция gRPC с `HttpClientFactory` обеспечивает централизованный способ создания клиентов gRPC. Его можно использовать в качестве альтернативы [настройке отдельных экземпляров клиента gRPC](xref:grpc/client). Интеграция производства доступна в пакете NuGet [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory).

Производство обеспечивает следующие преимущества:

* предоставляет центральное расположение для настройки логических экземпляров клиента gRPC;
* управляет временем существования базового объекта `HttpClientMessageHandler`;
* автоматически распространяет крайний срок и отмену в службе gRPC ASP.NET Core.

## <a name="register-grpc-clients"></a>Регистрация клиентов gRPC

Для регистрации клиента gRPC можно использовать универсальный метод расширения `AddGrpcClient` в `Startup.ConfigureServices`, указав класс типизированного клиента gRPC и адрес службы:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Тип клиента gRPC регистрируется в системе внедрения зависимостей (DI) как временный. После этого клиент можно внедрять и использовать напрямую в типах, создаваемых посредством внедрения зависимостей. Автоматически внедрять клиенты gRPC можно в контроллеры MVC ASP.NET Core, концентраторы SignalR и службы gRPC:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Настройка HttpClient

`HttpClientFactory` создает объект `HttpClient`, используемый клиентом gRPC. С помощью стандартных методов `HttpClientFactory` можно добавлять ПО промежуточного слоя для исходящих запросов или настраивать базовый обработчик `HttpClientHandler` объекта `HttpClient`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Дополнительные сведения см. в статье [Выполнение HTTP-запросов с помощью IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Настройка канала и перехватчиков

Для gRPC доступны специальные методы, позволяющие делать следующее:

* настраивать базовый канал клиента gRPC;
* добавлять экземпляры `Interceptor`, которые клиент будет использовать при выполнении вызовов gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Распространение крайнего срока и отмены

С помощью метода `EnableCallContextPropagation()` для клиентов gRPC, созданных производством в службе gRPC, можно настроить автоматическое распространение токена крайнего срока и отмены на дочерние вызовы. Метод расширения `EnableCallContextPropagation()` доступен в пакете NuGet [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory).

Распространение контекста вызова осуществляется путем считывания токена крайнего срока и отмены из текущего контекста запроса gRPC и его автоматического распространения на исходящие вызовы, выполняемые клиентом gRPC. Распространение контекста вызова — это отличный способ обеспечить распространение крайнего срока и отмены в случае со сложными вложенными вызовами gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

По умолчанию `EnableCallContextPropagation` вызывает ошибку, если клиент используется вне контекста вызова gRPC. Эта ошибка информирует о том, что контекст вызова для распространения отсутствует. Если вы хотите использовать клиент за пределами контекста вызова, подавите ошибку при настройке клиента с использованием `SuppressContextNotFoundErrors`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Дополнительные сведения о крайних сроках и отмене RPC см. в разделе, посвященном [жизненному циклу RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
