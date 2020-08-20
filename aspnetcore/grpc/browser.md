---
title: Использование gRPC в приложениях на основе браузера
author: jamesnk
description: Узнайте, как настроить службы gRPC на платформе ASP.NET Core для вызова из приложений браузера с помощью gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 8d1f761731ab3840d009eba1ff5316808bafec40
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634414"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="fba09-103">Использование gRPC в приложениях на основе браузера</span><span class="sxs-lookup"><span data-stu-id="fba09-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="fba09-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="fba09-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="fba09-105">Узнайте, как настроить существующую службу ASP.NET Core gRPC для вызовов из приложений браузера с использованием протокола [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md).</span><span class="sxs-lookup"><span data-stu-id="fba09-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="fba09-106">gRPC-Web позволяет приложениям JavaScript и Blazor на основе браузера вызывать службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="fba09-107">Вызвать службу HTTP/2 gRPC из приложения на основе браузера нельзя.</span><span class="sxs-lookup"><span data-stu-id="fba09-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="fba09-108">Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="fba09-109">См. раздел [Добавление служб gRPC в приложение ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="fba09-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="fba09-110">Инструкции по созданию проекта gRPC см. здесь: <xref:tutorials/grpc/grpc-start>.</span><span class="sxs-lookup"><span data-stu-id="fba09-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="fba09-111">gRPC-Web в ASP.NET Core или Envoy</span><span class="sxs-lookup"><span data-stu-id="fba09-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="fba09-112">Существует два способа добавления gRPC-Web в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fba09-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="fba09-113">Поддержка gRPC-Web вместе с gRPC HTTP/2 в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fba09-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="fba09-114">Этот параметр использует ПО промежуточного слоя, предоставленное пакетом `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="fba09-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="fba09-115">Используйте поддержку gRPC-Web в [прокси-сервере Envoy](https://www.envoyproxy.io/), чтобы транслировать gRPC-Web в gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fba09-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="fba09-116">Затем переведенный вызов перенаправляется в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fba09-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="fba09-117">Есть свои плюсы и минусы этого подхода.</span><span class="sxs-lookup"><span data-stu-id="fba09-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="fba09-118">Если среда приложения уже использует Envoy в качестве прокси-сервера, имеет смысл использовать Envoy и для предоставления поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fba09-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="fba09-119">Если требуется простое решение для gRPC-Web, для которого требуется только ASP.NET Core, используйте `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="fba09-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="fba09-120">Настройка gRPC-Web в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fba09-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="fba09-121">Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="fba09-122">gRPC-Web не требует вносить изменения в службы.</span><span class="sxs-lookup"><span data-stu-id="fba09-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="fba09-123">Единственного изменения потребует конфигурация запуска.</span><span class="sxs-lookup"><span data-stu-id="fba09-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="fba09-124">Чтобы включить gRPC-Web со службой gRPC ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="fba09-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="fba09-125">Добавьте ссылку на пакет [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).</span><span class="sxs-lookup"><span data-stu-id="fba09-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="fba09-126">Настройте приложение на использование gRPC-Web, добавив `UseGrpcWeb` и `EnableGrpcWeb` в файл *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fba09-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="fba09-127">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="fba09-127">The preceding code:</span></span>

* <span data-ttu-id="fba09-128">Добавляет ПО промежуточного слоя gRPC-Web (`UseGrpcWeb`) после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="fba09-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="fba09-129">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой gRPC-Web с `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="fba09-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="fba09-130">Также можно настроить ПО промежуточного слоя gRPC-Web, чтобы все службы поддерживали gRPC-Web по умолчанию и не нужно было использовать `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="fba09-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="fba09-131">Укажите `new GrpcWebOptions { DefaultEnabled = true }` при добавлении ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fba09-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="fba09-132">Существует известная ошибка, приводящая к сбою gRPC-Web при [, размещенной в HTTP. sys](xref:fundamentals/servers/httpsys) в .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="fba09-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="fba09-133">Обходной путь для получения gRPC-Web на HTTP. sys доступен [здесь](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="fba09-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="fba09-134">gRPC-Web и CORS</span><span class="sxs-lookup"><span data-stu-id="fba09-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="fba09-135">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="fba09-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="fba09-136">Это ограничение применяется к вызовам gRPC-Web с приложениями браузера.</span><span class="sxs-lookup"><span data-stu-id="fba09-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="fba09-137">Например, приложение браузера, обслуживаемое `https://www.contoso.com`, блокирует вызов служб gRPC-Web, размещенных на `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="fba09-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="fba09-138">Можно использовать общий доступ к ресурсам независимо от источника (CORS), чтобы ослабить это ограничение.</span><span class="sxs-lookup"><span data-stu-id="fba09-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="fba09-139">Чтобы разрешить приложению браузера вызывать gRPC-Web независимо от источника, настройте [CORS в ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="fba09-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="fba09-140">Используйте встроенную поддержку CORS и предоставьте заголовки, относящиеся к gRPC, с помощью <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span><span class="sxs-lookup"><span data-stu-id="fba09-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="fba09-141">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="fba09-141">The preceding code:</span></span>

* <span data-ttu-id="fba09-142">Вызывает `AddCors` для добавления служб CORS и настраивает политику CORS, которая предоставляет заголовки, относящиеся к gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="fba09-143">Вызывает `UseCors` для добавления ПО промежуточного слоя CORS после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="fba09-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="fba09-144">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой CORS с `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="fba09-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="fba09-145">gRPC-Web и потоковая передача</span><span class="sxs-lookup"><span data-stu-id="fba09-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="fba09-146">Традиционный API gRPC по HTTP/2 поддерживает потоковую передачу во всех направлениях.</span><span class="sxs-lookup"><span data-stu-id="fba09-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="fba09-147">gRPC-Web имеет ограниченную поддержку потоковой передачи:</span><span class="sxs-lookup"><span data-stu-id="fba09-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="fba09-148">Клиенты браузера с gRPC-Web не поддерживают вызов методов потоковой передачи клиента и двунаправленной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="fba09-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="fba09-149">Службы gRPC в ASP.NET Core, размещенные в Службе приложений Azure и IIS, не поддерживают двунаправленную потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="fba09-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="fba09-150">При использовании gRPC-Web мы рекомендуем применять только унарные методы и методы серверной потоковой передачи.</span><span class="sxs-lookup"><span data-stu-id="fba09-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="fba09-151">Вызов gRPC-Web из браузера</span><span class="sxs-lookup"><span data-stu-id="fba09-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="fba09-152">Приложения браузера могут использовать gRPC-Web для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="fba09-153">При вызове служб gRPC с помощью gRPC-Web из браузера существует ряд требований и ограничений.</span><span class="sxs-lookup"><span data-stu-id="fba09-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="fba09-154">Сервер должен быть настроен для поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fba09-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="fba09-155">Потоковая передача клиента и вызовы двунаправленной потоковой передачи не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="fba09-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="fba09-156">Потоковая передача сервера поддерживается.</span><span class="sxs-lookup"><span data-stu-id="fba09-156">Server streaming is supported.</span></span>
* <span data-ttu-id="fba09-157">Для вызова служб gRPC в другом домене требуется настроить [CORS](xref:security/cors) на сервере.</span><span class="sxs-lookup"><span data-stu-id="fba09-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="fba09-158">Клиент gRPC-Web JavaScript</span><span class="sxs-lookup"><span data-stu-id="fba09-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="fba09-159">Существует клиент gRPC-Web JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fba09-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="fba09-160">Инструкции по использованию gRPC-Web из JavaScript см. в статье, посвященной [написанию кода клиента JavaScript с gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="fba09-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="fba09-161">Настройка gRPC-Web с помощью клиента .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="fba09-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="fba09-162">Клиент .NET gRPC можно настроить для выполнения вызовов gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fba09-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="fba09-163">Это полезно для приложений [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), которые размещаются в браузере и имеют те же ограничения HTTP, что и код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fba09-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="fba09-164">Вызов gRPC-Web с помощью клиента .NET выполняется [так же, как и HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="fba09-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="fba09-165">Единственным изменением является то, как создается канал.</span><span class="sxs-lookup"><span data-stu-id="fba09-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="fba09-166">Чтобы использовать gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="fba09-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="fba09-167">Добавьте ссылку на пакет [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).</span><span class="sxs-lookup"><span data-stu-id="fba09-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="fba09-168">Убедитесь, что используется ссылка на пакет [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.29.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="fba09-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="fba09-169">Настройте канал на использование `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="fba09-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="fba09-170">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="fba09-170">The preceding code:</span></span>

* <span data-ttu-id="fba09-171">Настраивает канал для использования gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="fba09-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="fba09-172">Создает клиент и выполняет вызов с помощью канала.</span><span class="sxs-lookup"><span data-stu-id="fba09-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="fba09-173">`GrpcWebHandler` имеет следующие параметры конфигурации.</span><span class="sxs-lookup"><span data-stu-id="fba09-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="fba09-174">**InnerHandler**: базовый <xref:System.Net.Http.HttpMessageHandler>, который выполняет HTTP-запрос gRPC, например `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="fba09-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="fba09-175">**GrpcWebMode**: Тип перечисления, указывающий, является ли HTTP-запрос gRPC `Content-Type` `application/grpc-web` или `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="fba09-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="fba09-176">`GrpcWebMode.GrpcWeb` настраивает содержимое для отправки без кодировки.</span><span class="sxs-lookup"><span data-stu-id="fba09-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="fba09-177">Значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fba09-177">Default value.</span></span>
    * <span data-ttu-id="fba09-178">`GrpcWebMode.GrpcWebText` настраивает содержимое в кодировке Base64.</span><span class="sxs-lookup"><span data-stu-id="fba09-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="fba09-179">Требуется для вызовов потоковой передачи сервера в браузерах.</span><span class="sxs-lookup"><span data-stu-id="fba09-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="fba09-180">**HttpVersion**: `Version` протокола HTTP, используемая для задания [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) в базовом HTTP-запросе gRPC.</span><span class="sxs-lookup"><span data-stu-id="fba09-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="fba09-181">gRPC-Web не требует определенной версии и не переопределяет значение по умолчанию, если не указано иное.</span><span class="sxs-lookup"><span data-stu-id="fba09-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fba09-182">Созданные клиенты gRPC имеют синхронные и асинхронные методы для вызова унарных методов.</span><span class="sxs-lookup"><span data-stu-id="fba09-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="fba09-183">Например, `SayHello` является синхронным, а `SayHelloAsync` — асинхронным.</span><span class="sxs-lookup"><span data-stu-id="fba09-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="fba09-184">Вызов синхронного метода в приложении Blazor WebAssembly приведет к тому, что приложение перестанет отвечать на запросы.</span><span class="sxs-lookup"><span data-stu-id="fba09-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="fba09-185">В Blazor WebAssembly всегда следует использовать асинхронные методы.</span><span class="sxs-lookup"><span data-stu-id="fba09-185">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fba09-186">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fba09-186">Additional resources</span></span>

* [<span data-ttu-id="fba09-187">Проект GitHub — gRPC для веб-клиентов</span><span class="sxs-lookup"><span data-stu-id="fba09-187">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
