---
title: Включить запросы cross-Origin (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как CORS как стандарт для разрешения или отклонения запросов на кросс-происхождение в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642697"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="3b04c-103">Включить запросы cross-Origin (CORS) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b04c-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b04c-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="3b04c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="3b04c-105">В этой статье показано, как включить CORS в ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="3b04c-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3b04c-106">Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="3b04c-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3b04c-107">Это ограничение называется *политикой одного и того же происхождения.*</span><span class="sxs-lookup"><span data-stu-id="3b04c-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3b04c-108">Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="3b04c-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3b04c-109">Иногда может потребоваться разрешить другим сайтам делать запросы на кросс-происхождение в ваше приложение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="3b04c-110">Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)</span><span class="sxs-lookup"><span data-stu-id="3b04c-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3b04c-111">[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="3b04c-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3b04c-112">Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3b04c-113">Это **не** функция безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="3b04c-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3b04c-114">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3b04c-115">Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="3b04c-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3b04c-116">Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.</span><span class="sxs-lookup"><span data-stu-id="3b04c-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3b04c-117">Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="3b04c-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3b04c-118">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3b04c-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3b04c-119">То же происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-119">Same origin</span></span>

<span data-ttu-id="3b04c-120">Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="3b04c-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3b04c-121">Эти два URL-адреса имеют одно и то же происхождение:</span><span class="sxs-lookup"><span data-stu-id="3b04c-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3b04c-122">Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="3b04c-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3b04c-123">`https://example.net`&ndash; Различные домены</span><span class="sxs-lookup"><span data-stu-id="3b04c-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="3b04c-124">`https://www.example.com/foo.html`&ndash; Различные поддомены</span><span class="sxs-lookup"><span data-stu-id="3b04c-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="3b04c-125">`http://example.com/foo.html`&ndash; Различная схема</span><span class="sxs-lookup"><span data-stu-id="3b04c-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="3b04c-126">`https://example.com:9000/foo.html`&ndash; Различные порты</span><span class="sxs-lookup"><span data-stu-id="3b04c-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="3b04c-127">Включение CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-127">Enable CORS</span></span>

<span data-ttu-id="3b04c-128">Существует три способа включить CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="3b04c-129">В промежуточном программном обеспечении с использованием [политики именованного](#np) значения или [политики по умолчанию.](#dp)</span><span class="sxs-lookup"><span data-stu-id="3b04c-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="3b04c-130">Использование [конечных точек.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3b04c-131">С атрибутом [«EnableCors».](#attr)</span><span class="sxs-lookup"><span data-stu-id="3b04c-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="3b04c-132">Использование атрибута [«EnableCors»](#attr) с именем политики обеспечивает лучший контроль в ограничении конечных точек, поддерживающих CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="3b04c-133">Каждый подход подробно описан в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="3b04c-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3b04c-134">CORS с названной политикой и программой</span><span class="sxs-lookup"><span data-stu-id="3b04c-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="3b04c-135">CORS Middleware обрабатывает запросы по перекрестному происхождению.</span><span class="sxs-lookup"><span data-stu-id="3b04c-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3b04c-136">Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными истоками:</span><span class="sxs-lookup"><span data-stu-id="3b04c-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="3b04c-137">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3b04c-137">The preceding code:</span></span>

* <span data-ttu-id="3b04c-138">Устанавливает имя политики на `_myAllowSpecificOrigins`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="3b04c-139">Название политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="3b04c-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3b04c-140">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и определяет `_myAllowSpecificOrigins` политику CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="3b04c-141">`UseCors`добавляет промежуточное программное обеспечение CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="3b04c-142">Вызов `UseCors` должен быть размещен `UseRouting`после, `UseAuthorization`но до .</span><span class="sxs-lookup"><span data-stu-id="3b04c-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="3b04c-143">Для получения дополнительной [Middleware order](xref:fundamentals/middleware/index#middleware-order)информации см.</span><span class="sxs-lookup"><span data-stu-id="3b04c-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="3b04c-144">Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="3b04c-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3b04c-145">Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет.</span><span class="sxs-lookup"><span data-stu-id="3b04c-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3b04c-146">[Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.</span><span class="sxs-lookup"><span data-stu-id="3b04c-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="3b04c-147">Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера.</span><span class="sxs-lookup"><span data-stu-id="3b04c-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="3b04c-148">[См. конечную точку,](#ecors) чтобы применить политику CORS к определенным конечным точкам.</span><span class="sxs-lookup"><span data-stu-id="3b04c-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="3b04c-149">При приведении в конечный пункт разгром промежуточное программное `UseRouting` `UseEndpoints`обеспечение CORS ***должно*** быть настроено для выполнения между вызовами и .</span><span class="sxs-lookup"><span data-stu-id="3b04c-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="3b04c-150">Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="3b04c-151">Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:</span><span class="sxs-lookup"><span data-stu-id="3b04c-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3b04c-152">Для получения дополнительной информации в этом документе [ознакомьтесь с вариантами политики CORS.](#cpo)</span><span class="sxs-lookup"><span data-stu-id="3b04c-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="3b04c-153">Методы <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> могут быть прикованы, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3b04c-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="3b04c-154">Примечание: Указанный URL **не** должен содержать`/`задняя черта ().</span><span class="sxs-lookup"><span data-stu-id="3b04c-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3b04c-155">Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="3b04c-156">CORS с политикой по умолчанию и программами среднего</span><span class="sxs-lookup"><span data-stu-id="3b04c-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="3b04c-157">Следующий выделенный код позволяет политику CORS по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3b04c-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="3b04c-158">Предыдущий код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.</span><span class="sxs-lookup"><span data-stu-id="3b04c-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="3b04c-159">Включение CORS с маршрутизацией конечных точек</span><span class="sxs-lookup"><span data-stu-id="3b04c-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="3b04c-160">Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf)</span><span class="sxs-lookup"><span data-stu-id="3b04c-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3b04c-161">Для получения дополнительной информации, [см. этот вопрос GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тест CORS с конечным пунктом разгрома и "HttpOptions"](#tcer).</span><span class="sxs-lookup"><span data-stu-id="3b04c-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="3b04c-162">При конечной конечной конечной реунетировке CORS может <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> быть включен на основе конечной точки с использованием набора методов расширения:</span><span class="sxs-lookup"><span data-stu-id="3b04c-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="3b04c-163">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3b04c-163">In the preceding code:</span></span>

* <span data-ttu-id="3b04c-164">`app.UseCors`позволяет CORS промежуточного посуды.</span><span class="sxs-lookup"><span data-stu-id="3b04c-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="3b04c-165">Поскольку политика по умолчанию не `app.UseCors()` настроена, само по себе не позволяет CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="3b04c-166">Конечные `/echo` точки контроллера и контроллера позволяют запросы на перекрестное происхождение с помощью указанной политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="3b04c-167">Конечные `/echo2` точки Страниц ы и страниц ы бритвы ***не*** разрешают запросы на перекрестное происхождение, поскольку политика по умолчанию не указана.</span><span class="sxs-lookup"><span data-stu-id="3b04c-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="3b04c-168">Атрибут [«DisableCors»](#dc) ***не*** отменяет CORS, который был включен путем `RequireCors`входиной раритетной коррески с помощью.</span><span class="sxs-lookup"><span data-stu-id="3b04c-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="3b04c-169">Ознакомиться с инструкциями по коду тестирования, аналогичным предыдущим, можно ознакомиться [с тестовым CORS с конечным точечным и «HttpOptions».](#tcer)</span><span class="sxs-lookup"><span data-stu-id="3b04c-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3b04c-170">Включить CORS с атрибутами</span><span class="sxs-lookup"><span data-stu-id="3b04c-170">Enable CORS with attributes</span></span>

<span data-ttu-id="3b04c-171">Включение CORS с атрибутом [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение политики с именем только к тем конечным точкам, которые требуют CORS, обеспечивает лучший контроль.</span><span class="sxs-lookup"><span data-stu-id="3b04c-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="3b04c-172">Атрибут [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру.</span><span class="sxs-lookup"><span data-stu-id="3b04c-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3b04c-173">Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек:</span><span class="sxs-lookup"><span data-stu-id="3b04c-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="3b04c-174">`[EnableCors]`определяет политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b04c-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="3b04c-175">`[EnableCors("{Policy String}")]`определяет именованные политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="3b04c-176">Атрибут `[EnableCors]` может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="3b04c-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="3b04c-177">Страница бритвы`PageModel`</span><span class="sxs-lookup"><span data-stu-id="3b04c-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="3b04c-178">Контроллер</span><span class="sxs-lookup"><span data-stu-id="3b04c-178">Controller</span></span>
* <span data-ttu-id="3b04c-179">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="3b04c-179">Controller action method</span></span>

<span data-ttu-id="3b04c-180">Различные политики могут быть применены к контроллерам, `[EnableCors]` моделям страниц или методам действий с атрибутом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="3b04c-181">Когда `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, и CORS включен в промежуточном программном обеспечении, ***обе*** политики применяются.</span><span class="sxs-lookup"><span data-stu-id="3b04c-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="3b04c-182">***Мы рекомендуем не сочетать политики. Используйте*** ***атрибут или промежуточное программное обеспечение, а не оба в том же приложении.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="3b04c-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="3b04c-183">Следующий код применяет различную политику к каждому методу:</span><span class="sxs-lookup"><span data-stu-id="3b04c-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3b04c-184">Следующий код создает две политики CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="3b04c-185">Для лучшего контроля за ограничением запросов CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="3b04c-186">Используйте `[EnableCors("MyPolicy")]` с именем политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="3b04c-187">Не определяйте политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b04c-187">Don't define a default policy.</span></span>
* <span data-ttu-id="3b04c-188">Не используйте [конечную точку.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3b04c-189">Код в следующем разделе соответствует предыдущему списку.</span><span class="sxs-lookup"><span data-stu-id="3b04c-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="3b04c-190">Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="3b04c-191">Отключить CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-191">Disable CORS</span></span>

<span data-ttu-id="3b04c-192">Атрибут [«DisableCors»](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***не*** отменяет CORS, который был включен путем [разгрома конечных точек.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="3b04c-193">Следующий код определяет политику `"MyPolicy"`CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="3b04c-194">Следующий код отменяет CORS `GetValues2` для действия:</span><span class="sxs-lookup"><span data-stu-id="3b04c-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="3b04c-195">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3b04c-195">The preceding code:</span></span>

* <span data-ttu-id="3b04c-196">Не позволяет CORS с [конечной точкий разгрома.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="3b04c-197">Не определяет [политику CORS по умолчанию.](#dp)</span><span class="sxs-lookup"><span data-stu-id="3b04c-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="3b04c-198">Использует [«EnableCors» («MyPolicy»)](#attr) для включения политики `"MyPolicy"` CORS для контроллера.</span><span class="sxs-lookup"><span data-stu-id="3b04c-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="3b04c-199">Отражает CORS `GetValues2` для метода.</span><span class="sxs-lookup"><span data-stu-id="3b04c-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="3b04c-200">Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [в Test CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="3b04c-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3b04c-201">Варианты политики CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-201">CORS policy options</span></span>

<span data-ttu-id="3b04c-202">В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3b04c-203">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3b04c-204">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="3b04c-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3b04c-205">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3b04c-206">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="3b04c-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3b04c-207">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="3b04c-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3b04c-208">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="3b04c-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3b04c-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в .</span><span class="sxs-lookup"><span data-stu-id="3b04c-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b04c-210">Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.</span><span class="sxs-lookup"><span data-stu-id="3b04c-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3b04c-211">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-211">Set the allowed origins</span></span>

<span data-ttu-id="3b04c-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`).</span><span class="sxs-lookup"><span data-stu-id="3b04c-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3b04c-213">`AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3b04c-214">Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт.</span><span class="sxs-lookup"><span data-stu-id="3b04c-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3b04c-215">Служба CORS возвращает недействительный ответ CORS, когда приложение настроено с помощью обоих методов.</span><span class="sxs-lookup"><span data-stu-id="3b04c-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="3b04c-216">`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3b04c-217">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3b04c-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3b04c-219">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="3b04c-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3b04c-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3b04c-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3b04c-221">Позволяет любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="3b04c-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="3b04c-222">Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3b04c-223">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3b04c-224">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-224">Set the allowed request headers</span></span>

<span data-ttu-id="3b04c-225">Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали [заголовки запросов автора,](https://xhr.spec.whatwg.org/#request)позвоните и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="3b04c-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3b04c-226">Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="3b04c-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3b04c-227">`AllowAnyHeader`влияет на предполетные запросы и заголовок [заголовков Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="3b04c-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="3b04c-228">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3b04c-229">Соответствие политики CORS Middleware к `WithHeaders` определенным заголовкам, указанному в, возможно только в том случае, если заголовки, отправленные в `Access-Control-Request-Headers` точном соответствии заголовкам, указанным в. `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="3b04c-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="3b04c-230">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3b04c-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="3b04c-231">CORS Middleware отклоняет предварительный запрос со `Content-Language` следующим заголовком запроса, потому `WithHeaders`что ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) не указан в:</span><span class="sxs-lookup"><span data-stu-id="3b04c-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="3b04c-232">Приложение возвращает *200 OK* ответ, но не отправить CORS заголовки обратно.</span><span class="sxs-lookup"><span data-stu-id="3b04c-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3b04c-233">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3b04c-234">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="3b04c-234">Set the exposed response headers</span></span>

<span data-ttu-id="3b04c-235">По умолчанию браузер не предоставляет приложению все заголовки ответов.</span><span class="sxs-lookup"><span data-stu-id="3b04c-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3b04c-236">Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="3b04c-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3b04c-237">Заголовки ответов, доступные по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3b04c-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3b04c-238">Спецификация CORS называет эти заголовки *простыми заголовками ответов.*</span><span class="sxs-lookup"><span data-stu-id="3b04c-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3b04c-239">Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3b04c-240">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="3b04c-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3b04c-241">Учетные данные требуют специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3b04c-242">По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3b04c-243">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b04c-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3b04c-244">Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3b04c-245">Использование `XMLHttpRequest` непосредственно:</span><span class="sxs-lookup"><span data-stu-id="3b04c-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3b04c-246">Используя j'ери:</span><span class="sxs-lookup"><span data-stu-id="3b04c-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3b04c-247">Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="3b04c-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3b04c-248">Сервер должен разрешить учетные данные.</span><span class="sxs-lookup"><span data-stu-id="3b04c-248">The server must allow the credentials.</span></span> <span data-ttu-id="3b04c-249">Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="3b04c-250">Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3b04c-251">Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.</span><span class="sxs-lookup"><span data-stu-id="3b04c-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3b04c-252">Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="3b04c-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3b04c-253">Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="3b04c-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3b04c-254">Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.</span><span class="sxs-lookup"><span data-stu-id="3b04c-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="3b04c-255">Предполетные запросы</span><span class="sxs-lookup"><span data-stu-id="3b04c-255">Preflight requests</span></span>

<span data-ttu-id="3b04c-256">Для некоторых запросов CORS браузер отправляет дополнительный запрос [OPTIONS,](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) прежде чем сделать фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="3b04c-257">Этот запрос называется [предполетным запросом.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="3b04c-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="3b04c-258">Браузер может пропустить предполетный запрос, если ***все*** следующие условия верны:</span><span class="sxs-lookup"><span data-stu-id="3b04c-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="3b04c-259">Метод запроса: GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="3b04c-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3b04c-260">Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .</span><span class="sxs-lookup"><span data-stu-id="3b04c-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3b04c-261">Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="3b04c-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3b04c-262">Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект.</span><span class="sxs-lookup"><span data-stu-id="3b04c-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3b04c-263">Спецификация CORS называет эти заголовки [автор запрос заголовки](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="3b04c-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="3b04c-264">Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3b04c-265">Ниже приводится пример ответа, аналогичного предполетного запроса, сделанного из кнопки **«Put Test»** в разделе [Test CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="3b04c-266">Предполетный запрос использует метод [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="3b04c-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="3b04c-267">Он может включать в себя следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="3b04c-267">It may include the following headers:</span></span>

* <span data-ttu-id="3b04c-268">[Метод доступа-Контроль-Запрос](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3b04c-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Список заголовков запросов, который приложение устанавливает на фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3b04c-270">Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .</span><span class="sxs-lookup"><span data-stu-id="3b04c-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="3b04c-271">Методы контроля доступа-разрешить</span><span class="sxs-lookup"><span data-stu-id="3b04c-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="3b04c-272">Если предполетный запрос отклонен, приложение `200 OK` возвращает ответ, но не устанавливает заголовки CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="3b04c-273">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="3b04c-274">Пример отклоненного предполетного запроса можно просмотреть раздел [теста CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="3b04c-275">Используя инструменты F12, консоль приложение показывает ошибку, похожую на одну из следующих, в зависимости от браузера:</span><span class="sxs-lookup"><span data-stu-id="3b04c-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="3b04c-276">Firefox: Кросс-Origin Запрос заблокирован: Та же политика происхождения `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`запрещает чтение удаленного ресурса на .</span><span class="sxs-lookup"><span data-stu-id="3b04c-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="3b04c-277">(Причина: запрос CORS не увенчался успехом).</span><span class="sxs-lookup"><span data-stu-id="3b04c-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="3b04c-278">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="3b04c-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="3b04c-279">Chromium на основе: Доступhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5к получению на ' от происхождения 'https://cors3.azurewebsites.netбыл заблокирован политикой CORS: Ответ на предполетный запрос не проходит проверку контроля доступа: нет заголовка 'Access-Control-Allow-Origin' на запрашиваемом ресурсе.</span><span class="sxs-lookup"><span data-stu-id="3b04c-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3b04c-280">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="3b04c-281">Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="3b04c-282">Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="3b04c-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="3b04c-283">Браузеры не соответствуют тому, `Access-Control-Request-Headers`как они устанавливают.</span><span class="sxs-lookup"><span data-stu-id="3b04c-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3b04c-284">Если какой-либо:</span><span class="sxs-lookup"><span data-stu-id="3b04c-284">If either:</span></span>

* <span data-ttu-id="3b04c-285">Заголовки настроены на что-либо, кроме`"*"`</span><span class="sxs-lookup"><span data-stu-id="3b04c-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="3b04c-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>называется: Включите `Content-Type`по `Origin`крайней мере `Accept`, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.</span><span class="sxs-lookup"><span data-stu-id="3b04c-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="3b04c-287">Автоматический код предполетного запроса</span><span class="sxs-lookup"><span data-stu-id="3b04c-287">Automatic preflight request code</span></span>

<span data-ttu-id="3b04c-288">При применении политики CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="3b04c-289">Глобально, позвонив `app.UseCors` в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="3b04c-290">Использование `[EnableCors]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="3b04c-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="3b04c-291">ASP.NET Core отвечает на предполетный запрос OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="3b04c-292">Включение CORS на основе конечных `RequireCors` точек, используя в настоящее ***время, не*** поддерживает автоматические предполетные запросы.</span><span class="sxs-lookup"><span data-stu-id="3b04c-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="3b04c-293">Элемент ы в разделе [Test CORS](#testc) этого документа демонстрирует такое поведение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="3b04c-294">Атрибут «HttpOptions» для предполетных запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="3b04c-295">Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="3b04c-296">В некоторых сценариях это может быть не так.</span><span class="sxs-lookup"><span data-stu-id="3b04c-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="3b04c-297">Например, использование [CORS с конечным пунктом разгрома.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="3b04c-298">Следующий код использует атрибут [«HttpOptions»](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) для создания конечных точек для запросов OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="3b04c-299">Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [тест CORS с конечным точечным routing и «HttpOptions».](#tcer)</span><span class="sxs-lookup"><span data-stu-id="3b04c-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3b04c-300">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="3b04c-300">Set the preflight expiration time</span></span>

<span data-ttu-id="3b04c-301">Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3b04c-302">Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3b04c-303">Как работает CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-303">How CORS works</span></span>

<span data-ttu-id="3b04c-304">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b04c-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3b04c-305">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="3b04c-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="3b04c-306">CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3b04c-307">Например, злоумышленник может использовать [кросс-сайт сценариев (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнить запрос на кросс-сайт на свой сайт с поддержкой CORS для кражи информации.</span><span class="sxs-lookup"><span data-stu-id="3b04c-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3b04c-308">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="3b04c-309">Клиент (браузер) должен обеспечить соблюдение CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3b04c-310">Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="3b04c-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3b04c-311">Например, любой из следующих инструментов будет отображать ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="3b04c-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3b04c-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3b04c-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3b04c-313">Postman</span><span class="sxs-lookup"><span data-stu-id="3b04c-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3b04c-314">.NET httpclient</span><span class="sxs-lookup"><span data-stu-id="3b04c-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3b04c-315">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="3b04c-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3b04c-316">Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.</span><span class="sxs-lookup"><span data-stu-id="3b04c-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3b04c-317">Браузеры без CORS не могут делать запросы на кросс-происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="3b04c-318">До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3b04c-319">JSONP не использует XHR, он `<script>` использует тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3b04c-320">Скрипты могут быть загружены кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3b04c-321">[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3b04c-322">Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3b04c-323">Пользовательский код JavaScript не требуется для включения CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3b04c-324">[Кнопка теста PUT](https://cors3.azurewebsites.net/test) на развернутом [образце](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="3b04c-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="3b04c-325">Ниже приводится пример запроса на перекрестное происхождение `https://cors1.azurewebsites.net/api/values`от кнопки тестирования [значений.](https://cors3.azurewebsites.net/)</span><span class="sxs-lookup"><span data-stu-id="3b04c-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="3b04c-326">Заголовок: `Origin`</span><span class="sxs-lookup"><span data-stu-id="3b04c-326">The `Origin` header:</span></span>

* <span data-ttu-id="3b04c-327">Предоставляет домен сайта, который делает запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="3b04c-328">Требуется и должен отличаться от хоста.</span><span class="sxs-lookup"><span data-stu-id="3b04c-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="3b04c-329">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="3b04c-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="3b04c-330">**Заголовки ответов**</span><span class="sxs-lookup"><span data-stu-id="3b04c-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="3b04c-331">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="3b04c-331">**Request headers**</span></span>

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

<span data-ttu-id="3b04c-332">В `OPTIONS` запросах сервер устанавливает заголовок **заголовков ответов** `Access-Control-Allow-Origin: {allowed origin}` в ответе.</span><span class="sxs-lookup"><span data-stu-id="3b04c-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="3b04c-333">Например, развернутый [образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), Удалить кнопку ["EnableCors"](https://cors1.azurewebsites.net/test?number=2) кнопка `OPTIONS` запрос содержит следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="3b04c-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="3b04c-334">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="3b04c-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="3b04c-335">**Заголовки ответов**</span><span class="sxs-lookup"><span data-stu-id="3b04c-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="3b04c-336">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="3b04c-336">**Request headers**</span></span>

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

<span data-ttu-id="3b04c-337">В предыдущих **заголовках ответов**сервер устанавливает в ответ заголовок [Access-Control-Allow-Origin.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)</span><span class="sxs-lookup"><span data-stu-id="3b04c-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="3b04c-338">Значение `https://cors1.azurewebsites.net` этого заголовка совпадает с заголовком `Origin` из запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="3b04c-339">Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> называется, `Access-Control-Allow-Origin: *`значение подстановочного знака возвращается.</span><span class="sxs-lookup"><span data-stu-id="3b04c-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="3b04c-340">`AllowAnyOrigin`позволяет любого происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="3b04c-341">Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается.</span><span class="sxs-lookup"><span data-stu-id="3b04c-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3b04c-342">В частности, браузер отдает запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3b04c-343">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="3b04c-344">Отображение options запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-344">Display OPTIONS requests</span></span>

<span data-ttu-id="3b04c-345">По умолчанию браузеры Chrome и Edge не отображали запросы OPTIONS на сетевой вкладке инструментов F12.</span><span class="sxs-lookup"><span data-stu-id="3b04c-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="3b04c-346">Для отображения запросов OPTIONS в этих браузерах:</span><span class="sxs-lookup"><span data-stu-id="3b04c-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="3b04c-347">`chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="3b04c-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="3b04c-348">отключить флаг.</span><span class="sxs-lookup"><span data-stu-id="3b04c-348">disable the flag.</span></span>
* <span data-ttu-id="3b04c-349">Перезапустить.</span><span class="sxs-lookup"><span data-stu-id="3b04c-349">restart.</span></span>

<span data-ttu-id="3b04c-350">Firefox показывает запросы OPTIONS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b04c-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3b04c-351">КОРС в IIS</span><span class="sxs-lookup"><span data-stu-id="3b04c-351">CORS in IIS</span></span>

<span data-ttu-id="3b04c-352">При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3b04c-353">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="3b04c-354">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-354">Test CORS</span></span>

<span data-ttu-id="3b04c-355">[В загрузке образца](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="3b04c-356">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3b04c-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="3b04c-357">Образец представляет собой проект API с добавлением страниц Razor:</span><span class="sxs-lookup"><span data-stu-id="3b04c-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="3b04c-358">`WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3b04c-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="3b04c-359">`ValuesController` Следующие точки для тестирования:</span><span class="sxs-lookup"><span data-stu-id="3b04c-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="3b04c-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется [пакетом Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet и отображает информацию о маршруте.</span><span class="sxs-lookup"><span data-stu-id="3b04c-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="3b04c-361">Проверьте предыдущий пример кода, используя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="3b04c-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="3b04c-362">Используйте развернутое [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)приложение образца на .</span><span class="sxs-lookup"><span data-stu-id="3b04c-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="3b04c-363">Нет необходимости загружать образец.</span><span class="sxs-lookup"><span data-stu-id="3b04c-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="3b04c-364">Выполнить образец с `dotnet run` помощью URL-адреса по `https://localhost:5001`умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b04c-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="3b04c-365">Выполнить образец из Visual Studio с портом установлен на `https://localhost:44398`44398 для URL .</span><span class="sxs-lookup"><span data-stu-id="3b04c-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="3b04c-366">Использование браузера с инструментами F12:</span><span class="sxs-lookup"><span data-stu-id="3b04c-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="3b04c-367">Выберите кнопку **Значения** и просмотрите заголовки во вкладке **Сети.**</span><span class="sxs-lookup"><span data-stu-id="3b04c-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="3b04c-368">Выберите кнопку **теста PUT.**</span><span class="sxs-lookup"><span data-stu-id="3b04c-368">Select the **PUT test** button.</span></span> <span data-ttu-id="3b04c-369">Смотрите [запросы Display OPTIONS](#options) для инструкций по отображению запроса OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="3b04c-370">**Тест PUT** создает два запроса: предполетный запрос OPTIONS и put-запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="3b04c-371">Выберите **`GetValues2 [DisableCors]`** кнопку, чтобы вызвать неудавшийся запрос CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="3b04c-372">Как уже упоминалось в документе, ответ возвращает 200 успехов, но запрос CORS не сделан.</span><span class="sxs-lookup"><span data-stu-id="3b04c-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="3b04c-373">Выберите вкладку **Консоль,** чтобы увидеть ошибку CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="3b04c-374">В зависимости от браузера отображается ошибка, аналогичная следующему:</span><span class="sxs-lookup"><span data-stu-id="3b04c-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="3b04c-375">Доступ к `'https://cors1.azurewebsites.net/api/values/GetValues2'` получению `'https://cors3.azurewebsites.net'` из происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка «Доступ-контроль-разрешить-Origin».</span><span class="sxs-lookup"><span data-stu-id="3b04c-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="3b04c-376">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="3b04c-377">Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [локон,](https://curl.haxx.se/) [Скрипач](https://www.telerik.com/fiddler)или [Почтальон.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="3b04c-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3b04c-378">При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей.</span><span class="sxs-lookup"><span data-stu-id="3b04c-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3b04c-379">Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:</span><span class="sxs-lookup"><span data-stu-id="3b04c-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3b04c-380">Для обработки запроса CORS Middleware не нужно.</span><span class="sxs-lookup"><span data-stu-id="3b04c-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3b04c-381">Заголовки CORS не возвращаются в ответ.</span><span class="sxs-lookup"><span data-stu-id="3b04c-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="3b04c-382">Следующая команда `curl` использует для выдачи запроса OPTIONS с информацией:</span><span class="sxs-lookup"><span data-stu-id="3b04c-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="3b04c-383">Тест CORS с конечным точечным разгромом и «HttpOptions»</span><span class="sxs-lookup"><span data-stu-id="3b04c-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="3b04c-384">Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf)</span><span class="sxs-lookup"><span data-stu-id="3b04c-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="3b04c-385">Рассмотрим следующий код, который использует [конечную точку разгрома для включения CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="3b04c-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="3b04c-386">`TodoItems1Controller` Следующие конечные точки для тестирования:</span><span class="sxs-lookup"><span data-stu-id="3b04c-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="3b04c-387">Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=1) развернутого [образца.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="3b04c-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="3b04c-388">**Кнопки «Удалить» и** **«GET» (EnableCors)** удаляют `[EnableCors]` ся, так как конечные точки имеют и отвечают на предполетные запросы.</span><span class="sxs-lookup"><span data-stu-id="3b04c-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="3b04c-389">Другие конечные точки не удается.</span><span class="sxs-lookup"><span data-stu-id="3b04c-389">The other endpoints fails.</span></span> <span data-ttu-id="3b04c-390">Кнопка **GET** выходит из строя, так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:</span><span class="sxs-lookup"><span data-stu-id="3b04c-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="3b04c-391">Следующие `TodoItems2Controller` конечные точки, но включает в себя явный код для ответа на запросы OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="3b04c-392">Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=2) развернутого образца.</span><span class="sxs-lookup"><span data-stu-id="3b04c-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="3b04c-393">В **контроллере** упасть список, выберите **Preflight,** а затем **установить контроллер.**</span><span class="sxs-lookup"><span data-stu-id="3b04c-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="3b04c-394">Все вызовы CORS `TodoItems2Controller` в конечные точки увенчаются успехом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b04c-395">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3b04c-395">Additional resources</span></span>

* [<span data-ttu-id="3b04c-396">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="3b04c-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3b04c-397">Начало работы с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3b04c-398">Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3b04c-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3b04c-399">В этой статье показано, как включить CORS в ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="3b04c-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="3b04c-400">Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="3b04c-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="3b04c-401">Это ограничение называется *политикой одного и того же происхождения.*</span><span class="sxs-lookup"><span data-stu-id="3b04c-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="3b04c-402">Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="3b04c-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="3b04c-403">Иногда может потребоваться разрешить другим сайтам делать запросы на перекрестное происхождение в вашем приложении.</span><span class="sxs-lookup"><span data-stu-id="3b04c-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="3b04c-404">Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)</span><span class="sxs-lookup"><span data-stu-id="3b04c-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="3b04c-405">[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="3b04c-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="3b04c-406">Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="3b04c-407">Это **не** функция безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="3b04c-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="3b04c-408">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="3b04c-409">Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="3b04c-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="3b04c-410">Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.</span><span class="sxs-lookup"><span data-stu-id="3b04c-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="3b04c-411">Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="3b04c-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="3b04c-412">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3b04c-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="3b04c-413">То же происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-413">Same origin</span></span>

<span data-ttu-id="3b04c-414">Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="3b04c-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="3b04c-415">Эти два URL-адреса имеют одно и то же происхождение:</span><span class="sxs-lookup"><span data-stu-id="3b04c-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="3b04c-416">Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="3b04c-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="3b04c-417">`https://example.net`&ndash; Различные домены</span><span class="sxs-lookup"><span data-stu-id="3b04c-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="3b04c-418">`https://www.example.com/foo.html`&ndash; Различные поддомены</span><span class="sxs-lookup"><span data-stu-id="3b04c-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="3b04c-419">`http://example.com/foo.html`&ndash; Различная схема</span><span class="sxs-lookup"><span data-stu-id="3b04c-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="3b04c-420">`https://example.com:9000/foo.html`&ndash; Различные порты</span><span class="sxs-lookup"><span data-stu-id="3b04c-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="3b04c-421">Internet Explorer не учитывает порт при сравнении происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="3b04c-422">CORS с названной политикой и программой</span><span class="sxs-lookup"><span data-stu-id="3b04c-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="3b04c-423">CORS Middleware обрабатывает запросы по перекрестному происхождению.</span><span class="sxs-lookup"><span data-stu-id="3b04c-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="3b04c-424">Следующий код позволяет CORS для всего приложения с указанным происхождением:</span><span class="sxs-lookup"><span data-stu-id="3b04c-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="3b04c-425">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="3b04c-425">The preceding code:</span></span>

* <span data-ttu-id="3b04c-426">Устанавливает название полиса\_на "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="3b04c-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="3b04c-427">Название политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="3b04c-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="3b04c-428">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который позволяет CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="3b04c-429">Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="3b04c-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="3b04c-430">Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет.</span><span class="sxs-lookup"><span data-stu-id="3b04c-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="3b04c-431">[Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.</span><span class="sxs-lookup"><span data-stu-id="3b04c-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="3b04c-432">Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:</span><span class="sxs-lookup"><span data-stu-id="3b04c-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="3b04c-433">Для получения дополнительной информации [в](#cpo) этом документе см.</span><span class="sxs-lookup"><span data-stu-id="3b04c-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="3b04c-434">Метод <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> может цепиметоды, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="3b04c-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="3b04c-435">Примечание: **URL-адрес не** должен содержать`/`задняя черта ().</span><span class="sxs-lookup"><span data-stu-id="3b04c-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="3b04c-436">Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="3b04c-437">Следующий код применяет политики CORS ко всем конечным точкам приложений через CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="3b04c-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="3b04c-438">Примечание: `UseCors` необходимо вызвать перед `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="3b04c-439">[См. Включить CORS в Razor Страницы, контроллеры и методы действий](#ecors) для применения политики CORS на странице / контроллер / действие уровне.</span><span class="sxs-lookup"><span data-stu-id="3b04c-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="3b04c-440">Ознакомиться с [инструкциями](#test) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="3b04c-441">Включить CORS с атрибутами</span><span class="sxs-lookup"><span data-stu-id="3b04c-441">Enable CORS with attributes</span></span>

<span data-ttu-id="3b04c-442">Атрибут [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру.</span><span class="sxs-lookup"><span data-stu-id="3b04c-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="3b04c-443">Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек.</span><span class="sxs-lookup"><span data-stu-id="3b04c-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="3b04c-444">Используется `[EnableCors]` для указания политики по умолчанию и `[EnableCors("{Policy String}")]` для указания политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="3b04c-445">Атрибут `[EnableCors]` может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="3b04c-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="3b04c-446">Страница бритвы`PageModel`</span><span class="sxs-lookup"><span data-stu-id="3b04c-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="3b04c-447">Контроллер</span><span class="sxs-lookup"><span data-stu-id="3b04c-447">Controller</span></span>
* <span data-ttu-id="3b04c-448">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="3b04c-448">Controller action method</span></span>

<span data-ttu-id="3b04c-449">Можно применить различные политики к контроллеру/странице-модели/действию с атрибутом. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="3b04c-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="3b04c-450">Когда `[EnableCors]` атрибут применяется к методу модели/модели/действий контроллеров/страниц, а CORS включен в промежуточном программном обеспечении, ***применяются обе*** политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="3b04c-451">Мы рекомендуем ***не*** комбинировать политики.</span><span class="sxs-lookup"><span data-stu-id="3b04c-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="3b04c-452">Используйте `[EnableCors]` атрибут или промежуточное программное обеспечение, и**то, и другое.**</span><span class="sxs-lookup"><span data-stu-id="3b04c-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="3b04c-453">При `[EnableCors]`использовании **не** определяется политика по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b04c-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="3b04c-454">Следующий код применяет различную политику к каждому методу:</span><span class="sxs-lookup"><span data-stu-id="3b04c-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="3b04c-455">Следующий код создает политику по умолчанию `"AnotherPolicy"`CORS и политику под названием:</span><span class="sxs-lookup"><span data-stu-id="3b04c-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="3b04c-456">Отключить CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-456">Disable CORS</span></span>

<span data-ttu-id="3b04c-457">[ &lbrack;Атрибут DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отстраняет CORS от контроллера/страницы-модели/действия.</span><span class="sxs-lookup"><span data-stu-id="3b04c-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="3b04c-458">Варианты политики CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-458">CORS policy options</span></span>

<span data-ttu-id="3b04c-459">В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="3b04c-460">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="3b04c-461">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="3b04c-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="3b04c-462">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="3b04c-463">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="3b04c-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="3b04c-464">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="3b04c-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="3b04c-465">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="3b04c-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="3b04c-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в .</span><span class="sxs-lookup"><span data-stu-id="3b04c-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3b04c-467">Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.</span><span class="sxs-lookup"><span data-stu-id="3b04c-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="3b04c-468">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="3b04c-468">Set the allowed origins</span></span>

<span data-ttu-id="3b04c-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`).</span><span class="sxs-lookup"><span data-stu-id="3b04c-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="3b04c-470">`AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="3b04c-471">Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт.</span><span class="sxs-lookup"><span data-stu-id="3b04c-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="3b04c-472">Для безопасного приложения укажите точный список истоков, если клиент должен разрешить себе доступ к серверным ресурсам.</span><span class="sxs-lookup"><span data-stu-id="3b04c-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="3b04c-473">`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="3b04c-474">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3b04c-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="3b04c-476">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="3b04c-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="3b04c-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="3b04c-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="3b04c-478">Позволяет любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="3b04c-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="3b04c-479">Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="3b04c-480">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="3b04c-481">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="3b04c-481">Set the allowed request headers</span></span>

<span data-ttu-id="3b04c-482">Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали *заголовки запросов автора,* позвоните и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="3b04c-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3b04c-483">Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="3b04c-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3b04c-484">Эта настройка влияет на `Access-Control-Request-Headers` предполетные запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="3b04c-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="3b04c-485">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="3b04c-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="3b04c-486">CORS Middleware всегда позволяет отправлять `Access-Control-Request-Headers` четыре заголовка, которые должны быть отправлены независимо от значений, настроенных в CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="3b04c-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="3b04c-487">Этот список заголовков включает в себя:</span><span class="sxs-lookup"><span data-stu-id="3b04c-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="3b04c-488">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3b04c-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="3b04c-489">CORS Middleware успешно отвечает на предполетный запрос следующим `Content-Language` заголовком запроса, поскольку всегда находится в белом списке:</span><span class="sxs-lookup"><span data-stu-id="3b04c-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="3b04c-490">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="3b04c-490">Set the exposed response headers</span></span>

<span data-ttu-id="3b04c-491">По умолчанию браузер не предоставляет приложению все заголовки ответов.</span><span class="sxs-lookup"><span data-stu-id="3b04c-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="3b04c-492">Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="3b04c-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="3b04c-493">Заголовки ответов, доступные по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3b04c-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="3b04c-494">Спецификация CORS называет эти заголовки *простыми заголовками ответов.*</span><span class="sxs-lookup"><span data-stu-id="3b04c-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="3b04c-495">Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="3b04c-496">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="3b04c-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="3b04c-497">Учетные данные требуют специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="3b04c-498">По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="3b04c-499">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b04c-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="3b04c-500">Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="3b04c-501">Использование `XMLHttpRequest` непосредственно:</span><span class="sxs-lookup"><span data-stu-id="3b04c-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="3b04c-502">Используя j'ери:</span><span class="sxs-lookup"><span data-stu-id="3b04c-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="3b04c-503">Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="3b04c-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="3b04c-504">Сервер должен разрешить учетные данные.</span><span class="sxs-lookup"><span data-stu-id="3b04c-504">The server must allow the credentials.</span></span> <span data-ttu-id="3b04c-505">Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="3b04c-506">Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="3b04c-507">Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.</span><span class="sxs-lookup"><span data-stu-id="3b04c-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="3b04c-508">Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="3b04c-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="3b04c-509">Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="3b04c-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="3b04c-510">Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.</span><span class="sxs-lookup"><span data-stu-id="3b04c-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="3b04c-511">Предполетные запросы</span><span class="sxs-lookup"><span data-stu-id="3b04c-511">Preflight requests</span></span>

<span data-ttu-id="3b04c-512">Для некоторых запросов CORS браузер отправляет дополнительный запрос, прежде чем сделать фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="3b04c-513">Этот запрос называется *предполетным запросом.*</span><span class="sxs-lookup"><span data-stu-id="3b04c-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="3b04c-514">Браузер может пропустить предполетный запрос, если верны следующие условия:</span><span class="sxs-lookup"><span data-stu-id="3b04c-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="3b04c-515">Метод запроса: GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="3b04c-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="3b04c-516">Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .</span><span class="sxs-lookup"><span data-stu-id="3b04c-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="3b04c-517">Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="3b04c-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="3b04c-518">Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект.</span><span class="sxs-lookup"><span data-stu-id="3b04c-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="3b04c-519">Спецификация CORS называет эти заголовки *автор запрос заголовки*.</span><span class="sxs-lookup"><span data-stu-id="3b04c-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="3b04c-520">Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="3b04c-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="3b04c-521">Ниже приводится пример предполетного запроса:</span><span class="sxs-lookup"><span data-stu-id="3b04c-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="3b04c-522">Предполетный запрос использует метод HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="3b04c-523">Она включает в себя два специальных заголовка:</span><span class="sxs-lookup"><span data-stu-id="3b04c-523">It includes two special headers:</span></span>

* <span data-ttu-id="3b04c-524">`Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="3b04c-525">`Access-Control-Request-Headers`: Список заголовков запросов, которые приложение устанавливает на фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="3b04c-526">Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .</span><span class="sxs-lookup"><span data-stu-id="3b04c-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="3b04c-527">Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="3b04c-528">Смотрите [атрибут «HttpOptions» для предполетных запросов.](#pro)</span><span class="sxs-lookup"><span data-stu-id="3b04c-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="3b04c-529">Предполетный запрос CORS `Access-Control-Request-Headers` может включать заголовок, который указывает на сервер заголовки, которые отправляются с фактическим запросом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="3b04c-530">Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="3b04c-531">Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="3b04c-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="3b04c-532">Браузеры не полностью последовательны в `Access-Control-Request-Headers`том, как они устанавливают.</span><span class="sxs-lookup"><span data-stu-id="3b04c-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="3b04c-533">Если вы установите заголовки `"*"` на <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>что-либо, кроме `Accept` `Content-Type`(или `Origin`использовать), вы должны включить по крайней мере, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.</span><span class="sxs-lookup"><span data-stu-id="3b04c-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="3b04c-534">Ниже приводится пример ответа на предполетный запрос (при условии, что сервер позволяет запрос):</span><span class="sxs-lookup"><span data-stu-id="3b04c-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="3b04c-535">Ответ включает `Access-Control-Allow-Methods` в себя заголовок, который перечисляет `Access-Control-Allow-Headers` разрешенные методы и опционально заголовок, в котором перечислены разрешенные заголовки.</span><span class="sxs-lookup"><span data-stu-id="3b04c-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="3b04c-536">Если предполетный запрос удается, браузер отправляет фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="3b04c-537">Если предполетный запрос отклонен, приложение возвращает *200 OK* ответ, но не отправляет заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="3b04c-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="3b04c-538">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="3b04c-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="3b04c-539">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="3b04c-539">Set the preflight expiration time</span></span>

<span data-ttu-id="3b04c-540">Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="3b04c-541">Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:</span><span class="sxs-lookup"><span data-stu-id="3b04c-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="3b04c-542">Как работает CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-542">How CORS works</span></span>

<span data-ttu-id="3b04c-543">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b04c-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="3b04c-544">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="3b04c-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="3b04c-545">CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="3b04c-546">Например, злоумышленник может использовать prevent [cross-Site Scripting (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнять запрос на перекрестный сайт на свой сайт с поддержкой CORS для кражи информации.</span><span class="sxs-lookup"><span data-stu-id="3b04c-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="3b04c-547">Ваш API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="3b04c-548">Клиент (браузер) должен обеспечить соблюдение CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="3b04c-549">Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="3b04c-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="3b04c-550">Например, любой из следующих инструментов будет отображать ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="3b04c-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="3b04c-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="3b04c-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="3b04c-552">Postman</span><span class="sxs-lookup"><span data-stu-id="3b04c-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="3b04c-553">.NET httpclient</span><span class="sxs-lookup"><span data-stu-id="3b04c-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="3b04c-554">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="3b04c-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="3b04c-555">Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.</span><span class="sxs-lookup"><span data-stu-id="3b04c-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="3b04c-556">Браузеры (без CORS) не могут делать запросы на кросс-происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="3b04c-557">До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="3b04c-558">JSONP не использует XHR, он `<script>` использует тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="3b04c-559">Скрипты могут быть загружены кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="3b04c-560">[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение.</span><span class="sxs-lookup"><span data-stu-id="3b04c-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="3b04c-561">Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="3b04c-562">Пользовательский код JavaScript не требуется для включения CORS.</span><span class="sxs-lookup"><span data-stu-id="3b04c-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="3b04c-563">Ниже приводится пример запроса о перекрестном происхождении.</span><span class="sxs-lookup"><span data-stu-id="3b04c-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="3b04c-564">Заголовок `Origin` предоставляет домен сайта, который делает запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="3b04c-565">Заголовок `Origin` необходим и должен отличаться от хостена.</span><span class="sxs-lookup"><span data-stu-id="3b04c-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="3b04c-566">Если сервер разрешает запрос, он `Access-Control-Allow-Origin` устанавливает заголовок в ответе.</span><span class="sxs-lookup"><span data-stu-id="3b04c-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="3b04c-567">Значение этого заголовка либо `Origin` соответствует заголовку из запроса, `"*"`либо является значением подстановочного знака, что означает, что любое происхождение разрешено:</span><span class="sxs-lookup"><span data-stu-id="3b04c-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="3b04c-568">Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается.</span><span class="sxs-lookup"><span data-stu-id="3b04c-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="3b04c-569">В частности, браузер отдает запрос.</span><span class="sxs-lookup"><span data-stu-id="3b04c-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="3b04c-570">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="3b04c-571">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-571">Test CORS</span></span>

<span data-ttu-id="3b04c-572">Для тестирования CORS:</span><span class="sxs-lookup"><span data-stu-id="3b04c-572">To test CORS:</span></span>

1. <span data-ttu-id="3b04c-573">[Создайте проект API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="3b04c-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="3b04c-574">Кроме того, вы можете [скачать образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3b04c-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="3b04c-575">Включите CORS, используя один из подходов в этом документе.</span><span class="sxs-lookup"><span data-stu-id="3b04c-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="3b04c-576">Пример:</span><span class="sxs-lookup"><span data-stu-id="3b04c-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="3b04c-577">`WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="3b04c-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="3b04c-578">Создайте проект веб-приложений (Страницы razor или MVC).</span><span class="sxs-lookup"><span data-stu-id="3b04c-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="3b04c-579">В образце используются страницы razor.</span><span class="sxs-lookup"><span data-stu-id="3b04c-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="3b04c-580">Вы можете создать веб-приложение в том же решении, что и проект API.</span><span class="sxs-lookup"><span data-stu-id="3b04c-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="3b04c-581">Добавьте следующий выделенный код в файл *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="3b04c-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="3b04c-582">В предыдущем коде `url: 'https://<web app>.azurewebsites.net/api/values/1',` замените URL-адрес развернутого приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="3b04c-583">Развертывание проекта API.</span><span class="sxs-lookup"><span data-stu-id="3b04c-583">Deploy the API project.</span></span> <span data-ttu-id="3b04c-584">Например, [развернуть в Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="3b04c-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="3b04c-585">Выполнить Razor Pages или MVC приложение с рабочего стола и нажмите на кнопку **испытаний.**</span><span class="sxs-lookup"><span data-stu-id="3b04c-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="3b04c-586">Используйте инструменты F12 для проверки сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="3b04c-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="3b04c-587">Удалите происхождение `WithOrigins` локального хозяина из приложения и разместите его.</span><span class="sxs-lookup"><span data-stu-id="3b04c-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="3b04c-588">Кроме того, запустите клиентское приложение с другим портом.</span><span class="sxs-lookup"><span data-stu-id="3b04c-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="3b04c-589">Например, запустить из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3b04c-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="3b04c-590">Тест с помощью клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-590">Test with the client app.</span></span> <span data-ttu-id="3b04c-591">Сбои CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3b04c-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="3b04c-592">Используйте консольную вкладку в инструментах F12, чтобы увидеть ошибку.</span><span class="sxs-lookup"><span data-stu-id="3b04c-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="3b04c-593">В зависимости от браузера, вы получаете ошибку (в f12 консоли инструментов) похож на следующее:</span><span class="sxs-lookup"><span data-stu-id="3b04c-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="3b04c-594">Использование Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="3b04c-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="3b04c-595">**SEC7120: «CORS» `https://localhost:44375` Происхождение не `https://localhost:44375` было нашло в заголовке ответа Access-Control-Allow-Origin для ресурса кросс-происхождения в`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="3b04c-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="3b04c-596">Использование Chrome:</span><span class="sxs-lookup"><span data-stu-id="3b04c-596">Using Chrome:</span></span>

     <span data-ttu-id="3b04c-597">**Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` от происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка "Доступ-контроль-Разрешить-Происхождение".**</span><span class="sxs-lookup"><span data-stu-id="3b04c-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="3b04c-598">Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [Fiddler](https://www.telerik.com/fiddler) или [Postman.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="3b04c-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="3b04c-599">При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей.</span><span class="sxs-lookup"><span data-stu-id="3b04c-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="3b04c-600">Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:</span><span class="sxs-lookup"><span data-stu-id="3b04c-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="3b04c-601">Для обработки запроса CORS Middleware не нужно.</span><span class="sxs-lookup"><span data-stu-id="3b04c-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="3b04c-602">Заголовки CORS не возвращаются в ответ.</span><span class="sxs-lookup"><span data-stu-id="3b04c-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="3b04c-603">КОРС в IIS</span><span class="sxs-lookup"><span data-stu-id="3b04c-603">CORS in IIS</span></span>

<span data-ttu-id="3b04c-604">При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="3b04c-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="3b04c-605">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="3b04c-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b04c-606">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3b04c-606">Additional resources</span></span>

* [<span data-ttu-id="3b04c-607">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="3b04c-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="3b04c-608">Начало работы с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="3b04c-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
