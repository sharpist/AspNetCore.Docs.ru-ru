---
title: Использование gRPC в приложениях на основе браузера
author: jamesnk
description: Узнайте, как настроить службы gRPC на платформе ASP.NET Core для вызова из приложений браузера с помощью gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 6f66a94b41e6e13550396e2e19fdf48f9dc63d46
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106602"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="6ad79-103">Использование gRPC в приложениях на основе браузера</span><span class="sxs-lookup"><span data-stu-id="6ad79-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="6ad79-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="6ad79-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6ad79-105">Вызвать службу HTTP/2 gRPC из приложения на основе браузера невозможно.</span><span class="sxs-lookup"><span data-stu-id="6ad79-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="6ad79-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) — это протокол, позволяющий приложениям JavaScript и Blazor на основе браузера вызывать службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ad79-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="6ad79-107">В этой статье описывается использование gRPC-Web в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ad79-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="6ad79-108">gRPC-Web в ASP.NET Core или Envoy</span><span class="sxs-lookup"><span data-stu-id="6ad79-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="6ad79-109">Существует два способа добавления gRPC-Web в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ad79-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="6ad79-110">Поддержка gRPC-Web вместе с gRPC HTTP/2 в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ad79-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="6ad79-111">Этот параметр использует ПО промежуточного слоя, предоставленное пакетом `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="6ad79-112">Используйте поддержку gRPC-Web в [прокси-сервере Envoy](https://www.envoyproxy.io/), чтобы транслировать gRPC-Web в gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6ad79-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="6ad79-113">Затем переведенный вызов перенаправляется в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ad79-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="6ad79-114">Есть свои плюсы и минусы этого подхода.</span><span class="sxs-lookup"><span data-stu-id="6ad79-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="6ad79-115">Если вы уже используете Envoy в качестве прокси-сервера в среде приложения, имеет смысл также использовать его для поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6ad79-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="6ad79-116">Если требуется простое решение для gRPC-Web, которое требует только ASP.NET Core, выбирайте `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="6ad79-117">Настройка gRPC-Web в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ad79-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="6ad79-118">Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ad79-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="6ad79-119">gRPC-Web не требует вносить изменения в службы.</span><span class="sxs-lookup"><span data-stu-id="6ad79-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="6ad79-120">Единственного изменения потребует конфигурация запуска.</span><span class="sxs-lookup"><span data-stu-id="6ad79-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="6ad79-121">Чтобы включить gRPC-Web со службой gRPC ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="6ad79-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="6ad79-122">Добавьте ссылку на пакет [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).</span><span class="sxs-lookup"><span data-stu-id="6ad79-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="6ad79-123">Настройте приложение на использование gRPC-Web, добавив `UseGrpcWeb` и `EnableGrpcWeb` в файл *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6ad79-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="6ad79-124">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6ad79-124">The preceding code:</span></span>

