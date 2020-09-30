---
title: Межпроцессное взаимодействие с помощью gRPC
author: jamesnk
description: Узнайте, как использовать gRPC для межпроцессного взаимодействия.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
ms.openlocfilehash: 34876f31cbc51ba66a91ae32ea6a5213dc34a369
ms.sourcegitcommit: 9c031530d2e652fe422e786bd43392bc500d622f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2020
ms.locfileid: "90770159"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="5a183-103">Межпроцессное взаимодействие с помощью gRPC</span><span class="sxs-lookup"><span data-stu-id="5a183-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="5a183-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5a183-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5a183-105">Вызовы gRPC между клиентом и службой обычно отправляются через сокеты TCP.</span><span class="sxs-lookup"><span data-stu-id="5a183-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="5a183-106">Протокол TCP был разработан для обмена данными по сети.</span><span class="sxs-lookup"><span data-stu-id="5a183-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="5a183-107">[Межпроцессное взаимодействие (IPC)](https://wikipedia.org/wiki/Inter-process_communication) более эффективно, чем TCP, если клиент и служба находятся на одном компьютере.</span><span class="sxs-lookup"><span data-stu-id="5a183-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="5a183-108">В этом документе объясняется, как использовать gRPC с настраиваемым транспортом в сценариях IPC.</span><span class="sxs-lookup"><span data-stu-id="5a183-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="5a183-109">Конфигурация сервера</span><span class="sxs-lookup"><span data-stu-id="5a183-109">Server configuration</span></span>

<span data-ttu-id="5a183-110">[Kestrel](xref:fundamentals/servers/kestrel) поддерживает настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="5a183-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="5a183-111">Kestrel настраивается в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5a183-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="5a183-112">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="5a183-112">The preceding example:</span></span>

* <span data-ttu-id="5a183-113">Настраивает конечные точки Kestrel в `ConfigureKestrel`.</span><span class="sxs-lookup"><span data-stu-id="5a183-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="5a183-114">Вызывает <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> для прослушивания [сокета домена UNIX (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="5a183-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="5a183-115">Kestrel имеет встроенную поддержку конечных точек UDS.</span><span class="sxs-lookup"><span data-stu-id="5a183-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="5a183-116">UDS поддерживаются в операционных системах Linux, macOS и [современных версиях Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="5a183-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="5a183-117">Настройка клиента</span><span class="sxs-lookup"><span data-stu-id="5a183-117">Client configuration</span></span>

<span data-ttu-id="5a183-118">`GrpcChannel` поддерживает выполнение вызовов gRPC через настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="5a183-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="5a183-119">При создании канала его можно настроить с помощью `SocketsHttpHandler`, у которого имеется настраиваемый `ConnectCallback`.</span><span class="sxs-lookup"><span data-stu-id="5a183-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="5a183-120">Обратный вызов позволяет клиенту устанавливать соединения через настраиваемые транспорты, а затем передавать HTTP-запросы через этот транспорт.</span><span class="sxs-lookup"><span data-stu-id="5a183-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5a183-121">`SocketsHttpHandler.ConnectCallback` — это новый API в релизе-кандидате 2 платформы .NET 5.</span><span class="sxs-lookup"><span data-stu-id="5a183-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="5a183-122">Пример фабрики подключений для сокетов доменов UNIX:</span><span class="sxs-lookup"><span data-stu-id="5a183-122">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

<span data-ttu-id="5a183-123">Использование настраиваемой фабрики подключений для создания канала:</span><span class="sxs-lookup"><span data-stu-id="5a183-123">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="5a183-124">Каналы, созданные с помощью приведенного выше кода, отправляют вызовы gRPC через сокеты доменов UNIX.</span><span class="sxs-lookup"><span data-stu-id="5a183-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="5a183-125">Поддержку других технологий IPC можно реализовать с помощью расширяемости в Kestrel и `SocketsHttpHandler`.</span><span class="sxs-lookup"><span data-stu-id="5a183-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
