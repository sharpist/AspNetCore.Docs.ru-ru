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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="17dbd-103">Межпроцессное взаимодействие с помощью gRPC</span><span class="sxs-lookup"><span data-stu-id="17dbd-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="17dbd-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="17dbd-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="17dbd-105">Вызовы gRPC между клиентом и службой обычно отправляются через сокеты TCP.</span><span class="sxs-lookup"><span data-stu-id="17dbd-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="17dbd-106">Протокол TCP отлично подходит для обмена данными по сети, однако [межпроцессное взаимодействие (IPC)](https://wikipedia.org/wiki/Inter-process_communication) более эффективно, если клиент и служба находятся на одном компьютере.</span><span class="sxs-lookup"><span data-stu-id="17dbd-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="17dbd-107">В этом документе объясняется, как использовать gRPC с настраиваемым транспортом в сценариях IPC.</span><span class="sxs-lookup"><span data-stu-id="17dbd-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="17dbd-108">Конфигурация сервера</span><span class="sxs-lookup"><span data-stu-id="17dbd-108">Server configuration</span></span>

<span data-ttu-id="17dbd-109">Kestrel поддерживает настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="17dbd-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="17dbd-110">Kestrel настраивается в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="17dbd-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="17dbd-111">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="17dbd-111">The preceding example:</span></span>

* <span data-ttu-id="17dbd-112">Настраивает конечные точки Kestrel в `ConfigureKestrel`.</span><span class="sxs-lookup"><span data-stu-id="17dbd-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="17dbd-113">Вызывает <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> для прослушивания [сокета домена UNIX (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="17dbd-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="17dbd-114">Kestrel имеет встроенную поддержку конечных точек UDS.</span><span class="sxs-lookup"><span data-stu-id="17dbd-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="17dbd-115">UDS поддерживаются в операционных системах Linux, macOS и [современных версиях Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span><span class="sxs-lookup"><span data-stu-id="17dbd-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="17dbd-116">Настройка клиента</span><span class="sxs-lookup"><span data-stu-id="17dbd-116">Client configuration</span></span>

<span data-ttu-id="17dbd-117">`GrpcChannel` поддерживает выполнение вызовов gRPC через настраиваемый транспорт.</span><span class="sxs-lookup"><span data-stu-id="17dbd-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="17dbd-118">При создании канала его можно настроить с помощью `SocketsHttpHandler`, у которого имеется настраиваемый `ConnectionFactory`.</span><span class="sxs-lookup"><span data-stu-id="17dbd-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="17dbd-119">Фабрика позволяет клиенту устанавливать соединения через настраиваемые транспорты, а затем передавать HTTP-запросы через этот транспорт.</span><span class="sxs-lookup"><span data-stu-id="17dbd-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17dbd-120">`ConnectionFactory` — это новый API в релизе-кандидате 1 платформы .NET 5.</span><span class="sxs-lookup"><span data-stu-id="17dbd-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="17dbd-121">Пример фабрики подключений для сокетов доменов UNIX:</span><span class="sxs-lookup"><span data-stu-id="17dbd-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="17dbd-122">Использование настраиваемой фабрики подключений для создания канала:</span><span class="sxs-lookup"><span data-stu-id="17dbd-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="17dbd-123">Каналы, созданные с помощью приведенного выше кода, отправляют вызовы gRPC через сокеты доменов UNIX.</span><span class="sxs-lookup"><span data-stu-id="17dbd-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
