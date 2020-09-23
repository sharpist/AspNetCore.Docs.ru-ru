---
title: Службы тестирования со средствами gRPC
author: jamesnk
description: Узнайте, как тестировать службы с помощью средств gRPC. gRPCurl — программа командной строки для взаимодействия со службами gRPC. gRPCui — интерактивный пользовательский веб-интерфейс.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594376"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="92e34-105">Службы тестирования со средствами gRPC</span><span class="sxs-lookup"><span data-stu-id="92e34-105">Test services with gRPC tools</span></span>

<span data-ttu-id="92e34-106">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="92e34-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="92e34-107">Для gRPC доступен инструментарий, позволяющий разработчикам тестировать службы без сборки клиентских приложений.</span><span class="sxs-lookup"><span data-stu-id="92e34-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="92e34-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) — это программа командной строки, которая обеспечивает взаимодействие со службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="92e34-109">[gRPCui](https://github.com/fullstorydev/grpcui) предоставляет интерактивный пользовательский веб-интерфейс для gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="92e34-110">В этой статье рассматриваются следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="92e34-110">This article discusses how to:</span></span>

* <span data-ttu-id="92e34-111">скачивание и установка gRPCurl и gRPCui;</span><span class="sxs-lookup"><span data-stu-id="92e34-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="92e34-112">настройка отражения gRPC с помощью приложения gRPC ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="92e34-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="92e34-113">обнаружение и тестирование служб gRPC с помощью `grpcurl`;</span><span class="sxs-lookup"><span data-stu-id="92e34-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="92e34-114">взаимодействие со службами gRPC в браузере с помощью `grpcui`.</span><span class="sxs-lookup"><span data-stu-id="92e34-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="92e34-115">Сведения о gRPCurl</span><span class="sxs-lookup"><span data-stu-id="92e34-115">About gRPCurl</span></span>

<span data-ttu-id="92e34-116">gRPCurl — это программа командной строки, разрабатываемая сообществом gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="92e34-117">Она обладает следующими возможностями:</span><span class="sxs-lookup"><span data-stu-id="92e34-117">Its features include:</span></span>

