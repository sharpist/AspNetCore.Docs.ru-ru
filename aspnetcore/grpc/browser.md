---
title: Использование gRPC в приложениях на основе браузера
author: jamesnk
description: Узнайте, как настроить службы gRPC на платформе ASP.NET Core для вызова из приложений браузера с помощью gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440770"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="6f158-103">Использование gRPC в приложениях на основе браузера</span><span class="sxs-lookup"><span data-stu-id="6f158-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="6f158-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="6f158-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f158-105">**Поддержка gRPC-Web реализуется в .NET в экспериментальном режиме**</span><span class="sxs-lookup"><span data-stu-id="6f158-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="6f158-106">gRPC-Web для .NET — это экспериментальный проект, а не готовый продукт.</span><span class="sxs-lookup"><span data-stu-id="6f158-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="6f158-107">Наши задачи:</span><span class="sxs-lookup"><span data-stu-id="6f158-107">We want to:</span></span>
>
> * <span data-ttu-id="6f158-108">Проверить, что наш подход к реализации gRPC-Web работает.</span><span class="sxs-lookup"><span data-stu-id="6f158-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="6f158-109">Получить отзыв о том, был ли этот подход полезен разработчикам .NET в сравнении с традиционным способом настройки gRPC-Web через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="6f158-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="6f158-110">Оставьте отзыв на сайте [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet), чтобы мы понимали, что создаем полезный и эффективный продукт для разработчиков.</span><span class="sxs-lookup"><span data-stu-id="6f158-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="6f158-111">Вызвать службу HTTP/2 gRPC из приложения на основе браузера невозможно.</span><span class="sxs-lookup"><span data-stu-id="6f158-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="6f158-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) — это протокол, позволяющий приложениям JavaScript и Blazor на основе браузера вызывать службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f158-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="6f158-113">В этой статье описывается использование gRPC-Web в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f158-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="6f158-114">gRPC-Web в ASP.NET Core или Envoy</span><span class="sxs-lookup"><span data-stu-id="6f158-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="6f158-115">Существует два способа добавления gRPC-Web в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f158-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="6f158-116">Поддержка gRPC-Web вместе с gRPC HTTP/2 в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f158-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="6f158-117">Этот параметр использует ПО промежуточного слоя, предоставленное пакетом `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="6f158-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="6f158-118">Используйте поддержку gRPC-Web в [прокси-сервере Envoy](https://www.envoyproxy.io/), чтобы транслировать gRPC-Web в gRPC HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6f158-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="6f158-119">Затем переведенный вызов перенаправляется в приложение ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f158-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="6f158-120">Есть свои плюсы и минусы этого подхода.</span><span class="sxs-lookup"><span data-stu-id="6f158-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="6f158-121">Если вы уже используете Envoy в качестве прокси-сервера в среде приложения, имеет смысл также использовать его для поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6f158-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="6f158-122">Если требуется простое решение для gRPC-Web, которое требует только ASP.NET Core, выбирайте `Grpc.AspNetCore.Web`.</span><span class="sxs-lookup"><span data-stu-id="6f158-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="6f158-123">Настройка gRPC-Web в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f158-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="6f158-124">Службы gRPC, размещенные в ASP.NET Core, можно настроить на поддержку gRPC-Web вместе с HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f158-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="6f158-125">gRPC-Web не требует вносить изменения в службы.</span><span class="sxs-lookup"><span data-stu-id="6f158-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="6f158-126">Единственного изменения потребует конфигурация запуска.</span><span class="sxs-lookup"><span data-stu-id="6f158-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="6f158-127">Чтобы включить gRPC-Web со службой gRPC ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="6f158-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="6f158-128">Добавьте ссылку на пакет [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web).</span><span class="sxs-lookup"><span data-stu-id="6f158-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="6f158-129">Настройте приложение на использование gRPC-Web, добавив `AddGrpcWeb` и `UseGrpcWeb` в файл *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6f158-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="6f158-130">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6f158-130">The preceding code:</span></span>

* <span data-ttu-id="6f158-131">Добавляет ПО промежуточного слоя gRPC-Web (`UseGrpcWeb`) после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="6f158-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="6f158-132">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой gRPC-Web с `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="6f158-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="6f158-133">Кроме того, можно настроить все службы на поддержку gRPC-Web, добавив `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` в ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="6f158-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="6f158-134">Существует известная ошибка, приводящая к сбою gRPC-Web при [, размещенной в HTTP. sys](xref:fundamentals/servers/httpsys) в .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="6f158-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="6f158-135">Обходной путь для получения gRPC-Web на HTTP. sys доступен [здесь](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="6f158-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="6f158-136">gRPC-Web и CORS</span><span class="sxs-lookup"><span data-stu-id="6f158-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="6f158-137">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="6f158-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6f158-138">Это ограничение применяется к вызовам gRPC-Web с приложениями браузера.</span><span class="sxs-lookup"><span data-stu-id="6f158-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="6f158-139">Например, приложение браузера, обслуживаемое `https://www.contoso.com`, блокирует вызов служб gRPC-Web, размещенных на `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="6f158-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="6f158-140">Можно использовать общий доступ к ресурсам независимо от источника (CORS), чтобы ослабить это ограничение.</span><span class="sxs-lookup"><span data-stu-id="6f158-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="6f158-141">Чтобы разрешить приложению браузера выполнять вызовы gRPC-Web независимо от источника, настройте [CORS в ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="6f158-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="6f158-142">Используйте встроенную поддержку CORS и предоставьте заголовки, относящиеся к gRPC, с помощью <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6f158-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="6f158-143">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6f158-143">The preceding code:</span></span>

* <span data-ttu-id="6f158-144">Вызывает `AddCors` для добавления служб CORS и настраивает политику CORS, которая предоставляет заголовки, относящиеся к gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f158-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="6f158-145">Вызывает `UseCors` для добавления ПО промежуточного слоя CORS после маршрутизации и перед конечными точками.</span><span class="sxs-lookup"><span data-stu-id="6f158-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="6f158-146">Указывает метод `endpoints.MapGrpcService<GreeterService>()` с поддержкой CORS с `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="6f158-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="6f158-147">Вызов gRPC-Web из браузера</span><span class="sxs-lookup"><span data-stu-id="6f158-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="6f158-148">Приложения браузера могут использовать gRPC-Web для вызова служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f158-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="6f158-149">При вызове служб gRPC с помощью gRPC-Web из браузера существует ряд требований и ограничений.</span><span class="sxs-lookup"><span data-stu-id="6f158-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="6f158-150">Сервер должен быть настроен для поддержки gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6f158-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="6f158-151">Потоковая передача клиента и вызовы двунаправленной потоковой передачи не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="6f158-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="6f158-152">Потоковая передача сервера поддерживается.</span><span class="sxs-lookup"><span data-stu-id="6f158-152">Server streaming is supported.</span></span>
* <span data-ttu-id="6f158-153">Для вызова служб gRPC в другом домене требуется настроить [CORS](xref:security/cors) на сервере.</span><span class="sxs-lookup"><span data-stu-id="6f158-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="6f158-154">Клиент gRPC-Web JavaScript</span><span class="sxs-lookup"><span data-stu-id="6f158-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="6f158-155">Существует клиент gRPC-Web JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6f158-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="6f158-156">Инструкции по использованию gRPC-Web из JavaScript см. в статье, посвященной [написанию кода клиента JavaScript с gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="6f158-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="6f158-157">Настройка gRPC-Web с помощью клиента .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="6f158-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="6f158-158">Клиент .NET gRPC можно настроить для выполнения вызовов gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6f158-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="6f158-159">Это полезно для приложений [Blazor WebAssembly](xref:blazor/index#blazor-webassembly), которые размещаются в браузере и имеют те же ограничения HTTP, что и код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6f158-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="6f158-160">Вызов gRPC-Web с помощью клиента .NET выполняется [так же, как и HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="6f158-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="6f158-161">Единственным изменением является то, как создается канал.</span><span class="sxs-lookup"><span data-stu-id="6f158-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="6f158-162">Чтобы использовать gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="6f158-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="6f158-163">Добавьте ссылку на пакет [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web).</span><span class="sxs-lookup"><span data-stu-id="6f158-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="6f158-164">Убедитесь, что ссылаетесь на пакет [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) версии 2.27.0 или более поздней.</span><span class="sxs-lookup"><span data-stu-id="6f158-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="6f158-165">Настройте канал на использование `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="6f158-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="6f158-166">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6f158-166">The preceding code:</span></span>

* <span data-ttu-id="6f158-167">Настраивает канал для использования gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="6f158-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="6f158-168">Создает клиент и выполняет вызов с помощью канала.</span><span class="sxs-lookup"><span data-stu-id="6f158-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="6f158-169">При создании `GrpcWebHandler` имеет следующие параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="6f158-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="6f158-170">**InnerHandler**: базовый <xref:System.Net.Http.HttpMessageHandler>, который выполняет HTTP-запрос gRPC, например `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="6f158-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="6f158-171">**Режим**. Тип перечисления, указывающий, является ли HTTP-запрос gRPC `Content-Type` `application/grpc-web` или `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="6f158-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="6f158-172">`GrpcWebMode.GrpcWeb` настраивает содержимое для отправки без кодировки.</span><span class="sxs-lookup"><span data-stu-id="6f158-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="6f158-173">Значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6f158-173">Default value.</span></span>
    * <span data-ttu-id="6f158-174">`GrpcWebMode.GrpcWebText` настраивает содержимое в кодировке Base64.</span><span class="sxs-lookup"><span data-stu-id="6f158-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="6f158-175">Требуется для вызовов потоковой передачи сервера в браузерах.</span><span class="sxs-lookup"><span data-stu-id="6f158-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="6f158-176">**HttpVersion**: `Version` протокола HTTP, используемая для задания [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) в базовом HTTP-запросе gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f158-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="6f158-177">gRPC-Web не требует определенной версии и не переопределяет значение по умолчанию, если не указано иное.</span><span class="sxs-lookup"><span data-stu-id="6f158-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6f158-178">Созданные клиенты gRPC имеют синхронные и асинхронные методы для вызова унарных методов.</span><span class="sxs-lookup"><span data-stu-id="6f158-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="6f158-179">Например, `SayHello` является синхронным, а `SayHelloAsync` — асинхронным.</span><span class="sxs-lookup"><span data-stu-id="6f158-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="6f158-180">Вызов синхронного метода в приложении Blazor WebAssembly приведет к тому, что приложение перестанет отвечать на запросы.</span><span class="sxs-lookup"><span data-stu-id="6f158-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="6f158-181">В Blazor WebAssembly всегда следует использовать асинхронные методы.</span><span class="sxs-lookup"><span data-stu-id="6f158-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f158-182">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6f158-182">Additional resources</span></span>

* [<span data-ttu-id="6f158-183">Проект GitHub — gRPC для веб-клиентов</span><span class="sxs-lookup"><span data-stu-id="6f158-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
