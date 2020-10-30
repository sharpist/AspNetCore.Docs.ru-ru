---
title: Включение запросов между источниками (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать CORS в качестве стандарта для разрешения или отклонения запросов между источниками в ASP.NET Core приложении.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060824"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="16230-103">Включение запросов между источниками (CORS) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16230-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="16230-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="16230-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="16230-105">В этой статье показано, как включить CORS в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="16230-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="16230-106">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="16230-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="16230-107">Это ограничение называется *политика одного источника* .</span><span class="sxs-lookup"><span data-stu-id="16230-107">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="16230-108">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="16230-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="16230-109">Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении.</span><span class="sxs-lookup"><span data-stu-id="16230-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="16230-110">Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="16230-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="16230-111">[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="16230-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="16230-112">— Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="16230-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="16230-113">**Не** является функцией безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="16230-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="16230-114">Интерфейс API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="16230-115">Дополнительные сведения см. в разделе [как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="16230-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="16230-116">Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.</span><span class="sxs-lookup"><span data-stu-id="16230-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="16230-117">Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="16230-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="16230-118">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16230-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="16230-119">Тот же источник</span><span class="sxs-lookup"><span data-stu-id="16230-119">Same origin</span></span>

<span data-ttu-id="16230-120">Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="16230-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="16230-121">Эти два URL-адреса имеют один и тот же источник:</span><span class="sxs-lookup"><span data-stu-id="16230-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="16230-122">Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="16230-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="16230-123">`https://example.net`: Другой домен</span><span class="sxs-lookup"><span data-stu-id="16230-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="16230-124">`https://www.example.com/foo.html`: Другой поддомен</span><span class="sxs-lookup"><span data-stu-id="16230-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="16230-125">`http://example.com/foo.html`: Другая схема</span><span class="sxs-lookup"><span data-stu-id="16230-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="16230-126">`https://example.com:9000/foo.html`: Другой порт</span><span class="sxs-lookup"><span data-stu-id="16230-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="16230-127">Включение CORS</span><span class="sxs-lookup"><span data-stu-id="16230-127">Enable CORS</span></span>

<span data-ttu-id="16230-128">Включить CORS можно тремя способами:</span><span class="sxs-lookup"><span data-stu-id="16230-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="16230-129">По промежуточного слоя с использованием [именованной политики](#np) или [политики по умолчанию](#dp).</span><span class="sxs-lookup"><span data-stu-id="16230-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="16230-130">Использование [маршрутизации конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="16230-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="16230-131">С атрибутом [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="16230-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="16230-132">Использование атрибута [[EnableCors]](#attr) с именованной политикой предоставляет элемент управления Finest в ограничении конечных точек, поддерживающих CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="16230-133"><xref:Owin.CorsExtensions.UseCors%2A> должен вызываться перед <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> использованием `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="16230-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="16230-134">Каждый подход подробно описан в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="16230-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="16230-135">CORS с именованной политикой и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="16230-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="16230-136">По промежуточного слоя CORS обрабатывает запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="16230-137">Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными источниками:</span><span class="sxs-lookup"><span data-stu-id="16230-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="16230-138">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="16230-138">The preceding code:</span></span>

* <span data-ttu-id="16230-139">Задает имя политики `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="16230-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="16230-140">Имя политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="16230-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="16230-141">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и задает  `_myAllowSpecificOrigins` политику CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="16230-142">`UseCors` добавляет по промежуточного слоя CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="16230-143">Вызов `UseCors` должен быть помещен после `UseRouting` , но до `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="16230-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="16230-144">Дополнительные сведения см. в разделе [порядок по промежуточного слоя](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="16230-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="16230-145">Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="16230-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="16230-146">Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект.</span><span class="sxs-lookup"><span data-stu-id="16230-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="16230-147">[Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="16230-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="16230-148">Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера.</span><span class="sxs-lookup"><span data-stu-id="16230-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="16230-149">Чтобы применить политику CORS к конкретным конечным точкам, см. раздел [Маршрутизация конечных](#ecors) точек.</span><span class="sxs-lookup"><span data-stu-id="16230-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="16230-150">При использовании по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware)вызовите метод <xref:Owin.CorsExtensions.UseCors%2A> перед <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="16230-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="16230-151">При маршрутизации конечных точек по промежуточного слоя CORS **должно** быть настроено для выполнения между вызовами функций `UseRouting` и `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="16230-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="16230-152">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="16230-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="16230-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:</span><span class="sxs-lookup"><span data-stu-id="16230-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="16230-154">Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="16230-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="16230-155"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Методы могут быть объединены в цепочку, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="16230-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="16230-156">Примечание. указанный URL-адрес **не** должен содержать косую черту ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="16230-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="16230-157">Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.</span><span class="sxs-lookup"><span data-stu-id="16230-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="16230-158">CORS с политикой по умолчанию и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="16230-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="16230-159">Следующий выделенный код включает политику CORS по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="16230-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="16230-160">Приведенный выше код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.</span><span class="sxs-lookup"><span data-stu-id="16230-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="16230-161">Включение CORS с маршрутизацией конечных точек</span><span class="sxs-lookup"><span data-stu-id="16230-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="16230-162">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf).</span><span class="sxs-lookup"><span data-stu-id="16230-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="16230-163">Дополнительные сведения см. в описании [этой проблемы GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тестировании CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="16230-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="16230-164">При маршрутизации конечных точек CORS можно включить для каждой конечной точки с помощью <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> набора методов расширения:</span><span class="sxs-lookup"><span data-stu-id="16230-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="16230-165">В предыдущем коде:</span><span class="sxs-lookup"><span data-stu-id="16230-165">In the preceding code:</span></span>

* <span data-ttu-id="16230-166">`app.UseCors` включает по промежуточного слоя CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="16230-167">Так как политика по умолчанию не настроена, `app.UseCors()` она не включает CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="16230-168">`/echo`Конечные точки контроллера и позволяют выполнять запросы между источниками с помощью указанной политики.</span><span class="sxs-lookup"><span data-stu-id="16230-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="16230-169">`/echo2` :::no-loc(Razor)::: Конечные точки страниц и **не** позволяют выполнять запросы независимо от источника, так как не указана политика по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="16230-169">The `/echo2` and :::no-loc(Razor)::: Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="16230-170">Атрибут [[дисаблекорс]](#dc) **не**  отключает CORS, который был включен с помощью маршрутизации конечной точки с помощью `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="16230-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="16230-171">Инструкции по проверке кода, аналогичные приведенным выше, см. в разделе [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="16230-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="16230-172">Включение CORS с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="16230-172">Enable CORS with attributes</span></span>

<span data-ttu-id="16230-173">Включение CORS с помощью атрибута [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение именованной политики только к тем конечным точкам, для которых требуется CORS, предоставляет элемент управления Finest.</span><span class="sxs-lookup"><span data-stu-id="16230-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="16230-174">Атрибут [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="16230-175">`[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не для всех конечных точек:</span><span class="sxs-lookup"><span data-stu-id="16230-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="16230-176">`[EnableCors]` Задает политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="16230-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="16230-177">`[EnableCors("{Policy String}")]` Задает именованную политику.</span><span class="sxs-lookup"><span data-stu-id="16230-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="16230-178">`[EnableCors]`Атрибут может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="16230-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="16230-179">:::no-loc(Razor)::: Страниц `PageModel`</span><span class="sxs-lookup"><span data-stu-id="16230-179">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="16230-180">Контроллер</span><span class="sxs-lookup"><span data-stu-id="16230-180">Controller</span></span>
* <span data-ttu-id="16230-181">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="16230-181">Controller action method</span></span>

<span data-ttu-id="16230-182">К контроллерам, моделям страниц или методам действий можно применять различные политики с `[EnableCors]` атрибутом.</span><span class="sxs-lookup"><span data-stu-id="16230-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="16230-183">Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики.</span><span class="sxs-lookup"><span data-stu-id="16230-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="16230-184">**Мы советуем использовать сочетание политик. Используйте** `[EnableCors]` **атрибут или по промежуточного слоя, а не оба в одном приложении.**</span><span class="sxs-lookup"><span data-stu-id="16230-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="16230-185">Следующий код применяет к каждому методу другую политику:</span><span class="sxs-lookup"><span data-stu-id="16230-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="16230-186">Следующий код создает две политики CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="16230-187">Для Finest управления ограничением запросов CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="16230-188">Используйте `[EnableCors("MyPolicy")]` с именованной политикой.</span><span class="sxs-lookup"><span data-stu-id="16230-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="16230-189">Не определяйте политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="16230-189">Don't define a default policy.</span></span>
* <span data-ttu-id="16230-190">Не используйте [маршрутизацию конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="16230-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="16230-191">Код в следующем разделе соответствует приведенному выше списку.</span><span class="sxs-lookup"><span data-stu-id="16230-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="16230-192">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="16230-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="16230-193">Отключение CORS</span><span class="sxs-lookup"><span data-stu-id="16230-193">Disable CORS</span></span>

<span data-ttu-id="16230-194">Атрибут [[дисаблекорс]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **не**  отключает CORS, который был включен с помощью [маршрутизации конечной точки](#ecors).</span><span class="sxs-lookup"><span data-stu-id="16230-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="16230-195">Следующий код определяет политику CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="16230-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="16230-196">Следующий код отключает CORS для `GetValues2` действия:</span><span class="sxs-lookup"><span data-stu-id="16230-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="16230-197">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="16230-197">The preceding code:</span></span>

* <span data-ttu-id="16230-198">Не включает CORS с [маршрутизацией конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="16230-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="16230-199">Не определяет [политику CORS по умолчанию](#dp).</span><span class="sxs-lookup"><span data-stu-id="16230-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="16230-200">Использует [[EnableCors ("MyPolicy")]](#attr) , чтобы включить `"MyPolicy"` политику CORS для контроллера.</span><span class="sxs-lookup"><span data-stu-id="16230-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="16230-201">Отключает CORS для `GetValues2` метода.</span><span class="sxs-lookup"><span data-stu-id="16230-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="16230-202">Инструкции по тестированию приведенного выше кода см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="16230-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="16230-203">Параметры политики CORS</span><span class="sxs-lookup"><span data-stu-id="16230-203">CORS policy options</span></span>

<span data-ttu-id="16230-204">В этом разделе описаны различные параметры, которые можно задать в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="16230-205">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="16230-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="16230-206">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="16230-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="16230-207">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="16230-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="16230-208">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="16230-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="16230-209">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="16230-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="16230-210">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="16230-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="16230-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> метод вызывается в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="16230-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16230-212">Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".</span><span class="sxs-lookup"><span data-stu-id="16230-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="16230-213">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="16230-213">Set the allowed origins</span></span>

<span data-ttu-id="16230-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ).</span><span class="sxs-lookup"><span data-stu-id="16230-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="16230-215">`AllowAnyOrigin` не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.</span><span class="sxs-lookup"><span data-stu-id="16230-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="16230-216">Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="16230-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="16230-217">Служба CORS возвращает недопустимый ответ CORS, если приложение настроено с обоими методами.</span><span class="sxs-lookup"><span data-stu-id="16230-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="16230-218">`AllowAnyOrigin` влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок.</span><span class="sxs-lookup"><span data-stu-id="16230-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="16230-219">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="16230-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.</span><span class="sxs-lookup"><span data-stu-id="16230-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="16230-221">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="16230-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="16230-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="16230-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="16230-223">Разрешает любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="16230-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="16230-224">Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок.</span><span class="sxs-lookup"><span data-stu-id="16230-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="16230-225">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="16230-226">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="16230-226">Set the allowed request headers</span></span>

<span data-ttu-id="16230-227">Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый [заголовком запроса на создание](https://xhr.spec.whatwg.org/#request), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="16230-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="16230-228">Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="16230-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="16230-229">`AllowAnyHeader` влияет на предпечатные запросы и заголовок [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="16230-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="16230-230">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="16230-231">Политика по промежуточного слоя CORS соответствует определенным заголовкам, указанным в параметре, `WithHeaders` только если отправляемые заголовки в `Access-Control-Request-Headers` точности совпадают с заголовками, указанными в `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="16230-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="16230-232">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="16230-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="16230-233">По промежуточного слоя CORS отклоняет Предпечатный запрос со следующим заголовком запроса, так как `Content-Language` ([Хеадернамес. контентлангуаже](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) нет в списке в `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="16230-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="16230-234">Приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="16230-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="16230-235">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="16230-236">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="16230-236">Set the exposed response headers</span></span>

<span data-ttu-id="16230-237">По умолчанию браузер не предоставляет все заголовки ответа приложению.</span><span class="sxs-lookup"><span data-stu-id="16230-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="16230-238">Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="16230-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="16230-239">По умолчанию доступны заголовки ответов:</span><span class="sxs-lookup"><span data-stu-id="16230-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="16230-240">Спецификация CORS вызывает эти заголовки *простых заголовков ответа* .</span><span class="sxs-lookup"><span data-stu-id="16230-240">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="16230-241">Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="16230-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="16230-242">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="16230-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="16230-243">Учетные данные требует специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="16230-244">По умолчанию браузер не отправляет учетные данные с запросом между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="16230-245">Учетные данные включают :::no-loc(cookie)::: схемы проверки подлинности s и HTTP.</span><span class="sxs-lookup"><span data-stu-id="16230-245">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="16230-246">Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .</span><span class="sxs-lookup"><span data-stu-id="16230-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="16230-247">Использование `XMLHttpRequest` напрямую:</span><span class="sxs-lookup"><span data-stu-id="16230-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="16230-248">Использование jQuery:</span><span class="sxs-lookup"><span data-stu-id="16230-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="16230-249">Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="16230-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="16230-250">Сервер должен разрешать учетные данные.</span><span class="sxs-lookup"><span data-stu-id="16230-250">The server must allow the credentials.</span></span> <span data-ttu-id="16230-251">Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="16230-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="16230-252">HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="16230-253">Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="16230-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="16230-254">Разрешение учетных данных между источниками является угрозой безопасности.</span><span class="sxs-lookup"><span data-stu-id="16230-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="16230-255">Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="16230-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="16230-256">В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="16230-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="16230-257">Предпечатные запросы</span><span class="sxs-lookup"><span data-stu-id="16230-257">Preflight requests</span></span>

<span data-ttu-id="16230-258">Для некоторых запросов CORS браузер отправляет запрос на дополнительные [Параметры](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) перед выполнением фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="16230-259">Этот запрос называется [предпечатным запросом](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="16230-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="16230-260">Браузер может пропустить Предпечатный запрос, если выполняются **все** перечисленные ниже условия.</span><span class="sxs-lookup"><span data-stu-id="16230-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="16230-261">Метод запроса — GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="16230-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="16230-262">Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="16230-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="16230-263">`Content-Type`Заголовок, если он задан, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="16230-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="16230-264">Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта.</span><span class="sxs-lookup"><span data-stu-id="16230-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="16230-265">Спецификация CORS вызывает заголовки [запроса автора](https://www.w3.org/TR/cors/#author-request-headers)заголовков.</span><span class="sxs-lookup"><span data-stu-id="16230-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="16230-266">Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="16230-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="16230-267">Ниже приведен пример ответа, аналогичного предпечатному запросу, выполненному с помощью кнопки **[размещение теста]** в разделе [Test CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="16230-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="16230-268">Предпечатный запрос использует метод [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="16230-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="16230-269">Он может включать следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="16230-269">It may include the following headers:</span></span>

* <span data-ttu-id="16230-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)— метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="16230-271">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers). список заголовков запросов, которые приложение устанавливает на основе фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="16230-272">Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="16230-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="16230-273">Access-Control — Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="16230-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="16230-274">Если Предпечатный запрос отклонен, приложение возвращает `200 OK` ответ, но не задает заголовки CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="16230-275">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="16230-276">Пример запрещенного запроса на предпечатную версию см. в разделе " [тестирование CORS](#testc) " этого документа.</span><span class="sxs-lookup"><span data-stu-id="16230-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="16230-277">С помощью средств F12 консольное приложение отображает ошибку, аналогичную одной из следующих в зависимости от браузера.</span><span class="sxs-lookup"><span data-stu-id="16230-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="16230-278">Firefox: запрос на перекрестное происхождение заблокирован: одна и та же политика происхождения запрещает чтение удаленного ресурса в `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="16230-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="16230-279">(Причина: запрос CORS не выполнен).</span><span class="sxs-lookup"><span data-stu-id="16230-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="16230-280">Подробнее</span><span class="sxs-lookup"><span data-stu-id="16230-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="16230-281">На основе Chromium: доступ к выборке в " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " из источника " https://cors3.azurewebsites.net " заблокирован политикой CORS: ответ на предварительный запрос не проходит проверку контроля доступа: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="16230-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="16230-282">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="16230-283">Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="16230-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="16230-284">Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="16230-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="16230-285">Браузеры не согласуются с тем, как они заданы `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="16230-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="16230-286">Если:</span><span class="sxs-lookup"><span data-stu-id="16230-286">If either:</span></span>

* <span data-ttu-id="16230-287">Для заголовков заданы любые значения, отличные от `"*"`</span><span class="sxs-lookup"><span data-stu-id="16230-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="16230-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> вызывается: включите как минимум `Accept` , `Content-Type` , и `Origin` , а также любые пользовательские заголовки, которые требуется поддерживать.</span><span class="sxs-lookup"><span data-stu-id="16230-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="16230-289">Код автоматического предпечатного запроса</span><span class="sxs-lookup"><span data-stu-id="16230-289">Automatic preflight request code</span></span>

<span data-ttu-id="16230-290">Когда применяется политика CORS, выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="16230-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="16230-291">Глобально путем вызова `app.UseCors` в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="16230-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="16230-292">С помощью `[EnableCors]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="16230-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="16230-293">ASP.NET Core отвечает на запрос параметров предварительной проверки.</span><span class="sxs-lookup"><span data-stu-id="16230-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="16230-294">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает автоматические предпечатные запросы.</span><span class="sxs-lookup"><span data-stu-id="16230-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="16230-295">Это поведение демонстрируется в разделе [тестирование CORS](#testc) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="16230-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="16230-296">Атрибут [Хттпоптионс] для предпечатных запросов</span><span class="sxs-lookup"><span data-stu-id="16230-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="16230-297">Когда CORS включается с соответствующей политикой, ASP.NET Core обычно отвечает на запросы на предпечатную CORS автоматически.</span><span class="sxs-lookup"><span data-stu-id="16230-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="16230-298">В некоторых сценариях это может быть не так.</span><span class="sxs-lookup"><span data-stu-id="16230-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="16230-299">Например, использование [CORS с маршрутизацией конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="16230-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="16230-300">В следующем коде атрибут [[хттпоптионс]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) используется для создания конечных точек для запросов Options:</span><span class="sxs-lookup"><span data-stu-id="16230-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="16230-301">Инструкции по тестированию приведенного выше кода см. в статьях [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="16230-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="16230-302">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="16230-302">Set the preflight expiration time</span></span>

<span data-ttu-id="16230-303">`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="16230-304">Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="16230-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="16230-305">Принцип работы CORS</span><span class="sxs-lookup"><span data-stu-id="16230-305">How CORS works</span></span>

<span data-ttu-id="16230-306">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.</span><span class="sxs-lookup"><span data-stu-id="16230-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="16230-307">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="16230-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="16230-308">CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="16230-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="16230-309">Например, вредоносный субъект может использовать [межсайтовые сценарии (XSS)](xref:security/cross-site-scripting) на сайте и выполнять межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.</span><span class="sxs-lookup"><span data-stu-id="16230-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="16230-310">API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="16230-311">Для применения CORS требуется клиент (браузер).</span><span class="sxs-lookup"><span data-stu-id="16230-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="16230-312">Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="16230-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="16230-313">Например, в любом из следующих средств будет отображаться ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="16230-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="16230-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="16230-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="16230-315">Postman</span><span class="sxs-lookup"><span data-stu-id="16230-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="16230-316">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="16230-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="16230-317">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="16230-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="16230-318">Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.</span><span class="sxs-lookup"><span data-stu-id="16230-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="16230-319">Браузеры без CORS не могут выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="16230-320">Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения.</span><span class="sxs-lookup"><span data-stu-id="16230-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="16230-321">JSONP не использует XHR, он использует `<script>` тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="16230-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="16230-322">Скрипты могут загружаться в разных источниках.</span><span class="sxs-lookup"><span data-stu-id="16230-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="16230-323">В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="16230-324">Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="16230-325">Для включения CORS не требуется пользовательский код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="16230-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="16230-326">[Кнопка "вставить тест"](https://cors3.azurewebsites.net/test) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="16230-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="16230-327">Ниже приведен пример запроса на перекрестное происхождение из кнопки проверки [значений](https://cors3.azurewebsites.net/) в `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="16230-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="16230-328">`Origin`Заголовок:</span><span class="sxs-lookup"><span data-stu-id="16230-328">The `Origin` header:</span></span>

* <span data-ttu-id="16230-329">Предоставляет домен сайта, выполняющего запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="16230-330">Параметр является обязательным и должен отличаться от узла.</span><span class="sxs-lookup"><span data-stu-id="16230-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="16230-331">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="16230-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="16230-332">**Заголовки ответа**</span><span class="sxs-lookup"><span data-stu-id="16230-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="16230-333">**Заголовки запроса**</span><span class="sxs-lookup"><span data-stu-id="16230-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="16230-334">В `OPTIONS` запросах сервер задает заголовок заголовка **ответа** `Access-Control-Allow-Origin: {allowed origin}` в ответе.</span><span class="sxs-lookup"><span data-stu-id="16230-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="16230-335">Например [,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)запрос на нажатие кнопки [удалить [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` содержит следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="16230-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="16230-336">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="16230-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="16230-337">**Заголовки ответа**</span><span class="sxs-lookup"><span data-stu-id="16230-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="16230-338">**Заголовки запроса**</span><span class="sxs-lookup"><span data-stu-id="16230-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="16230-339">В предыдущих **заголовках ответа** сервер задает заголовок [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) в ответе.</span><span class="sxs-lookup"><span data-stu-id="16230-339">In the preceding **Response headers** , the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="16230-340">`https://cors1.azurewebsites.net`Значение этого заголовка соответствует `Origin` заголовку запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="16230-341">Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> вызывается метод, `Access-Control-Allow-Origin: *` возвращается значение с подстановочным знаком.</span><span class="sxs-lookup"><span data-stu-id="16230-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="16230-342">`AllowAnyOrigin` разрешает любой источник.</span><span class="sxs-lookup"><span data-stu-id="16230-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="16230-343">Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="16230-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="16230-344">В частности, браузер не разрешает запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="16230-345">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.</span><span class="sxs-lookup"><span data-stu-id="16230-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="16230-346">Запросы параметров вывода</span><span class="sxs-lookup"><span data-stu-id="16230-346">Display OPTIONS requests</span></span>

<span data-ttu-id="16230-347">По умолчанию браузеры Chrome и ребра не отображают запросы параметров на вкладке Сеть средств F12.</span><span class="sxs-lookup"><span data-stu-id="16230-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="16230-348">Отображение запросов OPTIONS в следующих браузерах:</span><span class="sxs-lookup"><span data-stu-id="16230-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="16230-349">`chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="16230-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="16230-350">отключите флаг.</span><span class="sxs-lookup"><span data-stu-id="16230-350">disable the flag.</span></span>
* <span data-ttu-id="16230-351">перезагрузить.</span><span class="sxs-lookup"><span data-stu-id="16230-351">restart.</span></span>

<span data-ttu-id="16230-352">Firefox по умолчанию отображает запросы параметров.</span><span class="sxs-lookup"><span data-stu-id="16230-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="16230-353">CORS в IIS</span><span class="sxs-lookup"><span data-stu-id="16230-353">CORS in IIS</span></span>

<span data-ttu-id="16230-354">При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="16230-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="16230-355">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="16230-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="16230-356">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="16230-356">Test CORS</span></span>

<span data-ttu-id="16230-357">В [примере загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="16230-358">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="16230-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="16230-359">Пример представляет собой проект API с :::no-loc(Razor)::: добавленными страницами:</span><span class="sxs-lookup"><span data-stu-id="16230-359">The sample is an API project with :::no-loc(Razor)::: Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="16230-360">`WithOrigins("https://localhost:<port>");` следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="16230-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="16230-361">Ниже `ValuesController` приведены конечные точки для тестирования.</span><span class="sxs-lookup"><span data-stu-id="16230-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="16230-362">[Мидисплайраутеинфо](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется пакетом NuGet [Rick.Docs. Samples. раутеинфо](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) и отображает сведения о маршруте.</span><span class="sxs-lookup"><span data-stu-id="16230-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="16230-363">Протестируйте предыдущий пример кода с помощью одного из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="16230-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="16230-364">Используйте развернутый пример приложения в [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="16230-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="16230-365">Нет необходимости скачивать пример.</span><span class="sxs-lookup"><span data-stu-id="16230-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="16230-366">Запустите пример с `dotnet run` использованием URL-адреса по умолчанию для `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="16230-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="16230-367">Запустите пример из Visual Studio с портом 44398 для URL-адреса `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="16230-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="16230-368">Использование браузера с инструментами F12:</span><span class="sxs-lookup"><span data-stu-id="16230-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="16230-369">Нажмите кнопку **значения** и проверьте заголовки на вкладке **сеть** .</span><span class="sxs-lookup"><span data-stu-id="16230-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="16230-370">Нажмите кнопку **Вставить тест** .</span><span class="sxs-lookup"><span data-stu-id="16230-370">Select the **PUT test** button.</span></span> <span data-ttu-id="16230-371">Инструкции по отображению запроса OPTIONS см. в разделе [Параметры отображения запросы](#options) .</span><span class="sxs-lookup"><span data-stu-id="16230-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="16230-372">**Тест размещения** создает два запроса: запрос на предварительную проверку параметров и запрос на размещение.</span><span class="sxs-lookup"><span data-stu-id="16230-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="16230-373">Нажмите **`GetValues2 [DisableCors]`** кнопку, чтобы активировать неудачный запрос CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="16230-374">Как упоминалось в документе, ответ возвращает значение 200, но запрос CORS не выполняется.</span><span class="sxs-lookup"><span data-stu-id="16230-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="16230-375">Перейдите на вкладку **Console (консоль** ), чтобы просмотреть ошибку CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="16230-376">В зависимости от браузера отображается сообщение об ошибке следующего вида:</span><span class="sxs-lookup"><span data-stu-id="16230-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="16230-377">Доступ к выборке `'https://cors1.azurewebsites.net/api/values/GetValues2'` из источника `'https://cors3.azurewebsites.net'` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="16230-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="16230-378">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="16230-379">Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого [как](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler), или [POST](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="16230-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="16230-380">При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="16230-381">Если запрос не имеет *источника* на основе значения `Origin` заголовка:</span><span class="sxs-lookup"><span data-stu-id="16230-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="16230-382">Для обработки запроса по промежуточного слоя CORS не требуется.</span><span class="sxs-lookup"><span data-stu-id="16230-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="16230-383">Заголовки CORS не возвращаются в ответе.</span><span class="sxs-lookup"><span data-stu-id="16230-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="16230-384">Следующая команда использует `curl` , чтобы выдать запрос Options с информацией:</span><span class="sxs-lookup"><span data-stu-id="16230-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="16230-385">Тестирование CORS с маршрутизацией конечных точек и [Хттпоптионс]</span><span class="sxs-lookup"><span data-stu-id="16230-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="16230-386">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf).</span><span class="sxs-lookup"><span data-stu-id="16230-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="16230-387">Рассмотрим следующий код, который использует [маршрутизацию конечных точек для включения CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="16230-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="16230-388">Ниже `TodoItems1Controller` приведены конечные точки для тестирования.</span><span class="sxs-lookup"><span data-stu-id="16230-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="16230-389">Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=1) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="16230-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="16230-390">Кнопки **Delete [EnableCors]** и **Get [EnableCors]** выполняются успешно, так как конечные точки имеют `[EnableCors]` и отвечают на предпечатные запросы.</span><span class="sxs-lookup"><span data-stu-id="16230-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="16230-391">Другие конечные точки не удается.</span><span class="sxs-lookup"><span data-stu-id="16230-391">The other endpoints fails.</span></span> <span data-ttu-id="16230-392">Не удается выполнить кнопку **Get** , так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:</span><span class="sxs-lookup"><span data-stu-id="16230-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="16230-393">Ниже `TodoItems2Controller` приведены аналогичные конечные точки, но включается явный код для реагирования на запросы Options:</span><span class="sxs-lookup"><span data-stu-id="16230-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="16230-394">Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=2) в развернутом примере.</span><span class="sxs-lookup"><span data-stu-id="16230-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="16230-395">В раскрывающемся списке **контроллер** выберите Предварительная **Проверка** , а затем **Задайте значение контроллер** .</span><span class="sxs-lookup"><span data-stu-id="16230-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller** .</span></span> <span data-ttu-id="16230-396">Все вызовы CORS к `TodoItems2Controller` конечным точкам выполнены.</span><span class="sxs-lookup"><span data-stu-id="16230-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16230-397">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="16230-397">Additional resources</span></span>

* [<span data-ttu-id="16230-398">Общий доступ к ресурсам между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="16230-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="16230-399">Приступая к работе с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="16230-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16230-400">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="16230-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="16230-401">В этой статье показано, как включить CORS в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="16230-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="16230-402">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="16230-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="16230-403">Это ограничение называется *политика одного источника* .</span><span class="sxs-lookup"><span data-stu-id="16230-403">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="16230-404">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="16230-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="16230-405">Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении.</span><span class="sxs-lookup"><span data-stu-id="16230-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="16230-406">Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="16230-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="16230-407">[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="16230-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="16230-408">— Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="16230-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="16230-409">**Не** является функцией безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="16230-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="16230-410">Интерфейс API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="16230-411">Дополнительные сведения см. в разделе [как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="16230-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="16230-412">Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.</span><span class="sxs-lookup"><span data-stu-id="16230-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="16230-413">Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="16230-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="16230-414">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16230-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="16230-415">Тот же источник</span><span class="sxs-lookup"><span data-stu-id="16230-415">Same origin</span></span>

<span data-ttu-id="16230-416">Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="16230-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="16230-417">Эти два URL-адреса имеют один и тот же источник:</span><span class="sxs-lookup"><span data-stu-id="16230-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="16230-418">Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="16230-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="16230-419">`https://example.net`: Другой домен</span><span class="sxs-lookup"><span data-stu-id="16230-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="16230-420">`https://www.example.com/foo.html`: Другой поддомен</span><span class="sxs-lookup"><span data-stu-id="16230-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="16230-421">`http://example.com/foo.html`: Другая схема</span><span class="sxs-lookup"><span data-stu-id="16230-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="16230-422">`https://example.com:9000/foo.html`: Другой порт</span><span class="sxs-lookup"><span data-stu-id="16230-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="16230-423">При сравнении источников Internet Explorer не учитывает порт.</span><span class="sxs-lookup"><span data-stu-id="16230-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="16230-424">CORS с именованной политикой и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="16230-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="16230-425">По промежуточного слоя CORS обрабатывает запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="16230-426">Следующий код включает CORS для всего приложения с указанным источником:</span><span class="sxs-lookup"><span data-stu-id="16230-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="16230-427">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="16230-427">The preceding code:</span></span>

* <span data-ttu-id="16230-428">Задает имя политики " \_ мялловспеЦификоригинс".</span><span class="sxs-lookup"><span data-stu-id="16230-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="16230-429">Имя политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="16230-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="16230-430">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который включает CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="16230-431">Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="16230-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="16230-432">Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект.</span><span class="sxs-lookup"><span data-stu-id="16230-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="16230-433">[Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="16230-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="16230-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:</span><span class="sxs-lookup"><span data-stu-id="16230-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="16230-435">Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="16230-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="16230-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Метод может привести к цепочке методов, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="16230-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="16230-437">Примечание. URL-адрес **не** должен содержать косую черту ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="16230-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="16230-438">Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.</span><span class="sxs-lookup"><span data-stu-id="16230-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="16230-439">Следующий код применяет политики CORS ко всем конечным точкам приложений через по промежуточного слоя CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="16230-440">Примечание. `UseCors` необходимо вызвать до `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="16230-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="16230-441">См. раздел [Включение CORS на :::no-loc(Razor)::: страницах, контроллерах и методах действий](#ecors) для применения политики CORS на уровне страницы, контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="16230-441">See [Enable CORS in :::no-loc(Razor)::: Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="16230-442">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="16230-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="16230-443">Включение CORS с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="16230-443">Enable CORS with attributes</span></span>

<span data-ttu-id="16230-444">Атрибут [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="16230-445">`[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не всех конечных точек.</span><span class="sxs-lookup"><span data-stu-id="16230-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="16230-446">Используйте `[EnableCors]` , чтобы указать политику по умолчанию и `[EnableCors("{Policy String}")]` указать политику.</span><span class="sxs-lookup"><span data-stu-id="16230-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="16230-447">`[EnableCors]`Атрибут может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="16230-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="16230-448">:::no-loc(Razor)::: Страниц `PageModel`</span><span class="sxs-lookup"><span data-stu-id="16230-448">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="16230-449">Контроллер</span><span class="sxs-lookup"><span data-stu-id="16230-449">Controller</span></span>
* <span data-ttu-id="16230-450">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="16230-450">Controller action method</span></span>

<span data-ttu-id="16230-451">С атрибутом можно применить различные политики для контроллера, модели страницы или действия  `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="16230-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="16230-452">Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики.</span><span class="sxs-lookup"><span data-stu-id="16230-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="16230-453">**Не** рекомендуется объединять политики.</span><span class="sxs-lookup"><span data-stu-id="16230-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="16230-454">Используйте `[EnableCors]` атрибут или по промежуточного слоя, а **не оба** .</span><span class="sxs-lookup"><span data-stu-id="16230-454">Use the `[EnableCors]` attribute or middleware, **not both** .</span></span> <span data-ttu-id="16230-455">При использовании не `[EnableCors]` определяйте **not** политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="16230-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="16230-456">Следующий код применяет к каждому методу другую политику:</span><span class="sxs-lookup"><span data-stu-id="16230-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="16230-457">Следующий код создает политику CORS по умолчанию и политику с именем `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="16230-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="16230-458">Отключение CORS</span><span class="sxs-lookup"><span data-stu-id="16230-458">Disable CORS</span></span>

<span data-ttu-id="16230-459">Атрибут [ &lbrack; ДИСАБЛЕКОРС &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отключает CORS для контроллера, модели страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="16230-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="16230-460">Параметры политики CORS</span><span class="sxs-lookup"><span data-stu-id="16230-460">CORS policy options</span></span>

<span data-ttu-id="16230-461">В этом разделе описаны различные параметры, которые можно задать в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="16230-462">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="16230-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="16230-463">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="16230-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="16230-464">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="16230-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="16230-465">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="16230-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="16230-466">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="16230-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="16230-467">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="16230-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="16230-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> метод вызывается в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="16230-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16230-469">Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".</span><span class="sxs-lookup"><span data-stu-id="16230-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="16230-470">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="16230-470">Set the allowed origins</span></span>

<span data-ttu-id="16230-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ).</span><span class="sxs-lookup"><span data-stu-id="16230-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="16230-472">`AllowAnyOrigin` не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.</span><span class="sxs-lookup"><span data-stu-id="16230-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="16230-473">Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="16230-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="16230-474">Для безопасного приложения укажите точный список источников, если клиент должен авторизоваться для доступа к ресурсам сервера.</span><span class="sxs-lookup"><span data-stu-id="16230-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="16230-475">`AllowAnyOrigin` влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок.</span><span class="sxs-lookup"><span data-stu-id="16230-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="16230-476">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="16230-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.</span><span class="sxs-lookup"><span data-stu-id="16230-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="16230-478">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="16230-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="16230-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="16230-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="16230-480">Разрешает любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="16230-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="16230-481">Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок.</span><span class="sxs-lookup"><span data-stu-id="16230-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="16230-482">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="16230-483">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="16230-483">Set the allowed request headers</span></span>

<span data-ttu-id="16230-484">Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый *заголовком запроса на создание* , вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="16230-484">To allow specific headers to be sent in a CORS request, called *author request headers* , call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="16230-485">Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="16230-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="16230-486">Этот параметр влияет на предпечатные запросы и `Access-Control-Request-Headers` заголовок.</span><span class="sxs-lookup"><span data-stu-id="16230-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="16230-487">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="16230-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="16230-488">По промежуточного слоя CORS всегда позволяет отправлять четыре заголовка в, `Access-Control-Request-Headers` независимо от значений, настроенных в корсполици. Headers.</span><span class="sxs-lookup"><span data-stu-id="16230-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="16230-489">Этот список заголовков включает:</span><span class="sxs-lookup"><span data-stu-id="16230-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="16230-490">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="16230-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="16230-491">По промежуточного слоя CORS успешно реагирует на предварительный запрос со следующим заголовком запроса, так как `Content-Language` всегда разрешено следующее:</span><span class="sxs-lookup"><span data-stu-id="16230-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="16230-492">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="16230-492">Set the exposed response headers</span></span>

<span data-ttu-id="16230-493">По умолчанию браузер не предоставляет все заголовки ответа приложению.</span><span class="sxs-lookup"><span data-stu-id="16230-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="16230-494">Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="16230-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="16230-495">По умолчанию доступны заголовки ответов:</span><span class="sxs-lookup"><span data-stu-id="16230-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="16230-496">Спецификация CORS вызывает эти заголовки *простых заголовков ответа* .</span><span class="sxs-lookup"><span data-stu-id="16230-496">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="16230-497">Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="16230-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="16230-498">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="16230-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="16230-499">Учетные данные требует специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="16230-500">По умолчанию браузер не отправляет учетные данные с запросом между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="16230-501">Учетные данные включают :::no-loc(cookie)::: схемы проверки подлинности s и HTTP.</span><span class="sxs-lookup"><span data-stu-id="16230-501">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="16230-502">Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .</span><span class="sxs-lookup"><span data-stu-id="16230-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="16230-503">Использование `XMLHttpRequest` напрямую:</span><span class="sxs-lookup"><span data-stu-id="16230-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="16230-504">Использование jQuery:</span><span class="sxs-lookup"><span data-stu-id="16230-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="16230-505">Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="16230-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="16230-506">Сервер должен разрешать учетные данные.</span><span class="sxs-lookup"><span data-stu-id="16230-506">The server must allow the credentials.</span></span> <span data-ttu-id="16230-507">Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="16230-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="16230-508">HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="16230-509">Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="16230-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="16230-510">Разрешение учетных данных между источниками является угрозой безопасности.</span><span class="sxs-lookup"><span data-stu-id="16230-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="16230-511">Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="16230-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="16230-512">В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="16230-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="16230-513">Предпечатные запросы</span><span class="sxs-lookup"><span data-stu-id="16230-513">Preflight requests</span></span>

<span data-ttu-id="16230-514">Для некоторых запросов CORS браузер отправляет дополнительный запрос перед выполнением фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="16230-515">Этот запрос называется *предпечатным запросом* .</span><span class="sxs-lookup"><span data-stu-id="16230-515">This request is called a *preflight request* .</span></span> <span data-ttu-id="16230-516">Браузер может пропустить Предпечатный запрос, если выполняются следующие условия.</span><span class="sxs-lookup"><span data-stu-id="16230-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="16230-517">Метод запроса — GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="16230-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="16230-518">Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="16230-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="16230-519">`Content-Type`Заголовок, если он задан, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="16230-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="16230-520">Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта.</span><span class="sxs-lookup"><span data-stu-id="16230-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="16230-521">Спецификация CORS вызывает заголовки *запроса автора* заголовков.</span><span class="sxs-lookup"><span data-stu-id="16230-521">The CORS specification calls these headers *author request headers* .</span></span> <span data-ttu-id="16230-522">Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="16230-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="16230-523">Ниже приведен пример предпечатного запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="16230-524">Запрос перед рейсом использует метод HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="16230-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="16230-525">Он включает два специальных заголовка:</span><span class="sxs-lookup"><span data-stu-id="16230-525">It includes two special headers:</span></span>

* <span data-ttu-id="16230-526">`Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="16230-527">`Access-Control-Request-Headers`: Список заголовков запросов, которые приложение задает для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="16230-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="16230-528">Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="16230-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="16230-529">При включении CORS с соответствующей политикой ASP.NET Core обычно автоматически реагирует на предпечатные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="16230-530">См. [атрибут [хттпоптионс] для предпечатных запросов](#pro).</span><span class="sxs-lookup"><span data-stu-id="16230-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="16230-531">Предпечатный запрос CORS может включать `Access-Control-Request-Headers` заголовок, указывающий серверу на заголовки, отправляемые с фактическим запросом.</span><span class="sxs-lookup"><span data-stu-id="16230-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="16230-532">Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="16230-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="16230-533">Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="16230-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="16230-534">Браузеры не полностью согласуются с тем, как они заданы `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="16230-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="16230-535">Если для заголовков задано значение `"*"` , отличное от (или использование <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), следует включить как минимум `Accept` , `Content-Type` , и `Origin` , а также любые настраиваемые заголовки, которые требуется поддерживать.</span><span class="sxs-lookup"><span data-stu-id="16230-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="16230-536">Ниже приведен пример ответа на Предпечатный запрос (при условии, что сервер разрешает запрос):</span><span class="sxs-lookup"><span data-stu-id="16230-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="16230-537">Ответ содержит `Access-Control-Allow-Methods` заголовок со списком допустимых методов и, при необходимости `Access-Control-Allow-Headers` , заголовок, в котором перечислены разрешенные заголовки.</span><span class="sxs-lookup"><span data-stu-id="16230-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="16230-538">Если Предпечатный запрос выполнен, браузер отправляет фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="16230-539">Если Предпечатный запрос отклонен, приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="16230-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="16230-540">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="16230-541">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="16230-541">Set the preflight expiration time</span></span>

<span data-ttu-id="16230-542">`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="16230-543">Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="16230-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="16230-544">Принцип работы CORS</span><span class="sxs-lookup"><span data-stu-id="16230-544">How CORS works</span></span>

<span data-ttu-id="16230-545">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.</span><span class="sxs-lookup"><span data-stu-id="16230-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="16230-546">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="16230-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="16230-547">CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="16230-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="16230-548">Например, вредоносный субъект может использовать для веб [-узла предотвращение межсайтовых сценариев (XSS)](xref:security/cross-site-scripting) и выполнить межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.</span><span class="sxs-lookup"><span data-stu-id="16230-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="16230-549">Интерфейс API не обеспечивает безопасную работу, разрешая CORS.</span><span class="sxs-lookup"><span data-stu-id="16230-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="16230-550">Для применения CORS требуется клиент (браузер).</span><span class="sxs-lookup"><span data-stu-id="16230-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="16230-551">Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="16230-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="16230-552">Например, в любом из следующих средств будет отображаться ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="16230-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="16230-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="16230-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="16230-554">Postman</span><span class="sxs-lookup"><span data-stu-id="16230-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="16230-555">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="16230-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="16230-556">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="16230-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="16230-557">Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.</span><span class="sxs-lookup"><span data-stu-id="16230-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="16230-558">Браузеры (без CORS) не могут выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="16230-559">Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения.</span><span class="sxs-lookup"><span data-stu-id="16230-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="16230-560">JSONP не использует XHR, он использует `<script>` тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="16230-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="16230-561">Скрипты могут загружаться в разных источниках.</span><span class="sxs-lookup"><span data-stu-id="16230-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="16230-562">В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="16230-563">Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="16230-564">Для включения CORS не требуется пользовательский код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="16230-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="16230-565">Ниже приведен пример запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="16230-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="16230-566">`Origin`Заголовок предоставляет домен сайта, выполняющего запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="16230-567">`Origin`Заголовок является обязательным и должен отличаться от узла.</span><span class="sxs-lookup"><span data-stu-id="16230-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="16230-568">Если сервер разрешает запрос, он устанавливает `Access-Control-Allow-Origin` заголовок в ответе.</span><span class="sxs-lookup"><span data-stu-id="16230-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="16230-569">Значение этого заголовка либо соответствует `Origin` заголовку запроса, либо является подстановочным значением `"*"` , что означает, что любой источник разрешен:</span><span class="sxs-lookup"><span data-stu-id="16230-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="16230-570">Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="16230-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="16230-571">В частности, браузер не разрешает запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="16230-572">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.</span><span class="sxs-lookup"><span data-stu-id="16230-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="16230-573">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="16230-573">Test CORS</span></span>

<span data-ttu-id="16230-574">Тестирование CORS:</span><span class="sxs-lookup"><span data-stu-id="16230-574">To test CORS:</span></span>

1. <span data-ttu-id="16230-575">[Создайте проект API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="16230-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="16230-576">Кроме того, можно [загрузить пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="16230-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="16230-577">Включите CORS с помощью одного из подходов, описанных в этом документе.</span><span class="sxs-lookup"><span data-stu-id="16230-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="16230-578">Пример:</span><span class="sxs-lookup"><span data-stu-id="16230-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="16230-579">`WithOrigins("https://localhost:<port>");` следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="16230-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="16230-580">Создайте проект веб-приложения ( :::no-loc(Razor)::: страницы или MVC).</span><span class="sxs-lookup"><span data-stu-id="16230-580">Create a web app project (:::no-loc(Razor)::: Pages or MVC).</span></span> <span data-ttu-id="16230-581">В образце используются :::no-loc(Razor)::: страницы.</span><span class="sxs-lookup"><span data-stu-id="16230-581">The sample uses :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="16230-582">Вы можете создать веб-приложение в том же решении, что и проект API.</span><span class="sxs-lookup"><span data-stu-id="16230-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="16230-583">Добавьте выделенный ниже код в файл *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="16230-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="16230-584">В приведенном выше коде замените `url: 'https://<web app>.azurewebsites.net/api/values/1',` URL-адресом развернутого приложения.</span><span class="sxs-lookup"><span data-stu-id="16230-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="16230-585">Разверните проект API.</span><span class="sxs-lookup"><span data-stu-id="16230-585">Deploy the API project.</span></span> <span data-ttu-id="16230-586">Например, выполните [развертывание в Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="16230-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="16230-587">Запустите :::no-loc(Razor)::: страницы или приложение MVC на рабочем столе и нажмите кнопку **Test (тест** ).</span><span class="sxs-lookup"><span data-stu-id="16230-587">Run the :::no-loc(Razor)::: Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="16230-588">Используйте средства F12 для просмотра сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="16230-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="16230-589">Удалите источник localhost из `WithOrigins` и разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="16230-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="16230-590">Кроме того, можно запустить клиентское приложение с другим портом.</span><span class="sxs-lookup"><span data-stu-id="16230-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="16230-591">Например, запустите из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="16230-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="16230-592">Протестируйте клиентское приложение.</span><span class="sxs-lookup"><span data-stu-id="16230-592">Test with the client app.</span></span> <span data-ttu-id="16230-593">Ошибки CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript.</span><span class="sxs-lookup"><span data-stu-id="16230-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="16230-594">Чтобы просмотреть ошибку, используйте вкладку консоль в средствах F12.</span><span class="sxs-lookup"><span data-stu-id="16230-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="16230-595">В зависимости от браузера вы получаете ошибку (в консоли средств F12), как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="16230-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="16230-596">Использование Microsoft ребр:</span><span class="sxs-lookup"><span data-stu-id="16230-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="16230-597">**SEC7120: [CORS] `https://localhost:44375` не удалось найти источник `https://localhost:44375` в заголовке ответа Access-Control-Allow-Origin для ресурса-источника в `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="16230-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="16230-598">Использование Chrome:</span><span class="sxs-lookup"><span data-stu-id="16230-598">Using Chrome:</span></span>

     <span data-ttu-id="16230-599">**Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` из источника `https://localhost:44375` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".**</span><span class="sxs-lookup"><span data-stu-id="16230-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="16230-600">Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого как [Fiddler](https://www.telerik.com/fiddler) или [POST](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="16230-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="16230-601">При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос.</span><span class="sxs-lookup"><span data-stu-id="16230-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="16230-602">Если запрос не имеет *источника* на основе значения `Origin` заголовка:</span><span class="sxs-lookup"><span data-stu-id="16230-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="16230-603">Для обработки запроса по промежуточного слоя CORS не требуется.</span><span class="sxs-lookup"><span data-stu-id="16230-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="16230-604">Заголовки CORS не возвращаются в ответе.</span><span class="sxs-lookup"><span data-stu-id="16230-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="16230-605">CORS в IIS</span><span class="sxs-lookup"><span data-stu-id="16230-605">CORS in IIS</span></span>

<span data-ttu-id="16230-606">При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="16230-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="16230-607">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="16230-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16230-608">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="16230-608">Additional resources</span></span>

* [<span data-ttu-id="16230-609">Общий доступ к ресурсам между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="16230-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="16230-610">Приступая к работе с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="16230-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