* <span data-ttu-id="92e34-118">вызов служб gRPC, включая службы потоковой передачи;</span><span class="sxs-lookup"><span data-stu-id="92e34-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="92e34-119">обнаружение служб с помощью [отражения gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md);</span><span class="sxs-lookup"><span data-stu-id="92e34-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="92e34-120">формирование списка и описание служб gRPC;</span><span class="sxs-lookup"><span data-stu-id="92e34-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="92e34-121">работа с безопасными (TLS) и небезопасными (обычными текстовыми) серверами.</span><span class="sxs-lookup"><span data-stu-id="92e34-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="92e34-122">Сведения о скачивании и установке `grpcurl` см. на [домашней странице gRPCurl в GitHub](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="92e34-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="92e34-123">Настройка отражения gRPC</span><span class="sxs-lookup"><span data-stu-id="92e34-123">Setup gRPC reflection</span></span>

<span data-ttu-id="92e34-124">`grpcurl` должен быть известен контракт Protobuf служб, чтобы их можно было вызывать.</span><span class="sxs-lookup"><span data-stu-id="92e34-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="92e34-125">Это можно сделать двумя способами.</span><span class="sxs-lookup"><span data-stu-id="92e34-125">There are two ways to do this:</span></span>

* <span data-ttu-id="92e34-126">Используйте отражение gRPC для обнаружения контрактов служб.</span><span class="sxs-lookup"><span data-stu-id="92e34-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="92e34-127">Укажите файлы *PROTO* в аргументах командной строки.</span><span class="sxs-lookup"><span data-stu-id="92e34-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="92e34-128">gRPCurl проще использовать с отражением gRPC и обнаружением служб.</span><span class="sxs-lookup"><span data-stu-id="92e34-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="92e34-129">В gRPC ASP.NET Core есть встроенная поддержка отражения gRPC, обеспечиваемая пакетом [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection).</span><span class="sxs-lookup"><span data-stu-id="92e34-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="92e34-130">Чтобы настроить отражение в приложении, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="92e34-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="92e34-131">Добавьте ссылку на пакет `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="92e34-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="92e34-132">Зарегистрируйте отражение в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="92e34-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="92e34-133">`AddGrpcReflection` для регистрации служб, обеспечивающих отражение;</span><span class="sxs-lookup"><span data-stu-id="92e34-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="92e34-134">`MapGrpcReflectionService` для добавления конечной точки службы отражения.</span><span class="sxs-lookup"><span data-stu-id="92e34-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="92e34-135">Используйте `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="92e34-135">Use `grpcurl`</span></span>

<span data-ttu-id="92e34-136">Аргумент `-help` описывает параметры командной строки `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="92e34-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="92e34-137">Обнаружение служб</span><span class="sxs-lookup"><span data-stu-id="92e34-137">Discover services</span></span>

<span data-ttu-id="92e34-138">Используйте команду `describe` для просмотра служб, определенных сервером:</span><span class="sxs-lookup"><span data-stu-id="92e34-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="92e34-139">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="92e34-139">The preceding example:</span></span>

* <span data-ttu-id="92e34-140">Выполняет команду `describe` на сервере `localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="92e34-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="92e34-141">Выводит службы и методы, возвращаемые отражением gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="92e34-142">`Greeter` — это служба, реализуемая приложением.</span><span class="sxs-lookup"><span data-stu-id="92e34-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="92e34-143">`ServerReflection` — это служба, добавляемая пакетом `Grpc.AspNetCore.Server.Reflection`.</span><span class="sxs-lookup"><span data-stu-id="92e34-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="92e34-144">Используйте `describe` в сочетании с именем службы, метода или сообщения, чтобы просмотреть сведения о них:</span><span class="sxs-lookup"><span data-stu-id="92e34-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="92e34-145">Вызов служб gRPC</span><span class="sxs-lookup"><span data-stu-id="92e34-145">Call gRPC services</span></span>

<span data-ttu-id="92e34-146">Вызовите службу gRPC, указав имена службы и метода вместе с аргументом JSON, который представляет сообщение запроса.</span><span class="sxs-lookup"><span data-stu-id="92e34-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="92e34-147">Сообщение JSON преобразуется в Protobuf и отправляется службе.</span><span class="sxs-lookup"><span data-stu-id="92e34-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="92e34-148">Предшествующий пример:</span><span class="sxs-lookup"><span data-stu-id="92e34-148">The preceding example:</span></span>

* <span data-ttu-id="92e34-149">Аргумент `-d` задает сообщение запроса с помощью JSON.</span><span class="sxs-lookup"><span data-stu-id="92e34-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="92e34-150">Этот аргумент должен предшествовать адресу сервера и имени метода.</span><span class="sxs-lookup"><span data-stu-id="92e34-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="92e34-151">Вызывает метод `SayHello` для службы `greeter.Greeter`.</span><span class="sxs-lookup"><span data-stu-id="92e34-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="92e34-152">Выводит ответное сообщение в формате JSON.</span><span class="sxs-lookup"><span data-stu-id="92e34-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="92e34-153">Сведения о gRPCui</span><span class="sxs-lookup"><span data-stu-id="92e34-153">About gRPCui</span></span>

<span data-ttu-id="92e34-154">gRPCui — это интерактивный пользовательский веб-интерфейс для gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="92e34-155">Он основан на gRPCurl и обеспечивает графический пользовательский интерфейс для обнаружения и тестирования служб gRPC, аналогичный таким средствам HTTP, как Postman.</span><span class="sxs-lookup"><span data-stu-id="92e34-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="92e34-156">Сведения о скачивании и установке `grpcui` см. на [домашней странице gRPCui в GitHub](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="92e34-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="92e34-157">Использование `grpcui`</span><span class="sxs-lookup"><span data-stu-id="92e34-157">Using `grpcui`</span></span>

<span data-ttu-id="92e34-158">Выполните команду `grpcui`, указав адрес нужного сервера в качестве аргумента.</span><span class="sxs-lookup"><span data-stu-id="92e34-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="92e34-159">Средство откроет окно браузера с интерактивным пользовательским веб-интерфейсом.</span><span class="sxs-lookup"><span data-stu-id="92e34-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="92e34-160">Службы gRPC обнаруживаются автоматически с помощью отражения gRPC.</span><span class="sxs-lookup"><span data-stu-id="92e34-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![Пользовательский веб-интерфейс gRPCui](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="92e34-162">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="92e34-162">Additional resources</span></span>

* [<span data-ttu-id="92e34-163">Домашняя страница gRPCurl в GitHub</span><span class="sxs-lookup"><span data-stu-id="92e34-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="92e34-164">Домашняя страница gRPCui в GitHub</span><span class="sxs-lookup"><span data-stu-id="92e34-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="92e34-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="92e34-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
