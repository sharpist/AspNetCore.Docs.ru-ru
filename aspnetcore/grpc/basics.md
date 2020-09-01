---
title: Службы gRPC на языке C#
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: aacaf9cca131d3fba1c3ae96cf42d51d3fdc17b6
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945432"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="b9938-103">Службы gRPC на языке C\#</span><span class="sxs-lookup"><span data-stu-id="b9938-103">gRPC services with C\#</span></span>

<span data-ttu-id="b9938-104">Этот документ описывает понятия, необходимые для написания приложений [gRPC](https://grpc.io/docs/guides/) на C#.</span><span class="sxs-lookup"><span data-stu-id="b9938-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="b9938-105">Рассматриваемые здесь темы применимы к приложениям gRPC как на основе [C-core](https://grpc.io/blog/grpc-stacks), так и на основе ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9938-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a><span data-ttu-id="b9938-106">Файл PROTO</span><span class="sxs-lookup"><span data-stu-id="b9938-106">proto file</span></span>

<span data-ttu-id="b9938-107">Для разработки API в gRPC используется подход, при котором сначала создается контракт.</span><span class="sxs-lookup"><span data-stu-id="b9938-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="b9938-108">Буферы протоколов (protobuf) по умолчанию используются в качестве языка IDL.</span><span class="sxs-lookup"><span data-stu-id="b9938-108">Protocol buffers (protobuf) are used as the Interface Definition Language (IDL) by default.</span></span> <span data-ttu-id="b9938-109">Файл *\*.proto* содержит следующее:</span><span class="sxs-lookup"><span data-stu-id="b9938-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="b9938-110">Определение службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9938-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="b9938-111">Сообщения, передаваемые между клиентами и серверами.</span><span class="sxs-lookup"><span data-stu-id="b9938-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="b9938-112">Дополнительные сведения о синтаксисе файлов protobuf см. в разделе <xref:grpc/protobuf>.</span><span class="sxs-lookup"><span data-stu-id="b9938-112">For more information on the syntax of protobuf files, see <xref:grpc/protobuf>.</span></span>

<span data-ttu-id="b9938-113">Например, рассмотрим файл *greet.proto*, используемый в разделе о [начале работы со службами gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="b9938-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="b9938-114">Определяет службу `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="b9938-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="b9938-115">Служба `Greeter` определяет вызов `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="b9938-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="b9938-116">`SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`:</span><span class="sxs-lookup"><span data-stu-id="b9938-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="b9938-117">Добавление файла PROTO в приложение C\#</span><span class="sxs-lookup"><span data-stu-id="b9938-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="b9938-118">Файл *\*.proto* включается в проект путем его добавления в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="b9938-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="b9938-119">По умолчанию ссылка на `<Protobuf>` создает конкретный клиент и базовый класс службы.</span><span class="sxs-lookup"><span data-stu-id="b9938-119">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="b9938-120">С помощью атрибута `GrpcServices` элемента ссылки можно ограничить создание ресурсов на C#.</span><span class="sxs-lookup"><span data-stu-id="b9938-120">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="b9938-121">Допустимые значения `GrpcServices`:</span><span class="sxs-lookup"><span data-stu-id="b9938-121">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="b9938-122">`Both` (по умолчанию, если значение не задано)</span><span class="sxs-lookup"><span data-stu-id="b9938-122">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="b9938-123">Средства C# для работы с файлами с расширением .proto</span><span class="sxs-lookup"><span data-stu-id="b9938-123">C# Tooling support for .proto files</span></span>

<span data-ttu-id="b9938-124">Для создания ресурсов C# из *\*.proto* требуется пакет инструментов [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="b9938-124">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="b9938-125">Создаваемые ресурсы (файлы):</span><span class="sxs-lookup"><span data-stu-id="b9938-125">The generated assets (files):</span></span>

* <span data-ttu-id="b9938-126">создаются по мере необходимости при каждой сборке проекта;</span><span class="sxs-lookup"><span data-stu-id="b9938-126">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="b9938-127">не добавляются в проект или не возвращаются в систему управления версиями;</span><span class="sxs-lookup"><span data-stu-id="b9938-127">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="b9938-128">представляют собой артефакт сборки, находящийся в каталоге *obj*.</span><span class="sxs-lookup"><span data-stu-id="b9938-128">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="b9938-129">Этот пакет требуется как для серверных, так и для клиентских проектов.</span><span class="sxs-lookup"><span data-stu-id="b9938-129">This package is required by both the server and client projects.</span></span> <span data-ttu-id="b9938-130">Метапакет `Grpc.AspNetCore` содержит ссылку на `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="b9938-130">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="b9938-131">Серверные проекты могут добавлять `Grpc.AspNetCore` с помощью диспетчера пакетов в Visual Studio или путем добавления `<PackageReference>` в файл проекта:</span><span class="sxs-lookup"><span data-stu-id="b9938-131">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="b9938-132">Клиентские проекты должны непосредственно ссылаться на `Grpc.Tools` вместе с другими пакетами, необходимыми для использования клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9938-132">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="b9938-133">Этот пакет инструментов не требуется во время выполнения, поэтому зависимость помечается как `PrivateAssets="All"`:</span><span class="sxs-lookup"><span data-stu-id="b9938-133">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="b9938-134">Создаваемые ресурсы C#</span><span class="sxs-lookup"><span data-stu-id="b9938-134">Generated C# assets</span></span>

<span data-ttu-id="b9938-135">Пакет инструментов создает типы C#, которые представляют сообщения, определенные во включаемых файлах *\*.proto*.</span><span class="sxs-lookup"><span data-stu-id="b9938-135">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="b9938-136">Для ресурсов на стороне сервера создается абстрактный базовый тип службы.</span><span class="sxs-lookup"><span data-stu-id="b9938-136">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="b9938-137">Этот базовый тип содержит определения всех вызовов gRPC, содержащихся в файле *.proto*.</span><span class="sxs-lookup"><span data-stu-id="b9938-137">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="b9938-138">Создайте конкретную реализацию службы, производную от этого базового типа, и реализуйте логику для вызовов gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9938-138">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="b9938-139">Для `greet.proto` в описанном выше примере создается абстрактный тип `GreeterBase`, содержащий виртуальный метод `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="b9938-139">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="b9938-140">Конкретная реализация `GreeterService` переопределяет метод и реализует логику, обрабатывающую вызов gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9938-140">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="b9938-141">Для клиентских ресурсов создается конкретный тип клиента.</span><span class="sxs-lookup"><span data-stu-id="b9938-141">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="b9938-142">Вызовы gRPC в файле *.proto* преобразуются в методы конкретного типа, которые могут быть вызваны.</span><span class="sxs-lookup"><span data-stu-id="b9938-142">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="b9938-143">Для `greet.proto` в описанном выше примере создается конкретный тип `GreeterClient`.</span><span class="sxs-lookup"><span data-stu-id="b9938-143">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="b9938-144">Вызовите `GreeterClient.SayHelloAsync`, чтобы инициировать вызов gRPC на сервере.</span><span class="sxs-lookup"><span data-stu-id="b9938-144">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="b9938-145">По умолчанию серверные и клиентские ресурсы создаются для каждого файла *\*.proto*, включаемого в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="b9938-145">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="b9938-146">Чтобы гарантировать, что в серверном проекте создаются только серверные ресурсы, атрибуту `GrpcServices` присваивается значение `Server`.</span><span class="sxs-lookup"><span data-stu-id="b9938-146">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="b9938-147">Аналогичным образом атрибуту присваивается значение `Client` в клиентских проектах.</span><span class="sxs-lookup"><span data-stu-id="b9938-147">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9938-148">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b9938-148">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
