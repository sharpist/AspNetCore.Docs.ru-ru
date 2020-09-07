---
title: Создание веб-интерфейсов API JSON из gRPC
author: jamesnk
description: Узнайте, как создавать интерфейсы API JSON HTTP для служб gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102879"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="6e0ac-103">Создание веб-интерфейсов API JSON из gRPC</span><span class="sxs-lookup"><span data-stu-id="6e0ac-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="6e0ac-104">Автор: [Джеймс Ньютон-Кинг (James Newton-King)](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6e0ac-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6e0ac-105">API HTTP gRPC — это экспериментальный проект и пока не является готовым продуктом.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="6e0ac-106">Наши задачи:</span><span class="sxs-lookup"><span data-stu-id="6e0ac-106">We want to:</span></span>
>
> * <span data-ttu-id="6e0ac-107">Проверка, работает ли наш метод создания веб-API JSON для служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="6e0ac-108">Получение отзывов, полезен ли этот метод для разработчиков .NET.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="6e0ac-109">Просим вас [оставлять отзывы](https://github.com/grpc/grpc-dotnet/issues/167), чтобы мы могли понять, создаем ли мы удобный и эффективный продукт для разработчиков.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="6e0ac-110">gRPC — это современный способ обмена данными между приложениями.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="6e0ac-111">gRPC использует HTTP/2, потоковую передачу, Protobuf и контракты сообщений для создания высокопроизводительных служб в реальном времени.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="6e0ac-112">gRPC имеет единственное ограничение — его можно использовать не на каждой платформе.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="6e0ac-113">Браузеры не полностью поддерживают HTTP/2, поэтому REST и JSON являются основными способами передачи данных в браузерные приложения.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="6e0ac-114">Даже при всех преимуществах gRPC REST и JSON играют важную роль в современных приложениях.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="6e0ac-115">Создание веб-API gRPC ***и*** JSON добавляет нежелательные затраты на разработку приложений.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="6e0ac-116">В этом документе рассматривается создание веб-API JSON с помощью служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="6e0ac-117">API gRPC HTTP</span><span class="sxs-lookup"><span data-stu-id="6e0ac-117">gRPC HTTP API</span></span>

<span data-ttu-id="6e0ac-118">API gRPC HTTP — это экспериментальная сборка для ASP.NET Core, которая создает API-интерфейсы RESTful JSON для служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="6e0ac-119">После настройки API gRPC HTTP позволяет приложениям вызывать службы gRPC, используя следующие привычные понятия HTTP:</span><span class="sxs-lookup"><span data-stu-id="6e0ac-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="6e0ac-120">Команды HTTP</span><span class="sxs-lookup"><span data-stu-id="6e0ac-120">HTTP verbs</span></span>
* <span data-ttu-id="6e0ac-121">привязку параметра URL-адреса;</span><span class="sxs-lookup"><span data-stu-id="6e0ac-121">URL parameter binding</span></span>
* <span data-ttu-id="6e0ac-122">запросы и ответы JSON.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-122">JSON requests/responses</span></span>

<span data-ttu-id="6e0ac-123">gRPC по-прежнему можно использовать для вызова служб.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="6e0ac-124">Использование</span><span class="sxs-lookup"><span data-stu-id="6e0ac-124">Usage</span></span>

1. <span data-ttu-id="6e0ac-125">Добавьте ссылку на пакет в [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="6e0ac-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="6e0ac-126">Зарегистрируйте службы в *Startup.cs* с помощью `AddGrpcHttpApi`.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="6e0ac-127">Добавьте в свой проект файлы [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) и [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto).</span><span class="sxs-lookup"><span data-stu-id="6e0ac-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="6e0ac-128">Добавьте заметки в методы gRPC в ваших файлах *PROTO* с маршрутами и привязками HTTP:</span><span class="sxs-lookup"><span data-stu-id="6e0ac-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="6e0ac-129">Теперь метод `SayHello` gRPC можно вызывать как gRPC + Protobuf и как API HTTP:</span><span class="sxs-lookup"><span data-stu-id="6e0ac-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="6e0ac-130">Запрос: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="6e0ac-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="6e0ac-131">Ответ: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="6e0ac-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="6e0ac-132">Серверные журналы показывают, что HTTP-вызов выполняется службой gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="6e0ac-133">API gRPC HTTP сопоставляет входящий HTTP-запрос с сообщением gRPC, а затем преобразует ответное сообщение в JSON.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="6e0ac-134">Ниже приводится простой пример.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-134">This is a basic example.</span></span> <span data-ttu-id="6e0ac-135">Дополнительные возможности настройки см. в разделе [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span><span class="sxs-lookup"><span data-stu-id="6e0ac-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="6e0ac-136">Сравнение API gRPC HTTP и gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="6e0ac-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="6e0ac-137">И API gRPC HTTP, и gRPC-Web позволяют вызывать службы gRPC из браузера.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="6e0ac-138">Однако способы, которыми они это делают, отличаются.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="6e0ac-139">gRPC-Web позволяет браузерным приложениям вызывать службы gRPC из браузера с помощью клиента gRPC-Web и Protobuf.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="6e0ac-140">gRPC-Web требует, чтобы браузерное приложение создавало клиент gRPC, а преимущество заключается в том, что отправляются небольшие и быстрые сообщения Protobuf.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="6e0ac-141">API gRPC HTTP позволяет браузерным приложениям вызывать службы gRPC с помощью JSON, как если бы они были API RESTful.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="6e0ac-142">Браузерному приложению не нужно создавать клиент gRPC и не нужно ничего знать о gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="6e0ac-143">Для API gRPC HTTP не создается сформированный клиент.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="6e0ac-144">Предыдущую службу `Greeter` можно вызывать с помощью API JavaScript браузера:</span><span class="sxs-lookup"><span data-stu-id="6e0ac-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="6e0ac-145">Экспериментальное состояние</span><span class="sxs-lookup"><span data-stu-id="6e0ac-145">Experimental status</span></span>

<span data-ttu-id="6e0ac-146">API gRPC HTTP является экспериментом.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="6e0ac-147">Это решение не завершено и не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="6e0ac-148">Мы заинтересованы в этой технологии, и она предоставляет разработчикам приложений возможность быстро и одновременно создавать службы gRPC и JSON.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="6e0ac-149">Мы не принимаем на себя обязательства по завершению этого проекта API gRPC HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="6e0ac-150">Мы хотим оценить заинтересованность разработчиков в API gRPC HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="6e0ac-151">Если вас заинтересовала технология API gRPC HTTP, просим вас [отправить отзыв](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="6e0ac-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="6e0ac-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="6e0ac-152">grpc-gateway</span></span>

<span data-ttu-id="6e0ac-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) — еще одна технология создания API RESTful JSON из служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="6e0ac-154">Она использует такие же заметки в файлах *PROTO*, чтобы сопоставлять понятия HTTP со службами gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="6e0ac-155">Главное различие между grpc-gateway и API gRPC HTTP состоит в том, что grpc-gateway использует формирование кода для создания обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="6e0ac-156">Обратный прокси-сервер преобразует вызовы RESTful в gRPC, а затем отправляет их в службу gRPC.</span><span class="sxs-lookup"><span data-stu-id="6e0ac-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="6e0ac-157">Сведения об установке и использовании grpc-gateway см. в [документации по grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="6e0ac-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e0ac-158">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6e0ac-158">Additional resources</span></span>

* [<span data-ttu-id="6e0ac-159">Документация по google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="6e0ac-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
