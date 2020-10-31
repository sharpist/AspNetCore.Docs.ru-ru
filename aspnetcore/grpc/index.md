---
title: Общие сведения об использовании gRPC на платформе .NET Core
author: juntaoluo
description: Узнайте об использовании служб gRPC с сервером Kestrel и стеком ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/index
ms.openlocfilehash: 80f44e3845cc1e3c87d5d657807a318eb65e6c6f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059901"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="fe9c7-103">Общие сведения об использовании gRPC на платформе .NET Core</span><span class="sxs-lookup"><span data-stu-id="fe9c7-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="fe9c7-104">Авторы: [Джон Луо](https://github.com/juntaoluo) (John Luo) и [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="fe9c7-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="fe9c7-105">[gRPC](https://grpc.io/docs/guides/) — это не зависящая от языка высокопроизводительная платформа удаленного вызова процедур (RPC).</span><span class="sxs-lookup"><span data-stu-id="fe9c7-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="fe9c7-106">Ниже приведены основные преимущества gRPC.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="fe9c7-107">Современная высокопроизводительная упрощенная платформа RPC.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="fe9c7-108">Разработка API по модели "сначала контракт" с использованием механизма Protocol Buffers по умолчанию, что позволяет выпускать не зависящие от языка реализации.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="fe9c7-109">Доступные для многих языков инструменты, предназначенные для создания строго типизированных серверов и клиентов.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="fe9c7-110">Поддержка клиентских, серверных и двунаправленных потоковых вызовов.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="fe9c7-111">Снижение уровня использования сети за счет двоичной сериализации Protobuf.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="fe9c7-112">Благодаря этим преимуществам gRPC идеально подходит для:</span><span class="sxs-lookup"><span data-stu-id="fe9c7-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="fe9c7-113">упрощенных микрослужб, где важна эффективность;</span><span class="sxs-lookup"><span data-stu-id="fe9c7-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="fe9c7-114">многоязычных систем, где для разработки требуется несколько языков;</span><span class="sxs-lookup"><span data-stu-id="fe9c7-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="fe9c7-115">работающих в режиме реального времени служб типа "точка-точка", которые должны обрабатывать запросы и ответы потоковой передачи данных.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="fe9c7-116">Средства C# для работы с файлами с расширением .proto</span><span class="sxs-lookup"><span data-stu-id="fe9c7-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="fe9c7-117">Для разработки API в gRPC используется подход, при котором сначала создается контракт.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="fe9c7-118">Службы и сообщения определяются в файлах *\*.proto* :</span><span class="sxs-lookup"><span data-stu-id="fe9c7-118">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="fe9c7-119">Типы .NET для служб, клиентов и сообщений создаются автоматически, путем добавления файлов *\*.proto* в проект:</span><span class="sxs-lookup"><span data-stu-id="fe9c7-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="fe9c7-120">Добавьте ссылку на пакет [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="fe9c7-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="fe9c7-121">Добавьте файлы *\*.proto* в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="fe9c7-122">Дополнительные сведения о поддержке средств gRPC см. в статье <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="fe9c7-123">Службы gRPC на платформе ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe9c7-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="fe9c7-124">Службы gRPC можно размещать на платформе ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="fe9c7-125">Службы поддерживают полную интеграцию с популярными функциями ASP.NET Core, такими как ведение журнала, внедрение зависимостей (DI), проверка подлинности и авторизация.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="fe9c7-126">В шаблоне проекта gRPC предоставляется базовая служба:</span><span class="sxs-lookup"><span data-stu-id="fe9c7-126">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="fe9c7-127">`GreeterService` является производным от типа `GreeterBase`, который создается из службы `Greeter` в файле *\*.proto* .</span><span class="sxs-lookup"><span data-stu-id="fe9c7-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="fe9c7-128">Служба становится доступной для клиентов в *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="fe9c7-128">The service is made accessible to clients in *Startup.cs* :</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="fe9c7-129">Дополнительные сведения о службах gRPC на ASP.NET Core см. в статье <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="fe9c7-130">Вызов служб gRPC с помощью клиента .NET</span><span class="sxs-lookup"><span data-stu-id="fe9c7-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="fe9c7-131">Клиенты gRPC являются конкретными типами клиентов, [создаваемыми в файлах *\*.proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="fe9c7-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="fe9c7-132">Конкретный клиент gRPC использует методы, которые выполняют преобразование для служб gRPC в файле *\*.proto* .</span><span class="sxs-lookup"><span data-stu-id="fe9c7-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="fe9c7-133">Клиент gRPC создается с помощью канала, который представляет длительное подключение к службе gRPC.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="fe9c7-134">Канал можно создать с помощью `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="fe9c7-135">Дополнительные сведения о создании клиентов и вызове различных методов службы см. в статье <xref:grpc/client>.</span><span class="sxs-lookup"><span data-stu-id="fe9c7-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe9c7-136">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fe9c7-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