* <span data-ttu-id="6ad79-125">Добавляет ПО промежуточного слоя gRPC-Web (`UseGrpcWeb`) после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="6ad79-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="6ad79-126">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой gRPC-Web с `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="6ad79-127">Также можно настроить ПО промежуточного слоя gRPC-Web, чтобы все службы поддерживали gRPC-Web по умолчанию и не нужно было использовать `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="6ad79-128">Укажите `new GrpcWebOptions { DefaultEnabled = true }` при добавлении ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6ad79-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="6ad79-129">Существует известная ошибка, приводящая к сбою gRPC-Web при [, размещенной в HTTP. sys](xref:fundamentals/servers/httpsys) в .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="6ad79-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="6ad79-130">Обходной путь для получения gRPC-Web на HTTP. sys доступен [здесь](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="6ad79-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="6ad79-131">gRPC-Web и CORS</span><span class="sxs-lookup"><span data-stu-id="6ad79-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="6ad79-132">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="6ad79-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6ad79-133">Это ограничение применяется к вызовам gRPC-Web с приложениями браузера.</span><span class="sxs-lookup"><span data-stu-id="6ad79-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="6ad79-134">Например, приложение браузера, обслуживаемое `https://www.contoso.com`, блокирует вызов служб gRPC-Web, размещенных на `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="6ad79-135">Можно использовать общий доступ к ресурсам независимо от источника (CORS), чтобы ослабить это ограничение.</span><span class="sxs-lookup"><span data-stu-id="6ad79-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="6ad79-136">Чтобы разрешить приложению браузера выполнять вызовы gRPC-Web независимо от источника, настройте [CORS в ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="6ad79-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="6ad79-137">Используйте встроенную поддержку CORS и предоставьте заголовки, относящиеся к gRPC, с помощью <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6ad79-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="6ad79-138">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6ad79-138">The preceding code:</span></span>

* <span data-ttu-id="6ad79-139">Вызывает `AddCors` для добавления служб CORS и настраивает политику CORS, которая предоставляет заголовки, относящиеся к gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ad79-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="6ad79-140">Вызывает `UseCors` для добавления ПО промежуточного слоя CORS после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="6ad79-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="6ad79-141">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой CORS с `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="6ad79-142">Вызов gRPC-Web из браузера</span><span class="sxs-lookup"><span data-stu-id="6ad79-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="6ad79-143">Приложения браузера могут использовать gRPC-Web для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ad79-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="6ad79-144">При вызове служб gRPC с помощью gRPC-Web из браузера существует ряд требований и ограничений.</span><span class="sxs-lookup"><span data-stu-id="6ad79-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="6ad79-145">Сервер должен быть настроен для поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6ad79-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="6ad79-146">Потоковая передача клиента и вызовы двунаправленной потоковой передачи не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="6ad79-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="6ad79-147">Потоковая передача сервера поддерживается.</span><span class="sxs-lookup"><span data-stu-id="6ad79-147">Server streaming is supported.</span></span>
* <span data-ttu-id="6ad79-148">Для вызова служб gRPC в другом домене требуется настроить [CORS](xref:security/cors) на сервере.</span><span class="sxs-lookup"><span data-stu-id="6ad79-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="6ad79-149">Клиент gRPC-Web JavaScript</span><span class="sxs-lookup"><span data-stu-id="6ad79-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="6ad79-150">Существует клиент gRPC-Web JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ad79-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="6ad79-151">Инструкции по использованию gRPC-Web из JavaScript см. в статье, посвященной [написанию кода клиента JavaScript с gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="6ad79-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="6ad79-152">Настройка gRPC-Web с помощью клиента .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="6ad79-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="6ad79-153">Клиент .NET gRPC можно настроить для выполнения вызовов gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6ad79-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="6ad79-154">Это полезно для приложений [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), которые размещаются в браузере и имеют те же ограничения HTTP, что и код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6ad79-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="6ad79-155">Вызов gRPC-Web с помощью клиента .NET выполняется [так же, как и HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="6ad79-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="6ad79-156">Единственным изменением является то, как создается канал.</span><span class="sxs-lookup"><span data-stu-id="6ad79-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="6ad79-157">Чтобы использовать gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="6ad79-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="6ad79-158">Добавьте ссылку на пакет [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).</span><span class="sxs-lookup"><span data-stu-id="6ad79-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="6ad79-159">Убедитесь, что используется ссылка на пакет [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.29.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="6ad79-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="6ad79-160">Настройте канал на использование `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="6ad79-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="6ad79-161">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6ad79-161">The preceding code:</span></span>

* <span data-ttu-id="6ad79-162">Настраивает канал для использования gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6ad79-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="6ad79-163">Создает клиент и выполняет вызов с помощью канала.</span><span class="sxs-lookup"><span data-stu-id="6ad79-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="6ad79-164">`GrpcWebHandler` имеет следующие параметры конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6ad79-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="6ad79-165">**InnerHandler**: базовый <xref:System.Net.Http.HttpMessageHandler>, который выполняет HTTP-запрос gRPC, например `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="6ad79-166">**GrpcWebMode**: Тип перечисления, указывающий, является ли HTTP-запрос gRPC `Content-Type` `application/grpc-web` или `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="6ad79-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="6ad79-167">`GrpcWebMode.GrpcWeb` настраивает содержимое для отправки без кодировки.</span><span class="sxs-lookup"><span data-stu-id="6ad79-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="6ad79-168">Значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ad79-168">Default value.</span></span>
    * <span data-ttu-id="6ad79-169">`GrpcWebMode.GrpcWebText` настраивает содержимое в кодировке Base64.</span><span class="sxs-lookup"><span data-stu-id="6ad79-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="6ad79-170">Требуется для вызовов потоковой передачи сервера в браузерах.</span><span class="sxs-lookup"><span data-stu-id="6ad79-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="6ad79-171">**HttpVersion**: `Version` протокола HTTP, используемая для задания [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) в базовом HTTP-запросе gRPC.</span><span class="sxs-lookup"><span data-stu-id="6ad79-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="6ad79-172">gRPC-Web не требует определенной версии и не переопределяет значение по умолчанию, если не указано иное.</span><span class="sxs-lookup"><span data-stu-id="6ad79-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6ad79-173">Созданные клиенты gRPC имеют синхронные и асинхронные методы для вызова унарных методов.</span><span class="sxs-lookup"><span data-stu-id="6ad79-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="6ad79-174">Например, `SayHello` является синхронным, а `SayHelloAsync` — асинхронным.</span><span class="sxs-lookup"><span data-stu-id="6ad79-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="6ad79-175">Вызов синхронного метода в приложении Blazor WebAssembly приведет к тому, что приложение перестанет отвечать на запросы.</span><span class="sxs-lookup"><span data-stu-id="6ad79-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="6ad79-176">В Blazor WebAssembly всегда следует использовать асинхронные методы.</span><span class="sxs-lookup"><span data-stu-id="6ad79-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ad79-177">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6ad79-177">Additional resources</span></span>

* [<span data-ttu-id="6ad79-178">Проект GitHub — gRPC для веб-клиентов</span><span class="sxs-lookup"><span data-stu-id="6ad79-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
