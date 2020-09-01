---
title: Межпроцессное взаимодействие с помощью gRPC
author: jamesnk
description: Узнайте, как использовать gRPC для межпроцессного взаимодействия.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945521"
---
# <a name="inter-process-communication-with-grpc"></a>Межпроцессное взаимодействие с помощью gRPC

Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)

Вызовы gRPC между клиентом и службой обычно отправляются через сокеты TCP. Протокол TCP отлично подходит для обмена данными по сети, однако [межпроцессное взаимодействие (IPC)](https://wikipedia.org/wiki/Inter-process_communication) более эффективно, если клиент и служба находятся на одном компьютере. В этом документе объясняется, как использовать gRPC с настраиваемым транспортом в сценариях IPC.

## <a name="server-configuration"></a>Конфигурация сервера

Kestrel поддерживает настраиваемый транспорт. Kestrel настраивается в файле *Program.cs*:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

Предшествующий пример:

* Настраивает конечные точки Kestrel в `ConfigureKestrel`.
* Вызывает <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> для прослушивания [сокета домена UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) по указанному пути.

Kestrel имеет встроенную поддержку конечных точек UDS. UDS поддерживаются в операционных системах Linux, macOS и [современных версиях Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).

## <a name="client-configuration"></a>Настройка клиента

`GrpcChannel` поддерживает выполнение вызовов gRPC через настраиваемый транспорт. При создании канала его можно настроить с помощью `SocketsHttpHandler`, у которого имеется настраиваемый `ConnectionFactory`. Фабрика позволяет клиенту устанавливать соединения через настраиваемые транспорты, а затем передавать HTTP-запросы через этот транспорт.

> [!IMPORTANT]
> `ConnectionFactory` — это новый API в релизе-кандидате 1 платформы .NET 5.

Пример фабрики подключений для сокетов доменов UNIX:

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

Использование настраиваемой фабрики подключений для создания канала:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Каналы, созданные с помощью приведенного выше кода, отправляют вызовы gRPC через сокеты доменов UNIX.
