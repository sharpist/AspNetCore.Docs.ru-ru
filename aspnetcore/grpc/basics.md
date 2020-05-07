---
title: Службы gRPC на языке C#
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774760"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="88ce2-103">Службы gRPC на языке C\#</span><span class="sxs-lookup"><span data-stu-id="88ce2-103">gRPC services with C\#</span></span>

<span data-ttu-id="88ce2-104">Этот документ описывает понятия, необходимые для написания приложений [gRPC](https://grpc.io/docs/guides/) на C#.</span><span class="sxs-lookup"><span data-stu-id="88ce2-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="88ce2-105">Рассматриваемые здесь темы применимы к приложениям gRPC как на основе [C-core](https://grpc.io/blog/grpc-stacks), так и на основе ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="88ce2-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="88ce2-106">Файл PROTO</span><span class="sxs-lookup"><span data-stu-id="88ce2-106">proto file</span></span>

<span data-ttu-id="88ce2-107">Для разработки API в gRPC используется подход, при котором сначала создается контракт.</span><span class="sxs-lookup"><span data-stu-id="88ce2-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="88ce2-108">Буферы протоколов (protobuf) по умолчанию используются в качестве языка проектирования интерфейса (IDL).</span><span class="sxs-lookup"><span data-stu-id="88ce2-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="88ce2-109">Файл *\*.proto* содержит следующее:</span><span class="sxs-lookup"><span data-stu-id="88ce2-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="88ce2-110">Определение службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="88ce2-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="88ce2-111">Сообщения, передаваемые между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="88ce2-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="88ce2-112">Дополнительные сведения о синтаксисе файлов protobuf см. в [официальной документации (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="88ce2-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="88ce2-113">Например, рассмотрим файл *greet.proto*, используемый в разделе о [начале работы со службами gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="88ce2-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="88ce2-114">Определяет службу `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="88ce2-115">Служба `Greeter` определяет вызов `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="88ce2-116">`SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`:</span><span class="sxs-lookup"><span data-stu-id="88ce2-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="88ce2-117">Добавление файла PROTO в приложение C\#</span><span class="sxs-lookup"><span data-stu-id="88ce2-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="88ce2-118">Файл *\*.proto* включается в проект путем его добавления в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="88ce2-119">Средства C# для работы с файлами с расширением .proto</span><span class="sxs-lookup"><span data-stu-id="88ce2-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="88ce2-120">Для создания ресурсов C# из *\*.proto* требуется пакет инструментов [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="88ce2-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="88ce2-121">Создаваемые ресурсы (файлы):</span><span class="sxs-lookup"><span data-stu-id="88ce2-121">The generated assets (files):</span></span>

* <span data-ttu-id="88ce2-122">создаются по мере необходимости при каждой сборке проекта;</span><span class="sxs-lookup"><span data-stu-id="88ce2-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="88ce2-123">не добавляются в проект или не возвращаются в систему управления версиями;</span><span class="sxs-lookup"><span data-stu-id="88ce2-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="88ce2-124">представляют собой артефакт сборки, находящийся в каталоге *obj*.</span><span class="sxs-lookup"><span data-stu-id="88ce2-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="88ce2-125">Этот пакет требуется как для серверных, так и для клиентских проектов.</span><span class="sxs-lookup"><span data-stu-id="88ce2-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="88ce2-126">Метапакет `Grpc.AspNetCore` содержит ссылку на `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="88ce2-127">Серверные проекты могут добавлять `Grpc.AspNetCore` с помощью диспетчера пакетов в Visual Studio или путем добавления `<PackageReference>` в файл проекта:</span><span class="sxs-lookup"><span data-stu-id="88ce2-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="88ce2-128">Клиентские проекты должны непосредственно ссылаться на `Grpc.Tools` вместе с другими пакетами, необходимыми для использования клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="88ce2-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="88ce2-129">Этот пакет инструментов не требуется во время выполнения, поэтому зависимость помечается как `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="88ce2-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="88ce2-130">Создаваемые ресурсы C#</span><span class="sxs-lookup"><span data-stu-id="88ce2-130">Generated C# assets</span></span>

<span data-ttu-id="88ce2-131">Пакет инструментов создает типы C#, которые представляют сообщения, определенные во включаемых файлах *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="88ce2-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="88ce2-132">Для ресурсов на стороне сервера создается абстрактный базовый тип службы.</span><span class="sxs-lookup"><span data-stu-id="88ce2-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="88ce2-133">Этот базовый тип содержит определения всех вызовов gRPC, содержащихся в файле *.proto*.</span><span class="sxs-lookup"><span data-stu-id="88ce2-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="88ce2-134">Создайте конкретную реализацию службы, производную от этого базового типа, и реализуйте логику для вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="88ce2-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="88ce2-135">Для `greet.proto` в описанном выше примере создается абстрактный тип `GreeterBase`, содержащий виртуальный метод `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="88ce2-136">Конкретная реализация `GreeterService` переопределяет метод и реализует логику, обрабатывающую вызов gRPC.</span><span class="sxs-lookup"><span data-stu-id="88ce2-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="88ce2-137">Для клиентских ресурсов создается конкретный тип клиента.</span><span class="sxs-lookup"><span data-stu-id="88ce2-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="88ce2-138">Вызовы gRPC в файле *.proto* преобразуются в методы конкретного типа, которые могут быть вызваны.</span><span class="sxs-lookup"><span data-stu-id="88ce2-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="88ce2-139">Для `greet.proto` в описанном выше примере создается конкретный тип `GreeterClient`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="88ce2-140">Вызовите `GreeterClient.SayHelloAsync`, чтобы инициировать вызов gRPC на сервере.</span><span class="sxs-lookup"><span data-stu-id="88ce2-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="88ce2-141">По умолчанию серверные и клиентские ресурсы создаются для каждого файла *\*.proto*, включаемого в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="88ce2-142">Чтобы гарантировать, что в серверном проекте создаются только серверные ресурсы, атрибуту `GrpcServices` присваивается значение `Server`.</span><span class="sxs-lookup"><span data-stu-id="88ce2-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="88ce2-143">Аналогичным образом атрибуту присваивается значение `Client` в клиентских проектах.</span><span class="sxs-lookup"><span data-stu-id="88ce2-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="88ce2-144">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="88ce2-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
