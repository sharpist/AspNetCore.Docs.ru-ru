---
title: Тестирование служб gRPC с помощью gRPCurl в ASP.NET Core
author: jamesnk
description: Узнайте, как тестировать службы с помощью средств gRPC. gRPCurl — программа командной строки для взаимодействия со службами gRPC. gRPCui — интерактивный пользовательский веб-интерфейс.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058796"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="32411-105">Тестирование служб gRPC с помощью gRPCurl в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32411-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="32411-106">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="32411-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="32411-107">Для gRPC доступен инструментарий, позволяющий разработчикам тестировать службы без сборки клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="32411-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="32411-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) — это программа командной строки, которая обеспечивает взаимодействие со службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="32411-109">[gRPCui](https://github.com/fullstorydev/grpcui) создается на основе gRPCurl и имеет интерактивный веб-интерфейс для gRPC, аналогичный таким средствам, как интерфейс Postman и Swagger.</span><span class="sxs-lookup"><span data-stu-id="32411-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="32411-110">В этой статье рассматриваются следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="32411-110">This article discusses how to:</span></span>

* <span data-ttu-id="32411-111">скачивание и установка gRPCurl и gRPCui;</span><span class="sxs-lookup"><span data-stu-id="32411-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="32411-112">настройка отражения gRPC с помощью приложения gRPC ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="32411-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="32411-113">обнаружение и тестирование служб gRPC с помощью `grpcurl`;</span><span class="sxs-lookup"><span data-stu-id="32411-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="32411-114">взаимодействие со службами gRPC в браузере с помощью `grpcui`.</span><span class="sxs-lookup"><span data-stu-id="32411-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="32411-115">Сведения о gRPCurl</span><span class="sxs-lookup"><span data-stu-id="32411-115">About gRPCurl</span></span>

<span data-ttu-id="32411-116">gRPCurl — это программа командной строки, разрабатываемая сообществом gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="32411-117">Она обладает следующими возможностями:</span><span class="sxs-lookup"><span data-stu-id="32411-117">Its features include:</span></span>

* <span data-ttu-id="32411-118">вызов служб gRPC, включая службы потоковой передачи;</span><span class="sxs-lookup"><span data-stu-id="32411-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="32411-119">обнаружение служб с помощью [отражения gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md);</span><span class="sxs-lookup"><span data-stu-id="32411-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="32411-120">формирование списка и описание служб gRPC;</span><span class="sxs-lookup"><span data-stu-id="32411-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="32411-121">работа с безопасными (TLS) и небезопасными (обычными текстовыми) серверами.</span><span class="sxs-lookup"><span data-stu-id="32411-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="32411-122">Сведения о скачивании и установке `grpcurl` см. на [домашней странице gRPCurl в GitHub](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="32411-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![Командная строка gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="32411-124">Настройка отражения gRPC</span><span class="sxs-lookup"><span data-stu-id="32411-124">Set up gRPC reflection</span></span>

<span data-ttu-id="32411-125">`grpcurl` должен быть известен контракт Protobuf служб, чтобы их можно было вызывать.</span><span class="sxs-lookup"><span data-stu-id="32411-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="32411-126">Это можно сделать двумя способами.</span><span class="sxs-lookup"><span data-stu-id="32411-126">There are two ways to do this:</span></span>

* <span data-ttu-id="32411-127">Настройка [отражения gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) на сервере.</span><span class="sxs-lookup"><span data-stu-id="32411-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="32411-128">gRPCurl автоматически обнаруживает контракты служб.</span><span class="sxs-lookup"><span data-stu-id="32411-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="32411-129">Указание файлов `.proto` в аргументах командной строки.</span><span class="sxs-lookup"><span data-stu-id="32411-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="32411-130">gRPCurl проще использовать с отражением gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="32411-131">Отражение gRPC добавляет в приложение новую службу gRPC, которую клиенты могут вызывать для обнаружения служб.</span><span class="sxs-lookup"><span data-stu-id="32411-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="32411-132">В gRPC ASP.NET Core есть встроенная поддержка отражения gRPC, обеспечиваемая пакетом [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection).</span><span class="sxs-lookup"><span data-stu-id="32411-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="32411-133">Чтобы настроить отражение в приложении, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="32411-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="32411-134">Добавьте ссылку на пакет `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="32411-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="32411-135">Зарегистрируйте отражение в файле `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="32411-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="32411-136">`AddGrpcReflection` для регистрации служб, обеспечивающих отражение;</span><span class="sxs-lookup"><span data-stu-id="32411-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="32411-137">`MapGrpcReflectionService` для добавления конечной точки службы отражения.</span><span class="sxs-lookup"><span data-stu-id="32411-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="32411-138">После настройки отражения gRPC:</span><span class="sxs-lookup"><span data-stu-id="32411-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="32411-139">Служба отражения gRPC добавляется в серверное приложение.</span><span class="sxs-lookup"><span data-stu-id="32411-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="32411-140">Клиентские приложения, поддерживающие отражение gRPC, могут вызывать службу отражения для обнаружения служб, размещенных на сервере.</span><span class="sxs-lookup"><span data-stu-id="32411-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="32411-141">Службы gRPC по-прежнему вызываются из клиента.</span><span class="sxs-lookup"><span data-stu-id="32411-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="32411-142">Отражение обеспечивает только обнаружение службы и не выполняет обход безопасности на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="32411-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="32411-143">Для успешного вызова конечных точек, защищенных с помощью [проверки подлинности и авторизации](xref:grpc/authn-and-authz), требуется, чтобы вызывающий объект передавал конечной точке учетные данные.</span><span class="sxs-lookup"><span data-stu-id="32411-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="32411-144">Используйте `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="32411-144">Use `grpcurl`</span></span>

<span data-ttu-id="32411-145">Аргумент `-help` описывает параметры командной строки `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="32411-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="32411-146">Обнаружение служб</span><span class="sxs-lookup"><span data-stu-id="32411-146">Discover services</span></span>

<span data-ttu-id="32411-147">Используйте команду `describe` для просмотра служб, определенных сервером:</span><span class="sxs-lookup"><span data-stu-id="32411-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="32411-148">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="32411-148">The preceding example:</span></span>

* <span data-ttu-id="32411-149">Выполняет команду `describe` на сервере `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="32411-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="32411-150">Выводит службы и методы, возвращаемые отражением gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="32411-151">`Greeter` — это служба, реализуемая приложением.</span><span class="sxs-lookup"><span data-stu-id="32411-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="32411-152">`ServerReflection` — это служба, добавляемая пакетом `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="32411-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="32411-153">Используйте `describe` в сочетании с именем службы, метода или сообщения, чтобы просмотреть сведения о них:</span><span class="sxs-lookup"><span data-stu-id="32411-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="32411-154">Вызов служб gRPC</span><span class="sxs-lookup"><span data-stu-id="32411-154">Call gRPC services</span></span>

<span data-ttu-id="32411-155">Вызовите службу gRPC, указав имена службы и метода вместе с аргументом JSON, который представляет сообщение запроса.</span><span class="sxs-lookup"><span data-stu-id="32411-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="32411-156">Сообщение JSON преобразуется в Protobuf и отправляется службе.</span><span class="sxs-lookup"><span data-stu-id="32411-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="32411-157">В предыдущем примере:</span><span class="sxs-lookup"><span data-stu-id="32411-157">In the preceding example:</span></span>

* <span data-ttu-id="32411-158">Аргумент `-d` задает сообщение запроса с помощью JSON.</span><span class="sxs-lookup"><span data-stu-id="32411-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="32411-159">Этот аргумент должен предшествовать адресу сервера и имени метода.</span><span class="sxs-lookup"><span data-stu-id="32411-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="32411-160">Вызывает метод `SayHello` для службы `greeter.Greeter`.</span><span class="sxs-lookup"><span data-stu-id="32411-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="32411-161">Выводит ответное сообщение в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="32411-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="32411-162">Сведения о gRPCui</span><span class="sxs-lookup"><span data-stu-id="32411-162">About gRPCui</span></span>

<span data-ttu-id="32411-163">gRPCui — это интерактивный пользовательский веб-интерфейс для gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="32411-164">Он основан на gRPCurl и обеспечивает графический пользовательский интерфейс для обнаружения и тестирования служб gRPC, аналогичный таким средствам HTTP, как интерфейс Postman или Swagger.</span><span class="sxs-lookup"><span data-stu-id="32411-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="32411-165">Сведения о скачивании и установке `grpcui` см. на [домашней странице gRPCui в GitHub](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="32411-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="32411-166">Использование `grpcui`</span><span class="sxs-lookup"><span data-stu-id="32411-166">Using `grpcui`</span></span>

<span data-ttu-id="32411-167">Выполните команду `grpcui`, указав адрес нужного сервера в качестве аргумента:</span><span class="sxs-lookup"><span data-stu-id="32411-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="32411-168">Средство откроет окно браузера с интерактивным пользовательским веб-интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="32411-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="32411-169">Службы gRPC обнаруживаются автоматически с помощью отражения gRPC.</span><span class="sxs-lookup"><span data-stu-id="32411-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Пользовательский веб-интерфейс gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="32411-171">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="32411-171">Additional resources</span></span>

* [<span data-ttu-id="32411-172">Домашняя страница gRPCurl в GitHub</span><span class="sxs-lookup"><span data-stu-id="32411-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="32411-173">Домашняя страница gRPCui в GitHub</span><span class="sxs-lookup"><span data-stu-id="32411-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
