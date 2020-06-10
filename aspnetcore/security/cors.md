---
title: Включение запросов между источниками (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать CORS в качестве стандарта для разрешения или отклонения запросов между источниками в ASP.NET Core приложении.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: a78aff2d2e16f36ed034e6af110d7ed763271583
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105757"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="3053d-103">Включение запросов между источниками (CORS) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3053d-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3053d-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="3053d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="3053d-105">В этой статье показано, как включить CORS в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3053d-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3053d-106">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="3053d-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3053d-107">Это ограничение называется *политика одного источника*.</span><span class="sxs-lookup"><span data-stu-id="3053d-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3053d-108">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="3053d-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3053d-109">Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении.</span><span class="sxs-lookup"><span data-stu-id="3053d-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="3053d-110">Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="3053d-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3053d-111">[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="3053d-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3053d-112">— Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="3053d-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3053d-113">**Не** является функцией безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="3053d-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3053d-114">Интерфейс API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3053d-115">Дополнительные сведения см. в разделе [как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="3053d-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3053d-116">Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.</span><span class="sxs-lookup"><span data-stu-id="3053d-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3053d-117">Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="3053d-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3053d-118">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3053d-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3053d-119">Тот же источник</span><span class="sxs-lookup"><span data-stu-id="3053d-119">Same origin</span></span>

<span data-ttu-id="3053d-120">Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="3053d-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3053d-121">Эти два URL-адреса имеют один и тот же источник:</span><span class="sxs-lookup"><span data-stu-id="3053d-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3053d-122">Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="3053d-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3053d-123">`https://example.net`: Другой домен</span><span class="sxs-lookup"><span data-stu-id="3053d-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="3053d-124">`https://www.example.com/foo.html`: Другой поддомен</span><span class="sxs-lookup"><span data-stu-id="3053d-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="3053d-125">`http://example.com/foo.html`: Другая схема</span><span class="sxs-lookup"><span data-stu-id="3053d-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="3053d-126">`https://example.com:9000/foo.html`: Другой порт</span><span class="sxs-lookup"><span data-stu-id="3053d-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="3053d-127">Включение CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-127">Enable CORS</span></span>

<span data-ttu-id="3053d-128">Включить CORS можно тремя способами:</span><span class="sxs-lookup"><span data-stu-id="3053d-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="3053d-129">По промежуточного слоя с использованием [именованной политики](#np) или [политики по умолчанию](#dp).</span><span class="sxs-lookup"><span data-stu-id="3053d-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="3053d-130">Использование [маршрутизации конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="3053d-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3053d-131">С атрибутом [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="3053d-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="3053d-132">Использование атрибута [[EnableCors]](#attr) с именованной политикой предоставляет элемент управления Finest в ограничении конечных точек, поддерживающих CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="3053d-133">Каждый подход подробно описан в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="3053d-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3053d-134">CORS с именованной политикой и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3053d-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="3053d-135">По промежуточного слоя CORS обрабатывает запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3053d-136">Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными источниками:</span><span class="sxs-lookup"><span data-stu-id="3053d-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="3053d-137">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3053d-137">The preceding code:</span></span>

* <span data-ttu-id="3053d-138">Задает имя политики `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="3053d-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="3053d-139">Имя политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="3053d-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3053d-140">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и задает `_myAllowSpecificOrigins` политику CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="3053d-141">`UseCors`добавляет по промежуточного слоя CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="3053d-142">Вызов `UseCors` должен быть помещен после `UseRouting` , но до `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="3053d-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="3053d-143">Дополнительные сведения см. в разделе [порядок по промежуточного слоя](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="3053d-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="3053d-144">Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="3053d-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3053d-145">Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект.</span><span class="sxs-lookup"><span data-stu-id="3053d-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3053d-146">[Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="3053d-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="3053d-147">Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера.</span><span class="sxs-lookup"><span data-stu-id="3053d-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="3053d-148">Чтобы применить политику CORS к конкретным конечным точкам, см. раздел [Маршрутизация конечных](#ecors) точек.</span><span class="sxs-lookup"><span data-stu-id="3053d-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="3053d-149">При маршрутизации конечных точек по промежуточного слоя CORS **должно** быть настроено для выполнения между вызовами функций `UseRouting` и `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="3053d-149">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="3053d-150">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="3053d-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="3053d-151"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:</span><span class="sxs-lookup"><span data-stu-id="3053d-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3053d-152">Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="3053d-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="3053d-153"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Методы могут быть объединены в цепочку, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3053d-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="3053d-154">Примечание. указанный URL-адрес **не** должен содержать косую черту ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="3053d-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3053d-155">Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.</span><span class="sxs-lookup"><span data-stu-id="3053d-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="3053d-156">CORS с политикой по умолчанию и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3053d-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="3053d-157">Следующий выделенный код включает политику CORS по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3053d-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="3053d-158">Приведенный выше код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.</span><span class="sxs-lookup"><span data-stu-id="3053d-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="3053d-159">Включение CORS с маршрутизацией конечных точек</span><span class="sxs-lookup"><span data-stu-id="3053d-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="3053d-160">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf).</span><span class="sxs-lookup"><span data-stu-id="3053d-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3053d-161">Дополнительные сведения см. в описании [этой проблемы GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тестировании CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="3053d-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="3053d-162">При маршрутизации конечных точек CORS можно включить для каждой конечной точки с помощью <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> набора методов расширения:</span><span class="sxs-lookup"><span data-stu-id="3053d-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="3053d-163">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3053d-163">In the preceding code:</span></span>

* <span data-ttu-id="3053d-164">`app.UseCors`включает по промежуточного слоя CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="3053d-165">Так как политика по умолчанию не настроена, `app.UseCors()` она не включает CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="3053d-166">`/echo`Конечные точки контроллера и позволяют выполнять запросы между источниками с помощью указанной политики.</span><span class="sxs-lookup"><span data-stu-id="3053d-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="3053d-167">`/echo2` Razor Конечные точки страниц и **не** позволяют выполнять запросы независимо от источника, так как не указана политика по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3053d-167">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="3053d-168">Атрибут [[дисаблекорс]](#dc) **не** отключает CORS, который был включен с помощью маршрутизации конечной точки с помощью `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="3053d-168">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="3053d-169">Инструкции по проверке кода, аналогичные приведенным выше, см. в разделе [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="3053d-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3053d-170">Включение CORS с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="3053d-170">Enable CORS with attributes</span></span>

<span data-ttu-id="3053d-171">Включение CORS с помощью атрибута [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение именованной политики только к тем конечным точкам, для которых требуется CORS, предоставляет элемент управления Finest.</span><span class="sxs-lookup"><span data-stu-id="3053d-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="3053d-172">Атрибут [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3053d-173">`[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не для всех конечных точек:</span><span class="sxs-lookup"><span data-stu-id="3053d-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="3053d-174">`[EnableCors]`Задает политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3053d-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="3053d-175">`[EnableCors("{Policy String}")]`Задает именованную политику.</span><span class="sxs-lookup"><span data-stu-id="3053d-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="3053d-176">`[EnableCors]`Атрибут может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="3053d-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="3053d-177">Страниц`PageModel`</span><span class="sxs-lookup"><span data-stu-id="3053d-177"> Page `PageModel`</span></span>
* <span data-ttu-id="3053d-178">Контроллер</span><span class="sxs-lookup"><span data-stu-id="3053d-178">Controller</span></span>
* <span data-ttu-id="3053d-179">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="3053d-179">Controller action method</span></span>

<span data-ttu-id="3053d-180">К контроллерам, моделям страниц или методам действий можно применять различные политики с `[EnableCors]` атрибутом.</span><span class="sxs-lookup"><span data-stu-id="3053d-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="3053d-181">Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики.</span><span class="sxs-lookup"><span data-stu-id="3053d-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="3053d-182">**Мы советуем использовать сочетание политик. Используйте** `[EnableCors]` **атрибут или по промежуточного слоя, а не оба в одном приложении.**</span><span class="sxs-lookup"><span data-stu-id="3053d-182">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="3053d-183">Следующий код применяет к каждому методу другую политику:</span><span class="sxs-lookup"><span data-stu-id="3053d-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3053d-184">Следующий код создает две политики CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="3053d-185">Для Finest управления ограничением запросов CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="3053d-186">Используйте `[EnableCors("MyPolicy")]` с именованной политикой.</span><span class="sxs-lookup"><span data-stu-id="3053d-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="3053d-187">Не определяйте политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3053d-187">Don't define a default policy.</span></span>
* <span data-ttu-id="3053d-188">Не используйте [маршрутизацию конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="3053d-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3053d-189">Код в следующем разделе соответствует приведенному выше списку.</span><span class="sxs-lookup"><span data-stu-id="3053d-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="3053d-190">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="3053d-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="3053d-191">Отключение CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-191">Disable CORS</span></span>

<span data-ttu-id="3053d-192">Атрибут [[дисаблекорс]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **не** отключает CORS, который был включен с помощью [маршрутизации конечной точки](#ecors).</span><span class="sxs-lookup"><span data-stu-id="3053d-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3053d-193">Следующий код определяет политику CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="3053d-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="3053d-194">Следующий код отключает CORS для `GetValues2` действия:</span><span class="sxs-lookup"><span data-stu-id="3053d-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="3053d-195">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3053d-195">The preceding code:</span></span>

* <span data-ttu-id="3053d-196">Не включает CORS с [маршрутизацией конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="3053d-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3053d-197">Не определяет [политику CORS по умолчанию](#dp).</span><span class="sxs-lookup"><span data-stu-id="3053d-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="3053d-198">Использует [[EnableCors ("MyPolicy")]](#attr) , чтобы включить `"MyPolicy"` политику CORS для контроллера.</span><span class="sxs-lookup"><span data-stu-id="3053d-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="3053d-199">Отключает CORS для `GetValues2` метода.</span><span class="sxs-lookup"><span data-stu-id="3053d-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="3053d-200">Инструкции по тестированию приведенного выше кода см. в разделе [тестирование CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="3053d-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3053d-201">Параметры политики CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-201">CORS policy options</span></span>

<span data-ttu-id="3053d-202">В этом разделе описаны различные параметры, которые можно задать в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3053d-203">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="3053d-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3053d-204">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="3053d-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3053d-205">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="3053d-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3053d-206">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="3053d-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3053d-207">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="3053d-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3053d-208">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="3053d-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3053d-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>метод вызывается в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3053d-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3053d-210">Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".</span><span class="sxs-lookup"><span data-stu-id="3053d-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3053d-211">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="3053d-211">Set the allowed origins</span></span>

<span data-ttu-id="3053d-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ).</span><span class="sxs-lookup"><span data-stu-id="3053d-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3053d-213">`AllowAnyOrigin`не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.</span><span class="sxs-lookup"><span data-stu-id="3053d-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3053d-214">Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="3053d-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3053d-215">Служба CORS возвращает недопустимый ответ CORS, если приложение настроено с обоими методами.</span><span class="sxs-lookup"><span data-stu-id="3053d-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="3053d-216">`AllowAnyOrigin`влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок.</span><span class="sxs-lookup"><span data-stu-id="3053d-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3053d-217">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3053d-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.</span><span class="sxs-lookup"><span data-stu-id="3053d-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3053d-219">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="3053d-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3053d-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3053d-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3053d-221">Разрешает любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="3053d-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="3053d-222">Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок.</span><span class="sxs-lookup"><span data-stu-id="3053d-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3053d-223">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3053d-224">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="3053d-224">Set the allowed request headers</span></span>

<span data-ttu-id="3053d-225">Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый [заголовком запроса на создание](https://xhr.spec.whatwg.org/#request), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="3053d-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3053d-226">Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3053d-227">`AllowAnyHeader`влияет на предпечатные запросы и заголовок [Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="3053d-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="3053d-228">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3053d-229">Политика по промежуточного слоя CORS соответствует определенным заголовкам, указанным в параметре, `WithHeaders` только если отправляемые заголовки в `Access-Control-Request-Headers` точности совпадают с заголовками, указанными в `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="3053d-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="3053d-230">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3053d-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="3053d-231">По промежуточного слоя CORS отклоняет Предпечатный запрос со следующим заголовком запроса, так как `Content-Language` ([Хеадернамес. контентлангуаже](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) нет в списке в `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="3053d-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="3053d-232">Приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="3053d-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3053d-233">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3053d-234">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="3053d-234">Set the exposed response headers</span></span>

<span data-ttu-id="3053d-235">По умолчанию браузер не предоставляет все заголовки ответа приложению.</span><span class="sxs-lookup"><span data-stu-id="3053d-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3053d-236">Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="3053d-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3053d-237">По умолчанию доступны заголовки ответов:</span><span class="sxs-lookup"><span data-stu-id="3053d-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3053d-238">Спецификация CORS вызывает эти заголовки *простых заголовков ответа*.</span><span class="sxs-lookup"><span data-stu-id="3053d-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3053d-239">Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3053d-240">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="3053d-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3053d-241">Учетные данные требует специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3053d-242">По умолчанию браузер не отправляет учетные данные с запросом между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3053d-243">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="3053d-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3053d-244">Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .</span><span class="sxs-lookup"><span data-stu-id="3053d-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3053d-245">Использование `XMLHttpRequest` напрямую:</span><span class="sxs-lookup"><span data-stu-id="3053d-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3053d-246">Использование jQuery:</span><span class="sxs-lookup"><span data-stu-id="3053d-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3053d-247">Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="3053d-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3053d-248">Сервер должен разрешать учетные данные.</span><span class="sxs-lookup"><span data-stu-id="3053d-248">The server must allow the credentials.</span></span> <span data-ttu-id="3053d-249">Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="3053d-250">HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3053d-251">Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="3053d-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3053d-252">Разрешение учетных данных между источниками является угрозой безопасности.</span><span class="sxs-lookup"><span data-stu-id="3053d-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3053d-253">Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="3053d-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3053d-254">В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="3053d-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="3053d-255">Предпечатные запросы</span><span class="sxs-lookup"><span data-stu-id="3053d-255">Preflight requests</span></span>

<span data-ttu-id="3053d-256">Для некоторых запросов CORS браузер отправляет запрос на дополнительные [Параметры](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) перед выполнением фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="3053d-257">Этот запрос называется [предпечатным запросом](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="3053d-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="3053d-258">Браузер может пропустить Предпечатный запрос, если выполняются **все** перечисленные ниже условия.</span><span class="sxs-lookup"><span data-stu-id="3053d-258">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="3053d-259">Метод запроса — GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="3053d-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3053d-260">Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="3053d-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3053d-261">`Content-Type`Заголовок, если он задан, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="3053d-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3053d-262">Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта.</span><span class="sxs-lookup"><span data-stu-id="3053d-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3053d-263">Спецификация CORS вызывает заголовки [запроса автора](https://www.w3.org/TR/cors/#author-request-headers)заголовков.</span><span class="sxs-lookup"><span data-stu-id="3053d-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="3053d-264">Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="3053d-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3053d-265">Ниже приведен пример ответа, аналогичного предпечатному запросу, выполненному с помощью кнопки **[размещение теста]** в разделе [Test CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="3053d-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
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

<span data-ttu-id="3053d-266">Предпечатный запрос использует метод [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="3053d-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="3053d-267">Он может включать следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="3053d-267">It may include the following headers:</span></span>

* <span data-ttu-id="3053d-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)— метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3053d-269">[Access-Control-request-headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers). список заголовков запросов, которые приложение устанавливает на основе фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3053d-270">Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="3053d-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="3053d-271">Access-Control — Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="3053d-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="3053d-272">Если Предпечатный запрос отклонен, приложение возвращает `200 OK` ответ, но не задает заголовки CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="3053d-273">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="3053d-274">Пример запрещенного запроса на предпечатную версию см. в разделе " [тестирование CORS](#testc) " этого документа.</span><span class="sxs-lookup"><span data-stu-id="3053d-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="3053d-275">С помощью средств F12 консольное приложение отображает ошибку, аналогичную одной из следующих в зависимости от браузера.</span><span class="sxs-lookup"><span data-stu-id="3053d-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="3053d-276">Firefox: запрос на перекрестное происхождение заблокирован: одна и та же политика происхождения запрещает чтение удаленного ресурса в `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="3053d-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="3053d-277">(Причина: запрос CORS не выполнен).</span><span class="sxs-lookup"><span data-stu-id="3053d-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="3053d-278">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="3053d-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="3053d-279">На основе Chromium: доступ к выборке в " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " из источника " https://cors3.azurewebsites.net " заблокирован политикой CORS: ответ на предварительный запрос не проходит проверку контроля доступа: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="3053d-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3053d-280">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="3053d-281">Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3053d-282">Чтобы разрешить все [заголовки запроса автора](https://www.w3.org/TR/cors/#author-request-headers), вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3053d-283">Браузеры не согласуются с тем, как они заданы `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="3053d-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3053d-284">Если:</span><span class="sxs-lookup"><span data-stu-id="3053d-284">If either:</span></span>

* <span data-ttu-id="3053d-285">Для заголовков заданы любые значения, отличные от`"*"`</span><span class="sxs-lookup"><span data-stu-id="3053d-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="3053d-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>вызывается: включите как минимум `Accept` , `Content-Type` , и `Origin` , а также любые пользовательские заголовки, которые требуется поддерживать.</span><span class="sxs-lookup"><span data-stu-id="3053d-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="3053d-287">Код автоматического предпечатного запроса</span><span class="sxs-lookup"><span data-stu-id="3053d-287">Automatic preflight request code</span></span>

<span data-ttu-id="3053d-288">Когда применяется политика CORS, выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="3053d-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="3053d-289">Глобально путем вызова `app.UseCors` в `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="3053d-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="3053d-290">С помощью `[EnableCors]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="3053d-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="3053d-291">ASP.NET Core отвечает на запрос параметров предварительной проверки.</span><span class="sxs-lookup"><span data-stu-id="3053d-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="3053d-292">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает автоматические предпечатные запросы.</span><span class="sxs-lookup"><span data-stu-id="3053d-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="3053d-293">Это поведение демонстрируется в разделе [тестирование CORS](#testc) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="3053d-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="3053d-294">Атрибут [Хттпоптионс] для предпечатных запросов</span><span class="sxs-lookup"><span data-stu-id="3053d-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="3053d-295">Когда CORS включается с соответствующей политикой, ASP.NET Core обычно отвечает на запросы на предпечатную CORS автоматически.</span><span class="sxs-lookup"><span data-stu-id="3053d-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="3053d-296">В некоторых сценариях это может быть не так.</span><span class="sxs-lookup"><span data-stu-id="3053d-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="3053d-297">Например, использование [CORS с маршрутизацией конечных точек](#ecors).</span><span class="sxs-lookup"><span data-stu-id="3053d-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="3053d-298">В следующем коде атрибут [[хттпоптионс]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) используется для создания конечных точек для запросов Options:</span><span class="sxs-lookup"><span data-stu-id="3053d-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="3053d-299">Инструкции по тестированию приведенного выше кода см. в статьях [тестирование CORS с маршрутизацией конечных точек и [хттпоптионс]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="3053d-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3053d-300">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="3053d-300">Set the preflight expiration time</span></span>

<span data-ttu-id="3053d-301">`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3053d-302">Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3053d-303">Принцип работы CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-303">How CORS works</span></span>

<span data-ttu-id="3053d-304">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.</span><span class="sxs-lookup"><span data-stu-id="3053d-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3053d-305">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="3053d-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="3053d-306">CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="3053d-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3053d-307">Например, вредоносный субъект может использовать [межсайтовые сценарии (XSS)](xref:security/cross-site-scripting) на сайте и выполнять межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.</span><span class="sxs-lookup"><span data-stu-id="3053d-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3053d-308">API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="3053d-309">Для применения CORS требуется клиент (браузер).</span><span class="sxs-lookup"><span data-stu-id="3053d-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3053d-310">Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="3053d-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3053d-311">Например, в любом из следующих средств будет отображаться ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="3053d-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3053d-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3053d-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3053d-313">Postman</span><span class="sxs-lookup"><span data-stu-id="3053d-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3053d-314">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="3053d-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3053d-315">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="3053d-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3053d-316">Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.</span><span class="sxs-lookup"><span data-stu-id="3053d-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3053d-317">Браузеры без CORS не могут выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="3053d-318">Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения.</span><span class="sxs-lookup"><span data-stu-id="3053d-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3053d-319">JSONP не использует XHR, он использует `<script>` тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="3053d-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3053d-320">Скрипты могут загружаться в разных источниках.</span><span class="sxs-lookup"><span data-stu-id="3053d-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3053d-321">В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3053d-322">Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3053d-323">Для включения CORS не требуется пользовательский код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3053d-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3053d-324">[Кнопка "вставить тест"](https://cors3.azurewebsites.net/test) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="3053d-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="3053d-325">Ниже приведен пример запроса на перекрестное происхождение из кнопки проверки [значений](https://cors3.azurewebsites.net/) в `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="3053d-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="3053d-326">`Origin`Заголовок:</span><span class="sxs-lookup"><span data-stu-id="3053d-326">The `Origin` header:</span></span>

* <span data-ttu-id="3053d-327">Предоставляет домен сайта, выполняющего запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="3053d-328">Параметр является обязательным и должен отличаться от узла.</span><span class="sxs-lookup"><span data-stu-id="3053d-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="3053d-329">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="3053d-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="3053d-330">**Заголовки ответа**</span><span class="sxs-lookup"><span data-stu-id="3053d-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="3053d-331">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="3053d-331">**Request headers**</span></span>

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

<span data-ttu-id="3053d-332">В `OPTIONS` запросах сервер задает заголовок заголовка **ответа** `Access-Control-Allow-Origin: {allowed origin}` в ответе.</span><span class="sxs-lookup"><span data-stu-id="3053d-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="3053d-333">Например [,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)запрос на нажатие кнопки [удалить [EnableCors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` содержит следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="3053d-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="3053d-334">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="3053d-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="3053d-335">**Заголовки ответа**</span><span class="sxs-lookup"><span data-stu-id="3053d-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="3053d-336">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="3053d-336">**Request headers**</span></span>

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

<span data-ttu-id="3053d-337">В предыдущих **заголовках ответа**сервер задает заголовок [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) в ответе.</span><span class="sxs-lookup"><span data-stu-id="3053d-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="3053d-338">`https://cors1.azurewebsites.net`Значение этого заголовка соответствует `Origin` заголовку запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="3053d-339">Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> вызывается метод, `Access-Control-Allow-Origin: *` возвращается значение с подстановочным знаком.</span><span class="sxs-lookup"><span data-stu-id="3053d-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="3053d-340">`AllowAnyOrigin`разрешает любой источник.</span><span class="sxs-lookup"><span data-stu-id="3053d-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="3053d-341">Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="3053d-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3053d-342">В частности, браузер не разрешает запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3053d-343">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.</span><span class="sxs-lookup"><span data-stu-id="3053d-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="3053d-344">Запросы параметров вывода</span><span class="sxs-lookup"><span data-stu-id="3053d-344">Display OPTIONS requests</span></span>

<span data-ttu-id="3053d-345">По умолчанию браузеры Chrome и ребра не отображают запросы параметров на вкладке Сеть средств F12.</span><span class="sxs-lookup"><span data-stu-id="3053d-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="3053d-346">Отображение запросов OPTIONS в следующих браузерах:</span><span class="sxs-lookup"><span data-stu-id="3053d-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="3053d-347">`chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="3053d-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="3053d-348">отключите флаг.</span><span class="sxs-lookup"><span data-stu-id="3053d-348">disable the flag.</span></span>
* <span data-ttu-id="3053d-349">перезагрузить.</span><span class="sxs-lookup"><span data-stu-id="3053d-349">restart.</span></span>

<span data-ttu-id="3053d-350">Firefox по умолчанию отображает запросы параметров.</span><span class="sxs-lookup"><span data-stu-id="3053d-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3053d-351">CORS в IIS</span><span class="sxs-lookup"><span data-stu-id="3053d-351">CORS in IIS</span></span>

<span data-ttu-id="3053d-352">При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="3053d-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3053d-353">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="3053d-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="3053d-354">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-354">Test CORS</span></span>

<span data-ttu-id="3053d-355">В [примере загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="3053d-356">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3053d-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="3053d-357">Пример представляет собой проект API с Razor добавленными страницами:</span><span class="sxs-lookup"><span data-stu-id="3053d-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="3053d-358">`WithOrigins("https://localhost:<port>");`следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3053d-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="3053d-359">Ниже `ValuesController` приведены конечные точки для тестирования.</span><span class="sxs-lookup"><span data-stu-id="3053d-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="3053d-360">[Мидисплайраутеинфо](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется пакетом NuGet для [Рик. документация. Samples. раутеинфо](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) и отображает сведения о маршруте.</span><span class="sxs-lookup"><span data-stu-id="3053d-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="3053d-361">Протестируйте предыдущий пример кода с помощью одного из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="3053d-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="3053d-362">Используйте развернутый пример приложения в [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="3053d-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="3053d-363">Нет необходимости скачивать пример.</span><span class="sxs-lookup"><span data-stu-id="3053d-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="3053d-364">Запустите пример с `dotnet run` использованием URL-адреса по умолчанию для `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="3053d-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="3053d-365">Запустите пример из Visual Studio с портом 44398 для URL-адреса `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="3053d-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="3053d-366">Использование браузера с инструментами F12:</span><span class="sxs-lookup"><span data-stu-id="3053d-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="3053d-367">Нажмите кнопку **значения** и проверьте заголовки на вкладке **сеть** .</span><span class="sxs-lookup"><span data-stu-id="3053d-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="3053d-368">Нажмите кнопку **Вставить тест** .</span><span class="sxs-lookup"><span data-stu-id="3053d-368">Select the **PUT test** button.</span></span> <span data-ttu-id="3053d-369">Инструкции по отображению запроса OPTIONS см. в разделе [Параметры отображения запросы](#options) .</span><span class="sxs-lookup"><span data-stu-id="3053d-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="3053d-370">**Тест размещения** создает два запроса: запрос на предварительную проверку параметров и запрос на размещение.</span><span class="sxs-lookup"><span data-stu-id="3053d-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="3053d-371">Нажмите **`GetValues2 [DisableCors]`** кнопку, чтобы активировать неудачный запрос CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="3053d-372">Как упоминалось в документе, ответ возвращает значение 200, но запрос CORS не выполняется.</span><span class="sxs-lookup"><span data-stu-id="3053d-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="3053d-373">Перейдите на вкладку **Console (консоль** ), чтобы просмотреть ошибку CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="3053d-374">В зависимости от браузера отображается сообщение об ошибке следующего вида:</span><span class="sxs-lookup"><span data-stu-id="3053d-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="3053d-375">Доступ к выборке `'https://cors1.azurewebsites.net/api/values/GetValues2'` из источника `'https://cors3.azurewebsites.net'` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".</span><span class="sxs-lookup"><span data-stu-id="3053d-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3053d-376">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="3053d-377">Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого [как](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler), или [POST](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="3053d-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3053d-378">При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3053d-379">Если запрос не имеет *источника* на основе значения `Origin` заголовка:</span><span class="sxs-lookup"><span data-stu-id="3053d-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3053d-380">Для обработки запроса по промежуточного слоя CORS не требуется.</span><span class="sxs-lookup"><span data-stu-id="3053d-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3053d-381">Заголовки CORS не возвращаются в ответе.</span><span class="sxs-lookup"><span data-stu-id="3053d-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="3053d-382">Следующая команда использует `curl` , чтобы выдать запрос Options с информацией:</span><span class="sxs-lookup"><span data-stu-id="3053d-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="3053d-383">Тестирование CORS с маршрутизацией конечных точек и [Хттпоптионс]</span><span class="sxs-lookup"><span data-stu-id="3053d-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="3053d-384">Включение CORS для отдельных конечных точек с помощью `RequireCors` в настоящее время **не** поддерживает [Автоматические предпечатные запросы](#apf).</span><span class="sxs-lookup"><span data-stu-id="3053d-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3053d-385">Рассмотрим следующий код, который использует [маршрутизацию конечных точек для включения CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="3053d-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="3053d-386">Ниже `TodoItems1Controller` приведены конечные точки для тестирования.</span><span class="sxs-lookup"><span data-stu-id="3053d-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="3053d-387">Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=1) в развернутом [примере](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span><span class="sxs-lookup"><span data-stu-id="3053d-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="3053d-388">Кнопки **Delete [EnableCors]** и **Get [EnableCors]** выполняются успешно, так как конечные точки имеют `[EnableCors]` и отвечают на предпечатные запросы.</span><span class="sxs-lookup"><span data-stu-id="3053d-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="3053d-389">Другие конечные точки не удается.</span><span class="sxs-lookup"><span data-stu-id="3053d-389">The other endpoints fails.</span></span> <span data-ttu-id="3053d-390">Не удается выполнить кнопку **Get** , так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:</span><span class="sxs-lookup"><span data-stu-id="3053d-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="3053d-391">Ниже `TodoItems2Controller` приведены аналогичные конечные точки, но включается явный код для реагирования на запросы Options:</span><span class="sxs-lookup"><span data-stu-id="3053d-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="3053d-392">Протестируйте предыдущий код на [странице тест](https://cors1.azurewebsites.net/test?number=2) в развернутом примере.</span><span class="sxs-lookup"><span data-stu-id="3053d-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="3053d-393">В раскрывающемся списке **контроллер** выберите Предварительная **Проверка** , а затем **Задайте значение контроллер**.</span><span class="sxs-lookup"><span data-stu-id="3053d-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="3053d-394">Все вызовы CORS к `TodoItems2Controller` конечным точкам выполнены.</span><span class="sxs-lookup"><span data-stu-id="3053d-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3053d-395">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3053d-395">Additional resources</span></span>

* [<span data-ttu-id="3053d-396">Общий доступ к ресурсам между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="3053d-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3053d-397">Приступая к работе с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3053d-398">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="3053d-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3053d-399">В этой статье показано, как включить CORS в приложении ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3053d-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3053d-400">Система безопасности браузера предотвращает запросы веб-страницы к другому домену, отличному от того, который обслуживает веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="3053d-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3053d-401">Это ограничение называется *политика одного источника*.</span><span class="sxs-lookup"><span data-stu-id="3053d-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3053d-402">Эта политика предотвращает чтение вредоносным сайтом конфиденциальных данных с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="3053d-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3053d-403">Иногда может потребоваться разрешить другим сайтам выполнять запросы между источниками в приложении.</span><span class="sxs-lookup"><span data-stu-id="3053d-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="3053d-404">Дополнительные сведения см. в [статье Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="3053d-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3053d-405">[Общий доступ к ресурсам в разных источниках](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="3053d-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3053d-406">— Это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="3053d-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3053d-407">**Не** является функцией безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="3053d-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3053d-408">Интерфейс API не обеспечивает безопасную поддержку CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3053d-409">Дополнительные сведения см. в разделе [как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="3053d-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3053d-410">Позволяет серверу явно разрешать некоторые запросы между источниками и отклонять другие.</span><span class="sxs-lookup"><span data-stu-id="3053d-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3053d-411">Является более безопасным и более гибким, чем более ранние методики, например [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="3053d-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3053d-412">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3053d-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3053d-413">Тот же источник</span><span class="sxs-lookup"><span data-stu-id="3053d-413">Same origin</span></span>

<span data-ttu-id="3053d-414">Два URL-адреса имеют одинаковый источник, если они имеют идентичные схемы, узлы и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="3053d-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3053d-415">Эти два URL-адреса имеют один и тот же источник:</span><span class="sxs-lookup"><span data-stu-id="3053d-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3053d-416">Эти URL-адреса имеют разные источники, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="3053d-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3053d-417">`https://example.net`: Другой домен</span><span class="sxs-lookup"><span data-stu-id="3053d-417">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="3053d-418">`https://www.example.com/foo.html`: Другой поддомен</span><span class="sxs-lookup"><span data-stu-id="3053d-418">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="3053d-419">`http://example.com/foo.html`: Другая схема</span><span class="sxs-lookup"><span data-stu-id="3053d-419">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="3053d-420">`https://example.com:9000/foo.html`: Другой порт</span><span class="sxs-lookup"><span data-stu-id="3053d-420">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="3053d-421">При сравнении источников Internet Explorer не учитывает порт.</span><span class="sxs-lookup"><span data-stu-id="3053d-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3053d-422">CORS с именованной политикой и по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3053d-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="3053d-423">По промежуточного слоя CORS обрабатывает запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3053d-424">Следующий код включает CORS для всего приложения с указанным источником:</span><span class="sxs-lookup"><span data-stu-id="3053d-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="3053d-425">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3053d-425">The preceding code:</span></span>

* <span data-ttu-id="3053d-426">Задает имя политики " \_ мялловспеЦификоригинс".</span><span class="sxs-lookup"><span data-stu-id="3053d-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="3053d-427">Имя политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="3053d-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3053d-428">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который включает CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="3053d-429">Вызывает <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [лямбда-выражение](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="3053d-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3053d-430">Лямбда-выражение принимает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> объект.</span><span class="sxs-lookup"><span data-stu-id="3053d-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3053d-431">[Параметры конфигурации](#cors-policy-options), такие как `WithOrigins` , описаны далее в этой статье.</span><span class="sxs-lookup"><span data-stu-id="3053d-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="3053d-432"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Вызов метода добавляет службы CORS в контейнер службы приложения:</span><span class="sxs-lookup"><span data-stu-id="3053d-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3053d-433">Дополнительные сведения см. в разделе [Параметры политики CORS](#cpo) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="3053d-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="3053d-434"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Метод может привести к цепочке методов, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3053d-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="3053d-435">Примечание. URL-адрес **не** должен содержать косую черту ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="3053d-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3053d-436">Если URL-адрес завершается с `/` , то сравнение возвращает значение `false` и заголовок не возвращается.</span><span class="sxs-lookup"><span data-stu-id="3053d-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="3053d-437">Следующий код применяет политики CORS ко всем конечным точкам приложений через по промежуточного слоя CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="3053d-438">Примечание. `UseCors` необходимо вызвать до `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="3053d-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="3053d-439">См. раздел [Включение CORS на Razor страницах, контроллерах и методах действий](#ecors) для применения политики CORS на уровне страницы, контроллера или действия.</span><span class="sxs-lookup"><span data-stu-id="3053d-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="3053d-440">Инструкции по тестированию кода, аналогичные приведенному выше, см. в разделе [тестирование CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="3053d-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3053d-441">Включение CORS с помощью атрибутов</span><span class="sxs-lookup"><span data-stu-id="3053d-441">Enable CORS with attributes</span></span>

<span data-ttu-id="3053d-442">Атрибут [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) предоставляет альтернативу глобальному применению CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3053d-443">`[EnableCors]`Атрибут включает CORS для выбранных конечных точек, а не всех конечных точек.</span><span class="sxs-lookup"><span data-stu-id="3053d-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="3053d-444">Используйте `[EnableCors]` , чтобы указать политику по умолчанию и `[EnableCors("{Policy String}")]` указать политику.</span><span class="sxs-lookup"><span data-stu-id="3053d-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="3053d-445">`[EnableCors]`Атрибут может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="3053d-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="3053d-446">Страниц`PageModel`</span><span class="sxs-lookup"><span data-stu-id="3053d-446"> Page `PageModel`</span></span>
* <span data-ttu-id="3053d-447">Контроллер</span><span class="sxs-lookup"><span data-stu-id="3053d-447">Controller</span></span>
* <span data-ttu-id="3053d-448">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="3053d-448">Controller action method</span></span>

<span data-ttu-id="3053d-449">С атрибутом можно применить различные политики для контроллера, модели страницы или действия `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="3053d-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="3053d-450">Если `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, а CORS включен по промежуточного слоя, применяются **обе** политики.</span><span class="sxs-lookup"><span data-stu-id="3053d-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="3053d-451">**Не** рекомендуется объединять политики.</span><span class="sxs-lookup"><span data-stu-id="3053d-451">We recommend **not** combining policies.</span></span> <span data-ttu-id="3053d-452">Используйте `[EnableCors]` атрибут или по промежуточного слоя, а **не оба**.</span><span class="sxs-lookup"><span data-stu-id="3053d-452">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="3053d-453">При использовании не `[EnableCors]` определяйте **not** политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3053d-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="3053d-454">Следующий код применяет к каждому методу другую политику:</span><span class="sxs-lookup"><span data-stu-id="3053d-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3053d-455">Следующий код создает политику CORS по умолчанию и политику с именем `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="3053d-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="3053d-456">Отключение CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-456">Disable CORS</span></span>

<span data-ttu-id="3053d-457">Атрибут [ &lbrack; ДИСАБЛЕКОРС &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отключает CORS для контроллера, модели страницы или действия.</span><span class="sxs-lookup"><span data-stu-id="3053d-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3053d-458">Параметры политики CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-458">CORS policy options</span></span>

<span data-ttu-id="3053d-459">В этом разделе описаны различные параметры, которые можно задать в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3053d-460">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="3053d-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3053d-461">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="3053d-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3053d-462">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="3053d-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3053d-463">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="3053d-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3053d-464">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="3053d-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3053d-465">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="3053d-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3053d-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>метод вызывается в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3053d-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3053d-467">Для некоторых параметров может оказаться полезным сначала ознакомиться с разделом " [работа CORS](#how-cors) ".</span><span class="sxs-lookup"><span data-stu-id="3053d-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3053d-468">Установка разрешенных источников</span><span class="sxs-lookup"><span data-stu-id="3053d-468">Set the allowed origins</span></span>

<span data-ttu-id="3053d-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Разрешает запросы CORS из всех источников с любой схемой ( `http` или `https` ).</span><span class="sxs-lookup"><span data-stu-id="3053d-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3053d-470">`AllowAnyOrigin`не является безопасным, так как *любой веб-сайт* может выполнять запросы между источниками в приложение.</span><span class="sxs-lookup"><span data-stu-id="3053d-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3053d-471">Указание `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке межсайтовых запросов.</span><span class="sxs-lookup"><span data-stu-id="3053d-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3053d-472">Для безопасного приложения укажите точный список источников, если клиент должен авторизоваться для доступа к ресурсам сервера.</span><span class="sxs-lookup"><span data-stu-id="3053d-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="3053d-473">`AllowAnyOrigin`влияет на предпечатные запросы и `Access-Control-Allow-Origin` заголовок.</span><span class="sxs-lookup"><span data-stu-id="3053d-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3053d-474">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3053d-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Задает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, которая позволяет источникам соответствовать настроенному домену с подстановочными знаками при оценке того, разрешен ли источник.</span><span class="sxs-lookup"><span data-stu-id="3053d-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3053d-476">Задание допустимых методов HTTP</span><span class="sxs-lookup"><span data-stu-id="3053d-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3053d-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3053d-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3053d-478">Разрешает любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="3053d-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="3053d-479">Влияет на предпечатные запросы и `Access-Control-Allow-Methods` заголовок.</span><span class="sxs-lookup"><span data-stu-id="3053d-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3053d-480">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3053d-481">Задание разрешенных заголовков запроса</span><span class="sxs-lookup"><span data-stu-id="3053d-481">Set the allowed request headers</span></span>

<span data-ttu-id="3053d-482">Чтобы разрешить отправку конкретных заголовков в запрос CORS, именуемый *заголовком запроса на создание*, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="3053d-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3053d-483">Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3053d-484">Этот параметр влияет на предпечатные запросы и `Access-Control-Request-Headers` заголовок.</span><span class="sxs-lookup"><span data-stu-id="3053d-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="3053d-485">Дополнительные сведения см. в разделе [предпечатные запросы](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="3053d-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3053d-486">По промежуточного слоя CORS всегда позволяет отправлять четыре заголовка в, `Access-Control-Request-Headers` независимо от значений, настроенных в корсполици. Headers.</span><span class="sxs-lookup"><span data-stu-id="3053d-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="3053d-487">Этот список заголовков включает:</span><span class="sxs-lookup"><span data-stu-id="3053d-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="3053d-488">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3053d-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="3053d-489">По промежуточного слоя CORS успешно реагирует на предварительный запрос со следующим заголовком запроса, поскольку `Content-Language` всегда имеет значение список разрешений:</span><span class="sxs-lookup"><span data-stu-id="3053d-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3053d-490">Задание предоставленных заголовков ответа</span><span class="sxs-lookup"><span data-stu-id="3053d-490">Set the exposed response headers</span></span>

<span data-ttu-id="3053d-491">По умолчанию браузер не предоставляет все заголовки ответа приложению.</span><span class="sxs-lookup"><span data-stu-id="3053d-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3053d-492">Дополнительные сведения см. в разделе [общий доступ к ресурсам между источниками W3C (терминология): простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="3053d-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3053d-493">По умолчанию доступны заголовки ответов:</span><span class="sxs-lookup"><span data-stu-id="3053d-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3053d-494">Спецификация CORS вызывает эти заголовки *простых заголовков ответа*.</span><span class="sxs-lookup"><span data-stu-id="3053d-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3053d-495">Чтобы сделать другие заголовки доступными для приложения, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3053d-496">Учетные данные в запросах между источниками</span><span class="sxs-lookup"><span data-stu-id="3053d-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3053d-497">Учетные данные требует специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3053d-498">По умолчанию браузер не отправляет учетные данные с запросом между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3053d-499">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="3053d-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3053d-500">Чтобы отправить учетные данные с запросом между источниками, клиент должен установить `XMLHttpRequest.withCredentials` в значение `true` .</span><span class="sxs-lookup"><span data-stu-id="3053d-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3053d-501">Использование `XMLHttpRequest` напрямую:</span><span class="sxs-lookup"><span data-stu-id="3053d-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3053d-502">Использование jQuery:</span><span class="sxs-lookup"><span data-stu-id="3053d-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3053d-503">Использование [API выборки](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="3053d-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3053d-504">Сервер должен разрешать учетные данные.</span><span class="sxs-lookup"><span data-stu-id="3053d-504">The server must allow the credentials.</span></span> <span data-ttu-id="3053d-505">Чтобы разрешить учетные данные для разных источников, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="3053d-506">HTTP-ответ содержит `Access-Control-Allow-Credentials` заголовок, который сообщает браузеру, что сервер разрешает учетные данные для запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3053d-507">Если браузер отправляет учетные данные, но ответ не включает допустимый `Access-Control-Allow-Credentials` заголовок, браузер не предоставляет ответ на приложение, и запрос на перекрестное происхождение завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="3053d-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3053d-508">Разрешение учетных данных между источниками является угрозой безопасности.</span><span class="sxs-lookup"><span data-stu-id="3053d-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3053d-509">Веб-сайт в другом домене может отправить учетные данные пользователя, выполнившего вход, в приложение от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="3053d-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3053d-510">В спецификации CORS также указывается, что `"*"` при наличии заголовка недопустимыми являются исходные значения (все источники) `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="3053d-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="3053d-511">Предпечатные запросы</span><span class="sxs-lookup"><span data-stu-id="3053d-511">Preflight requests</span></span>

<span data-ttu-id="3053d-512">Для некоторых запросов CORS браузер отправляет дополнительный запрос перед выполнением фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="3053d-513">Этот запрос называется *предпечатным запросом*.</span><span class="sxs-lookup"><span data-stu-id="3053d-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="3053d-514">Браузер может пропустить Предпечатный запрос, если выполняются следующие условия.</span><span class="sxs-lookup"><span data-stu-id="3053d-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="3053d-515">Метод запроса — GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="3053d-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3053d-516">Приложение не устанавливает заголовки запроса `Accept` , кроме, `Accept-Language` , `Content-Language` , `Content-Type` или `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="3053d-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3053d-517">`Content-Type`Заголовок, если он задан, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="3053d-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3053d-518">Правило для заголовков запросов, заданных для запроса клиента, применяется к заголовкам, которые устанавливаются приложением путем вызова `setRequestHeader` для `XMLHttpRequest` объекта.</span><span class="sxs-lookup"><span data-stu-id="3053d-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3053d-519">Спецификация CORS вызывает заголовки *запроса автора*заголовков.</span><span class="sxs-lookup"><span data-stu-id="3053d-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="3053d-520">Правило не применяется к заголовкам, которые может задать браузер, например `User-Agent` , `Host` или `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="3053d-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3053d-521">Ниже приведен пример предпечатного запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="3053d-522">Запрос перед рейсом использует метод HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="3053d-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="3053d-523">Он включает два специальных заголовка:</span><span class="sxs-lookup"><span data-stu-id="3053d-523">It includes two special headers:</span></span>

* <span data-ttu-id="3053d-524">`Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3053d-525">`Access-Control-Request-Headers`: Список заголовков запросов, которые приложение задает для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3053d-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3053d-526">Как упоминалось ранее, в него не входят заголовки, заданных браузером, например `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="3053d-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="3053d-527">При включении CORS с соответствующей политикой ASP.NET Core обычно автоматически реагирует на предпечатные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="3053d-528">См. [атрибут [хттпоптионс] для предпечатных запросов](#pro).</span><span class="sxs-lookup"><span data-stu-id="3053d-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="3053d-529">Предпечатный запрос CORS может включать `Access-Control-Request-Headers` заголовок, указывающий серверу на заголовки, отправляемые с фактическим запросом.</span><span class="sxs-lookup"><span data-stu-id="3053d-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="3053d-530">Чтобы разрешить определенные заголовки, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3053d-531">Чтобы разрешить все заголовки запроса автора, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3053d-532">Браузеры не полностью согласуются с тем, как они заданы `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="3053d-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3053d-533">Если для заголовков задано значение `"*"` , отличное от (или использование <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), следует включить как минимум `Accept` , `Content-Type` , и `Origin` , а также любые настраиваемые заголовки, которые требуется поддерживать.</span><span class="sxs-lookup"><span data-stu-id="3053d-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="3053d-534">Ниже приведен пример ответа на Предпечатный запрос (при условии, что сервер разрешает запрос):</span><span class="sxs-lookup"><span data-stu-id="3053d-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="3053d-535">Ответ содержит `Access-Control-Allow-Methods` заголовок со списком допустимых методов и, при необходимости `Access-Control-Allow-Headers` , заголовок, в котором перечислены разрешенные заголовки.</span><span class="sxs-lookup"><span data-stu-id="3053d-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="3053d-536">Если Предпечатный запрос выполнен, браузер отправляет фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="3053d-537">Если Предпечатный запрос отклонен, приложение возвращает ответ *ок 200* , но не ОТПРАВЛЯЕТ заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="3053d-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3053d-538">Поэтому браузер не пытается выполнить запрос между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3053d-539">Задать срок действия предпечатного срока</span><span class="sxs-lookup"><span data-stu-id="3053d-539">Set the preflight expiration time</span></span>

<span data-ttu-id="3053d-540">`Access-Control-Max-Age`Заголовок указывает время, в течение которого может быть кэширован ответ на Предпечатный запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3053d-541">Чтобы задать этот заголовок, вызовите <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="3053d-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3053d-542">Принцип работы CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-542">How CORS works</span></span>

<span data-ttu-id="3053d-543">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне HTTP-сообщений.</span><span class="sxs-lookup"><span data-stu-id="3053d-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3053d-544">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="3053d-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="3053d-545">CORS — это стандарт консорциума W3C, позволяющий серверу ослабить политику того же источника.</span><span class="sxs-lookup"><span data-stu-id="3053d-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3053d-546">Например, вредоносный субъект может использовать для веб [-узла предотвращение межсайтовых сценариев (XSS)](xref:security/cross-site-scripting) и выполнить межсайтовый запрос к сайту с поддержкой CORS, чтобы украсть информацию.</span><span class="sxs-lookup"><span data-stu-id="3053d-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3053d-547">Интерфейс API не обеспечивает безопасную работу, разрешая CORS.</span><span class="sxs-lookup"><span data-stu-id="3053d-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="3053d-548">Для применения CORS требуется клиент (браузер).</span><span class="sxs-lookup"><span data-stu-id="3053d-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3053d-549">Сервер выполняет запрос и возвращает ответ. это клиент, который возвращает сообщение об ошибке и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="3053d-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3053d-550">Например, в любом из следующих средств будет отображаться ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="3053d-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3053d-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3053d-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3053d-552">Postman</span><span class="sxs-lookup"><span data-stu-id="3053d-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3053d-553">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="3053d-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3053d-554">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="3053d-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3053d-555">Сервер может позволить обозревателям выполнять запросы API [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) , которые в противном случае будут запрещены.</span><span class="sxs-lookup"><span data-stu-id="3053d-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3053d-556">Браузеры (без CORS) не могут выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="3053d-557">Перед CORS использовалась технология [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) для обхода этого ограничения.</span><span class="sxs-lookup"><span data-stu-id="3053d-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3053d-558">JSONP не использует XHR, он использует `<script>` тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="3053d-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3053d-559">Скрипты могут загружаться в разных источниках.</span><span class="sxs-lookup"><span data-stu-id="3053d-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3053d-560">В [спецификации CORS](https://www.w3.org/TR/cors/) появились несколько новых HTTP-заголовков, которые позволяют выполнять запросы между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3053d-561">Если браузер поддерживает CORS, эти заголовки автоматически устанавливаются для запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3053d-562">Для включения CORS не требуется пользовательский код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3053d-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3053d-563">Ниже приведен пример запроса между источниками.</span><span class="sxs-lookup"><span data-stu-id="3053d-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="3053d-564">`Origin`Заголовок предоставляет домен сайта, выполняющего запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="3053d-565">`Origin`Заголовок является обязательным и должен отличаться от узла.</span><span class="sxs-lookup"><span data-stu-id="3053d-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="3053d-566">Если сервер разрешает запрос, он устанавливает `Access-Control-Allow-Origin` заголовок в ответе.</span><span class="sxs-lookup"><span data-stu-id="3053d-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="3053d-567">Значение этого заголовка либо соответствует `Origin` заголовку запроса, либо является подстановочным значением `"*"` , что означает, что любой источник разрешен:</span><span class="sxs-lookup"><span data-stu-id="3053d-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="3053d-568">Если ответ не содержит `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение завершается с ошибкой.</span><span class="sxs-lookup"><span data-stu-id="3053d-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3053d-569">В частности, браузер не разрешает запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3053d-570">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным клиентскому приложению.</span><span class="sxs-lookup"><span data-stu-id="3053d-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="3053d-571">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-571">Test CORS</span></span>

<span data-ttu-id="3053d-572">Тестирование CORS:</span><span class="sxs-lookup"><span data-stu-id="3053d-572">To test CORS:</span></span>

1. <span data-ttu-id="3053d-573">[Создайте проект API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="3053d-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="3053d-574">Кроме того, можно [загрузить пример](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3053d-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="3053d-575">Включите CORS с помощью одного из подходов, описанных в этом документе.</span><span class="sxs-lookup"><span data-stu-id="3053d-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="3053d-576">Пример.</span><span class="sxs-lookup"><span data-stu-id="3053d-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="3053d-577">`WithOrigins("https://localhost:<port>");`следует использовать только для тестирования примера приложения, аналогичного [образцу кода для скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3053d-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="3053d-578">Создайте проект веб-приложения ( Razor страницы или MVC).</span><span class="sxs-lookup"><span data-stu-id="3053d-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="3053d-579">В образце используются Razor страницы.</span><span class="sxs-lookup"><span data-stu-id="3053d-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="3053d-580">Вы можете создать веб-приложение в том же решении, что и проект API.</span><span class="sxs-lookup"><span data-stu-id="3053d-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="3053d-581">Добавьте выделенный ниже код в файл *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3053d-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="3053d-582">В приведенном выше коде замените `url: 'https://<web app>.azurewebsites.net/api/values/1',` URL-адресом развернутого приложения.</span><span class="sxs-lookup"><span data-stu-id="3053d-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="3053d-583">Разверните проект API.</span><span class="sxs-lookup"><span data-stu-id="3053d-583">Deploy the API project.</span></span> <span data-ttu-id="3053d-584">Например, выполните [развертывание в Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="3053d-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="3053d-585">Запустите Razor страницы или приложение MVC на рабочем столе и нажмите кнопку **Test (тест** ).</span><span class="sxs-lookup"><span data-stu-id="3053d-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="3053d-586">Используйте средства F12 для просмотра сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="3053d-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="3053d-587">Удалите источник localhost из `WithOrigins` и разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="3053d-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="3053d-588">Кроме того, можно запустить клиентское приложение с другим портом.</span><span class="sxs-lookup"><span data-stu-id="3053d-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="3053d-589">Например, запустите из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3053d-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="3053d-590">Протестируйте клиентское приложение.</span><span class="sxs-lookup"><span data-stu-id="3053d-590">Test with the client app.</span></span> <span data-ttu-id="3053d-591">Ошибки CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3053d-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="3053d-592">Чтобы просмотреть ошибку, используйте вкладку консоль в средствах F12.</span><span class="sxs-lookup"><span data-stu-id="3053d-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="3053d-593">В зависимости от браузера вы получаете ошибку (в консоли средств F12), как показано ниже:</span><span class="sxs-lookup"><span data-stu-id="3053d-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="3053d-594">Использование Microsoft ребр:</span><span class="sxs-lookup"><span data-stu-id="3053d-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="3053d-595">**SEC7120: [CORS] `https://localhost:44375` не удалось найти источник `https://localhost:44375` в заголовке ответа Access-Control-Allow-Origin для ресурса-источника в`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="3053d-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="3053d-596">Использование Chrome:</span><span class="sxs-lookup"><span data-stu-id="3053d-596">Using Chrome:</span></span>

     <span data-ttu-id="3053d-597">**Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` из источника `https://localhost:44375` заблокирован политикой CORS: в запрошенном ресурсе отсутствует заголовок "Access-Control-Allow-Origin".**</span><span class="sxs-lookup"><span data-stu-id="3053d-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="3053d-598">Конечные точки с поддержкой CORS можно тестировать с помощью средства, такого как [Fiddler](https://www.telerik.com/fiddler) или [POST](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="3053d-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3053d-599">При использовании средства источник запроса, указанный в `Origin` заголовке, должен отличаться от узла, получающего запрос.</span><span class="sxs-lookup"><span data-stu-id="3053d-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3053d-600">Если запрос не имеет *источника* на основе значения `Origin` заголовка:</span><span class="sxs-lookup"><span data-stu-id="3053d-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3053d-601">Для обработки запроса по промежуточного слоя CORS не требуется.</span><span class="sxs-lookup"><span data-stu-id="3053d-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3053d-602">Заголовки CORS не возвращаются в ответе.</span><span class="sxs-lookup"><span data-stu-id="3053d-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3053d-603">CORS в IIS</span><span class="sxs-lookup"><span data-stu-id="3053d-603">CORS in IIS</span></span>

<span data-ttu-id="3053d-604">При развертывании в IIS CORS необходимо запустить перед проверкой подлинности Windows, если сервер не настроен на разрешение анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="3053d-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3053d-605">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="3053d-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3053d-606">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3053d-606">Additional resources</span></span>

* [<span data-ttu-id="3053d-607">Общий доступ к ресурсам между источниками (CORS)</span><span class="sxs-lookup"><span data-stu-id="3053d-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3053d-608">Приступая к работе с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="3053d-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
