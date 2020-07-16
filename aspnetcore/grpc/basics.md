---
title: Службы gRPC на языке C#
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a99e5c78e291469bdce5c2b5cfa6eec86caa9735
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176298"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="642b3-103">Службы gRPC на языке C\#</span><span class="sxs-lookup"><span data-stu-id="642b3-103">gRPC services with C\#</span></span>

<span data-ttu-id="642b3-104">Этот документ описывает понятия, необходимые для написания приложений [gRPC](https://grpc.io/docs/guides/) на C#.</span><span class="sxs-lookup"><span data-stu-id="642b3-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="642b3-105">Рассматриваемые здесь темы применимы к приложениям gRPC как на основе [C-core](https://grpc.io/blog/grpc-stacks), так и на основе ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="642b3-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="642b3-106">Файл PROTO</span><span class="sxs-lookup"><span data-stu-id="642b3-106">proto file</span></span>

<span data-ttu-id="642b3-107">Для разработки API в gRPC используется подход, при котором сначала создается контракт.</span><span class="sxs-lookup"><span data-stu-id="642b3-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="642b3-108">Буферы протоколов (protobuf) по умолчанию используются в качестве языка проектирования интерфейса (IDL).</span><span class="sxs-lookup"><span data-stu-id="642b3-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="642b3-109">Файл *\*.proto* содержит следующее:</span><span class="sxs-lookup"><span data-stu-id="642b3-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="642b3-110">Определение службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="642b3-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="642b3-111">Сообщения, передаваемые между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="642b3-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="642b3-112">Дополнительные сведения о синтаксисе файлов protobuf см. в [официальной документации (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="642b3-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="642b3-113">Например, рассмотрим файл *greet.proto*, используемый в разделе о [начале работы со службами gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="642b3-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="642b3-114">Определяет службу `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="642b3-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="642b3-115">Служба `Greeter` определяет вызов `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="642b3-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="642b3-116">`SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`:</span><span class="sxs-lookup"><span data-stu-id="642b3-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="642b3-117">Добавление файла PROTO в приложение C\#</span><span class="sxs-lookup"><span data-stu-id="642b3-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="642b3-118">Файл *\*.proto* включается в проект путем его добавления в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="642b3-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="642b3-119">По умолчанию ссылка на `<Protobuf>` создает конкретный клиент и базовый класс службы.</span><span class="sxs-lookup"><span data-stu-id="642b3-119">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="642b3-120">С помощью атрибута `GrpcServices` элемента ссылки можно ограничить создание ресурсов на C#.</span><span class="sxs-lookup"><span data-stu-id="642b3-120">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="642b3-121">Допустимые значения `GrpcServices`:</span><span class="sxs-lookup"><span data-stu-id="642b3-121">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="642b3-122">`Both` (по умолчанию, если значение не задано)</span><span class="sxs-lookup"><span data-stu-id="642b3-122">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="642b3-123">Средства C# для работы с файлами с расширением .proto</span><span class="sxs-lookup"><span data-stu-id="642b3-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="642b3-124">Для создания ресурсов C# из *\*.proto* требуется пакет инструментов [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="642b3-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="642b3-125">Создаваемые ресурсы (файлы):</span><span class="sxs-lookup"><span data-stu-id="642b3-125">The generated assets (files):</span></span>

* <span data-ttu-id="642b3-126">создаются по мере необходимости при каждой сборке проекта;</span><span class="sxs-lookup"><span data-stu-id="642b3-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="642b3-127">не добавляются в проект или не возвращаются в систему управления версиями;</span><span class="sxs-lookup"><span data-stu-id="642b3-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="642b3-128">представляют собой артефакт сборки, находящийся в каталоге *obj*.</span><span class="sxs-lookup"><span data-stu-id="642b3-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="642b3-129">Этот пакет требуется как для серверных, так и для клиентских проектов.</span><span class="sxs-lookup"><span data-stu-id="642b3-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="642b3-130">Метапакет `Grpc.AspNetCore` содержит ссылку на `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="642b3-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="642b3-131">Серверные проекты могут добавлять `Grpc.AspNetCore` с помощью диспетчера пакетов в Visual Studio или путем добавления `<PackageReference>` в файл проекта:</span><span class="sxs-lookup"><span data-stu-id="642b3-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="642b3-132">Клиентские проекты должны непосредственно ссылаться на `Grpc.Tools` вместе с другими пакетами, необходимыми для использования клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="642b3-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="642b3-133">Этот пакет инструментов не требуется во время выполнения, поэтому зависимость помечается как `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="642b3-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="642b3-134">Создаваемые ресурсы C#</span><span class="sxs-lookup"><span data-stu-id="642b3-134">Generated C# assets</span></span>

<span data-ttu-id="642b3-135">Пакет инструментов создает типы C#, которые представляют сообщения, определенные во включаемых файлах *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="642b3-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="642b3-136">Для ресурсов на стороне сервера создается абстрактный базовый тип службы.</span><span class="sxs-lookup"><span data-stu-id="642b3-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="642b3-137">Этот базовый тип содержит определения всех вызовов gRPC, содержащихся в файле *.proto*.</span><span class="sxs-lookup"><span data-stu-id="642b3-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="642b3-138">Создайте конкретную реализацию службы, производную от этого базового типа, и реализуйте логику для вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="642b3-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="642b3-139">Для `greet.proto` в описанном выше примере создается абстрактный тип `GreeterBase`, содержащий виртуальный метод `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="642b3-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="642b3-140">Конкретная реализация `GreeterService` переопределяет метод и реализует логику, обрабатывающую вызов gRPC.</span><span class="sxs-lookup"><span data-stu-id="642b3-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="642b3-141">Для клиентских ресурсов создается конкретный тип клиента.</span><span class="sxs-lookup"><span data-stu-id="642b3-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="642b3-142">Вызовы gRPC в файле *.proto* преобразуются в методы конкретного типа, которые могут быть вызваны.</span><span class="sxs-lookup"><span data-stu-id="642b3-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="642b3-143">Для `greet.proto` в описанном выше примере создается конкретный тип `GreeterClient`.</span><span class="sxs-lookup"><span data-stu-id="642b3-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="642b3-144">Вызовите `GreeterClient.SayHelloAsync`, чтобы инициировать вызов gRPC на сервере.</span><span class="sxs-lookup"><span data-stu-id="642b3-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="642b3-145">По умолчанию серверные и клиентские ресурсы создаются для каждого файла *\*.proto*, включаемого в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="642b3-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="642b3-146">Чтобы гарантировать, что в серверном проекте создаются только серверные ресурсы, атрибуту `GrpcServices` присваивается значение `Server`.</span><span class="sxs-lookup"><span data-stu-id="642b3-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="642b3-147">Аналогичным образом атрибуту присваивается значение `Client` в клиентских проектах.</span><span class="sxs-lookup"><span data-stu-id="642b3-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="642b3-148">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="642b3-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
