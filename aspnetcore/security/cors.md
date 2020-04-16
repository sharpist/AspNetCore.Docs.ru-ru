---
title: Включить запросы cross-Origin (CORS) в ASP.NET Core
author: rick-anderson
description: Узнайте, как CORS как стандарт для разрешения или отклонения запросов на кросс-происхождение в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440913"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="5bc31-103">Включить запросы cross-Origin (CORS) в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5bc31-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5bc31-104">[Рик Андерсон](https://twitter.com/RickAndMSFT) и [Кирк Ларкин](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="5bc31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="5bc31-105">В этой статье показано, как включить CORS в ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="5bc31-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="5bc31-106">Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="5bc31-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5bc31-107">Это ограничение называется *политикой одного и того же происхождения.*</span><span class="sxs-lookup"><span data-stu-id="5bc31-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5bc31-108">Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="5bc31-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5bc31-109">Иногда может потребоваться разрешить другим сайтам делать запросы на кросс-происхождение в ваше приложение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="5bc31-110">Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)</span><span class="sxs-lookup"><span data-stu-id="5bc31-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="5bc31-111">[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="5bc31-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="5bc31-112">Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="5bc31-113">Это **не** функция безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="5bc31-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="5bc31-114">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="5bc31-115">Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="5bc31-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="5bc31-116">Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.</span><span class="sxs-lookup"><span data-stu-id="5bc31-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="5bc31-117">Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="5bc31-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="5bc31-118">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5bc31-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="5bc31-119">То же происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-119">Same origin</span></span>

<span data-ttu-id="5bc31-120">Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="5bc31-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="5bc31-121">Эти два URL-адреса имеют одно и то же происхождение:</span><span class="sxs-lookup"><span data-stu-id="5bc31-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="5bc31-122">Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="5bc31-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="5bc31-123">`https://example.net`&ndash; Различные домены</span><span class="sxs-lookup"><span data-stu-id="5bc31-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="5bc31-124">`https://www.example.com/foo.html`&ndash; Различные поддомены</span><span class="sxs-lookup"><span data-stu-id="5bc31-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="5bc31-125">`http://example.com/foo.html`&ndash; Различная схема</span><span class="sxs-lookup"><span data-stu-id="5bc31-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="5bc31-126">`https://example.com:9000/foo.html`&ndash; Различные порты</span><span class="sxs-lookup"><span data-stu-id="5bc31-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="5bc31-127">Включение CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-127">Enable CORS</span></span>

<span data-ttu-id="5bc31-128">Существует три способа включить CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="5bc31-129">В промежуточном программном обеспечении с использованием [политики именованного](#np) значения или [политики по умолчанию.](#dp)</span><span class="sxs-lookup"><span data-stu-id="5bc31-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="5bc31-130">Использование [конечных точек.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="5bc31-131">С атрибутом [«EnableCors».](#attr)</span><span class="sxs-lookup"><span data-stu-id="5bc31-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="5bc31-132">Использование атрибута [«EnableCors»](#attr) с именем политики обеспечивает лучший контроль в ограничении конечных точек, поддерживающих CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="5bc31-133">Каждый подход подробно описан в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="5bc31-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="5bc31-134">CORS с названной политикой и программой</span><span class="sxs-lookup"><span data-stu-id="5bc31-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="5bc31-135">CORS Middleware обрабатывает запросы по перекрестному происхождению.</span><span class="sxs-lookup"><span data-stu-id="5bc31-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="5bc31-136">Следующий код применяет политику CORS ко всем конечным точкам приложения с указанными истоками:</span><span class="sxs-lookup"><span data-stu-id="5bc31-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="5bc31-137">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5bc31-137">The preceding code:</span></span>

* <span data-ttu-id="5bc31-138">Устанавливает имя политики на `_myAllowSpecificOrigins`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="5bc31-139">Название политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="5bc31-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="5bc31-140">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения и определяет `_myAllowSpecificOrigins` политику CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="5bc31-141">`UseCors`добавляет промежуточное программное обеспечение CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="5bc31-142">Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="5bc31-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="5bc31-143">Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет.</span><span class="sxs-lookup"><span data-stu-id="5bc31-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="5bc31-144">[Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.</span><span class="sxs-lookup"><span data-stu-id="5bc31-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="5bc31-145">Включает `_myAllowSpecificOrigins` политику CORS для всех конечных точек контроллера.</span><span class="sxs-lookup"><span data-stu-id="5bc31-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="5bc31-146">[См. конечную точку,](#ecors) чтобы применить политику CORS к определенным конечным точкам.</span><span class="sxs-lookup"><span data-stu-id="5bc31-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="5bc31-147">При приведении в конечный пункт разгром промежуточное программное `UseRouting` `UseEndpoints`обеспечение CORS ***должно*** быть настроено для выполнения между вызовами и .</span><span class="sxs-lookup"><span data-stu-id="5bc31-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="5bc31-148">Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="5bc31-149">Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:</span><span class="sxs-lookup"><span data-stu-id="5bc31-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="5bc31-150">Для получения дополнительной информации в этом документе [ознакомьтесь с вариантами политики CORS.](#cpo)</span><span class="sxs-lookup"><span data-stu-id="5bc31-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="5bc31-151">Методы <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> могут быть прикованы, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="5bc31-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="5bc31-152">Примечание: Указанный URL **не** должен содержать`/`задняя черта ().</span><span class="sxs-lookup"><span data-stu-id="5bc31-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="5bc31-153">Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="5bc31-154">CORS с политикой по умолчанию и программами среднего</span><span class="sxs-lookup"><span data-stu-id="5bc31-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="5bc31-155">Следующий выделенный код позволяет политику CORS по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="5bc31-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="5bc31-156">Предыдущий код применяет политику CORS по умолчанию ко всем конечным точкам контроллера.</span><span class="sxs-lookup"><span data-stu-id="5bc31-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="5bc31-157">Включение CORS с маршрутизацией конечных точек</span><span class="sxs-lookup"><span data-stu-id="5bc31-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="5bc31-158">Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf)</span><span class="sxs-lookup"><span data-stu-id="5bc31-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="5bc31-159">Для получения дополнительной информации, [см. этот вопрос GitHub](https://github.com/dotnet/aspnetcore/issues/20709) и [тест CORS с конечным пунктом разгрома и "HttpOptions"](#tcer).</span><span class="sxs-lookup"><span data-stu-id="5bc31-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="5bc31-160">При конечной конечной конечной реунетировке CORS может <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> быть включен на основе конечной точки с использованием набора методов расширения:</span><span class="sxs-lookup"><span data-stu-id="5bc31-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="5bc31-161">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="5bc31-161">In the preceding code:</span></span>

* <span data-ttu-id="5bc31-162">`app.UseCors`позволяет CORS промежуточного посуды.</span><span class="sxs-lookup"><span data-stu-id="5bc31-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="5bc31-163">Поскольку политика по умолчанию не `app.UseCors()` настроена, само по себе не позволяет CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="5bc31-164">Конечные `/echo` точки контроллера и контроллера позволяют запросы на перекрестное происхождение с помощью указанной политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="5bc31-165">Конечные `/echo2` точки Страниц ы и страниц ы бритвы ***не*** разрешают запросы на перекрестное происхождение, поскольку политика по умолчанию не указана.</span><span class="sxs-lookup"><span data-stu-id="5bc31-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="5bc31-166">Атрибут [«DisableCors»](#dc) ***не*** отменяет CORS, который был включен путем `RequireCors`входиной раритетной коррески с помощью.</span><span class="sxs-lookup"><span data-stu-id="5bc31-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="5bc31-167">Ознакомиться с инструкциями по коду тестирования, аналогичным предыдущим, можно ознакомиться [с тестовым CORS с конечным точечным и «HttpOptions».](#tcer)</span><span class="sxs-lookup"><span data-stu-id="5bc31-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="5bc31-168">Включить CORS с атрибутами</span><span class="sxs-lookup"><span data-stu-id="5bc31-168">Enable CORS with attributes</span></span>

<span data-ttu-id="5bc31-169">Включение CORS с атрибутом [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) и применение политики с именем только к тем конечным точкам, которые требуют CORS, обеспечивает лучший контроль.</span><span class="sxs-lookup"><span data-stu-id="5bc31-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="5bc31-170">Атрибут [«EnableCors»](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру.</span><span class="sxs-lookup"><span data-stu-id="5bc31-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="5bc31-171">Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек:</span><span class="sxs-lookup"><span data-stu-id="5bc31-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="5bc31-172">`[EnableCors]`определяет политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5bc31-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="5bc31-173">`[EnableCors("{Policy String}")]`определяет именованные политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="5bc31-174">Атрибут `[EnableCors]` может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="5bc31-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="5bc31-175">Страница бритвы`PageModel`</span><span class="sxs-lookup"><span data-stu-id="5bc31-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="5bc31-176">Контроллер</span><span class="sxs-lookup"><span data-stu-id="5bc31-176">Controller</span></span>
* <span data-ttu-id="5bc31-177">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="5bc31-177">Controller action method</span></span>

<span data-ttu-id="5bc31-178">Различные политики могут быть применены к контроллерам, `[EnableCors]` моделям страниц или методам действий с атрибутом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="5bc31-179">Когда `[EnableCors]` атрибут применяется к контроллеру, модели страницы или методу действия, и CORS включен в промежуточном программном обеспечении, ***обе*** политики применяются.</span><span class="sxs-lookup"><span data-stu-id="5bc31-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="5bc31-180">***Мы рекомендуем не сочетать политики. Используйте*** ***атрибут или промежуточное программное обеспечение, а не оба в том же приложении.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="5bc31-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="5bc31-181">Следующий код применяет различную политику к каждому методу:</span><span class="sxs-lookup"><span data-stu-id="5bc31-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="5bc31-182">Следующий код создает две политики CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="5bc31-183">Для лучшего контроля за ограничением запросов CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="5bc31-184">Используйте `[EnableCors("MyPolicy")]` с именем политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="5bc31-185">Не определяйте политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5bc31-185">Don't define a default policy.</span></span>
* <span data-ttu-id="5bc31-186">Не используйте [конечную точку.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="5bc31-187">Код в следующем разделе соответствует предыдущему списку.</span><span class="sxs-lookup"><span data-stu-id="5bc31-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="5bc31-188">Ознакомиться с [инструкциями](#testc) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="5bc31-189">Отключить CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-189">Disable CORS</span></span>

<span data-ttu-id="5bc31-190">Атрибут [«DisableCors»](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***не*** отменяет CORS, который был включен путем [разгрома конечных точек.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="5bc31-191">Следующий код определяет политику `"MyPolicy"`CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="5bc31-192">Следующий код отменяет CORS `GetValues2` для действия:</span><span class="sxs-lookup"><span data-stu-id="5bc31-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="5bc31-193">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5bc31-193">The preceding code:</span></span>

* <span data-ttu-id="5bc31-194">Не позволяет CORS с [конечной точкий разгрома.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="5bc31-195">Не определяет [политику CORS по умолчанию.](#dp)</span><span class="sxs-lookup"><span data-stu-id="5bc31-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="5bc31-196">Использует [«EnableCors» («MyPolicy»)](#attr) для включения политики `"MyPolicy"` CORS для контроллера.</span><span class="sxs-lookup"><span data-stu-id="5bc31-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="5bc31-197">Отражает CORS `GetValues2` для метода.</span><span class="sxs-lookup"><span data-stu-id="5bc31-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="5bc31-198">Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [в Test CORS.](#testc)</span><span class="sxs-lookup"><span data-stu-id="5bc31-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="5bc31-199">Варианты политики CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-199">CORS policy options</span></span>

<span data-ttu-id="5bc31-200">В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="5bc31-201">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="5bc31-202">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="5bc31-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="5bc31-203">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="5bc31-204">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="5bc31-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="5bc31-205">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="5bc31-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="5bc31-206">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="5bc31-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="5bc31-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в .</span><span class="sxs-lookup"><span data-stu-id="5bc31-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5bc31-208">Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.</span><span class="sxs-lookup"><span data-stu-id="5bc31-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="5bc31-209">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-209">Set the allowed origins</span></span>

<span data-ttu-id="5bc31-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`).</span><span class="sxs-lookup"><span data-stu-id="5bc31-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="5bc31-211">`AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5bc31-212">Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт.</span><span class="sxs-lookup"><span data-stu-id="5bc31-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5bc31-213">Служба CORS возвращает недействительный ответ CORS, когда приложение настроено с помощью обоих методов.</span><span class="sxs-lookup"><span data-stu-id="5bc31-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="5bc31-214">`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="5bc31-215">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5bc31-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="5bc31-217">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="5bc31-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="5bc31-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="5bc31-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="5bc31-219">Позволяет любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="5bc31-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="5bc31-220">Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="5bc31-221">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="5bc31-222">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-222">Set the allowed request headers</span></span>

<span data-ttu-id="5bc31-223">Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали [заголовки запросов автора,](https://xhr.spec.whatwg.org/#request)позвоните и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="5bc31-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="5bc31-224">Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="5bc31-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="5bc31-225">`AllowAnyHeader`влияет на предполетные запросы и заголовок [заголовков Access-Control-Request-Headers.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)</span><span class="sxs-lookup"><span data-stu-id="5bc31-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="5bc31-226">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5bc31-227">Соответствие политики CORS Middleware к `WithHeaders` определенным заголовкам, указанному в, возможно только в том случае, если заголовки, отправленные в `Access-Control-Request-Headers` точном соответствии заголовкам, указанным в. `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="5bc31-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="5bc31-228">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5bc31-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="5bc31-229">CORS Middleware отклоняет предварительный запрос со `Content-Language` следующим заголовком запроса, потому `WithHeaders`что ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) не указан в:</span><span class="sxs-lookup"><span data-stu-id="5bc31-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="5bc31-230">Приложение возвращает *200 OK* ответ, но не отправить CORS заголовки обратно.</span><span class="sxs-lookup"><span data-stu-id="5bc31-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5bc31-231">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="5bc31-232">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="5bc31-232">Set the exposed response headers</span></span>

<span data-ttu-id="5bc31-233">По умолчанию браузер не предоставляет приложению все заголовки ответов.</span><span class="sxs-lookup"><span data-stu-id="5bc31-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="5bc31-234">Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="5bc31-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="5bc31-235">Заголовки ответов, доступные по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="5bc31-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="5bc31-236">Спецификация CORS называет эти заголовки *простыми заголовками ответов.*</span><span class="sxs-lookup"><span data-stu-id="5bc31-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="5bc31-237">Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="5bc31-238">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="5bc31-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="5bc31-239">Учетные данные требуют специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="5bc31-240">По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="5bc31-241">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="5bc31-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="5bc31-242">Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="5bc31-243">Использование `XMLHttpRequest` непосредственно:</span><span class="sxs-lookup"><span data-stu-id="5bc31-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="5bc31-244">Используя j'ери:</span><span class="sxs-lookup"><span data-stu-id="5bc31-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="5bc31-245">Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="5bc31-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="5bc31-246">Сервер должен разрешить учетные данные.</span><span class="sxs-lookup"><span data-stu-id="5bc31-246">The server must allow the credentials.</span></span> <span data-ttu-id="5bc31-247">Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="5bc31-248">Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="5bc31-249">Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.</span><span class="sxs-lookup"><span data-stu-id="5bc31-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="5bc31-250">Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="5bc31-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="5bc31-251">Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="5bc31-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="5bc31-252">Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.</span><span class="sxs-lookup"><span data-stu-id="5bc31-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="5bc31-253">Предполетные запросы</span><span class="sxs-lookup"><span data-stu-id="5bc31-253">Preflight requests</span></span>

<span data-ttu-id="5bc31-254">Для некоторых запросов CORS браузер отправляет дополнительный запрос [OPTIONS,](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) прежде чем сделать фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="5bc31-255">Этот запрос называется [предполетным запросом.](https://developer.mozilla.org/docs/Glossary/Preflight_request)</span><span class="sxs-lookup"><span data-stu-id="5bc31-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="5bc31-256">Браузер может пропустить предполетный запрос, если ***все*** следующие условия верны:</span><span class="sxs-lookup"><span data-stu-id="5bc31-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="5bc31-257">Метод запроса: GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="5bc31-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="5bc31-258">Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .</span><span class="sxs-lookup"><span data-stu-id="5bc31-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="5bc31-259">Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="5bc31-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="5bc31-260">Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект.</span><span class="sxs-lookup"><span data-stu-id="5bc31-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="5bc31-261">Спецификация CORS называет эти заголовки [автор запрос заголовки](https://www.w3.org/TR/cors/#author-request-headers).</span><span class="sxs-lookup"><span data-stu-id="5bc31-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="5bc31-262">Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="5bc31-263">Ниже приводится пример ответа, аналогичного предполетного запроса, сделанного из кнопки **«Put Test»** в разделе [Test CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="5bc31-264">Предполетный запрос использует метод [HTTP OPTIONS.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)</span><span class="sxs-lookup"><span data-stu-id="5bc31-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="5bc31-265">Он может включать в себя следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="5bc31-265">It may include the following headers:</span></span>

* <span data-ttu-id="5bc31-266">[Метод доступа-Контроль-Запрос](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="5bc31-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Список заголовков запросов, который приложение устанавливает на фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="5bc31-268">Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .</span><span class="sxs-lookup"><span data-stu-id="5bc31-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="5bc31-269">Методы контроля доступа-разрешить</span><span class="sxs-lookup"><span data-stu-id="5bc31-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="5bc31-270">Если предполетный запрос отклонен, приложение `200 OK` возвращает ответ, но не устанавливает заголовки CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="5bc31-271">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="5bc31-272">Пример отклоненного предполетного запроса можно просмотреть раздел [теста CORS](#testc) этого документа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="5bc31-273">Используя инструменты F12, консоль приложение показывает ошибку, похожую на одну из следующих, в зависимости от браузера:</span><span class="sxs-lookup"><span data-stu-id="5bc31-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="5bc31-274">Firefox: Кросс-Origin Запрос заблокирован: Та же политика происхождения `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`запрещает чтение удаленного ресурса на .</span><span class="sxs-lookup"><span data-stu-id="5bc31-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="5bc31-275">(Причина: запрос CORS не увенчался успехом).</span><span class="sxs-lookup"><span data-stu-id="5bc31-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="5bc31-276">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="5bc31-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="5bc31-277">Chromium на основе: Доступhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5к получению на ' от происхождения 'https://cors3.azurewebsites.netбыл заблокирован политикой CORS: Ответ на предполетный запрос не проходит проверку контроля доступа: нет заголовка 'Access-Control-Allow-Origin' на запрашиваемом ресурсе.</span><span class="sxs-lookup"><span data-stu-id="5bc31-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="5bc31-278">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="5bc31-279">Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="5bc31-280">Чтобы позволить всем [заголовкам запросов автора,](https://www.w3.org/TR/cors/#author-request-headers)позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="5bc31-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="5bc31-281">Браузеры не соответствуют тому, `Access-Control-Request-Headers`как они устанавливают.</span><span class="sxs-lookup"><span data-stu-id="5bc31-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="5bc31-282">Если какой-либо:</span><span class="sxs-lookup"><span data-stu-id="5bc31-282">If either:</span></span>

* <span data-ttu-id="5bc31-283">Заголовки настроены на что-либо, кроме`"*"`</span><span class="sxs-lookup"><span data-stu-id="5bc31-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="5bc31-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>называется: Включите `Content-Type`по `Origin`крайней мере `Accept`, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.</span><span class="sxs-lookup"><span data-stu-id="5bc31-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="5bc31-285">Автоматический код предполетного запроса</span><span class="sxs-lookup"><span data-stu-id="5bc31-285">Automatic preflight request code</span></span>

<span data-ttu-id="5bc31-286">При применении политики CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="5bc31-287">Глобально, позвонив `app.UseCors` в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="5bc31-288">Использование `[EnableCors]` атрибута.</span><span class="sxs-lookup"><span data-stu-id="5bc31-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="5bc31-289">ASP.NET Core отвечает на предполетный запрос OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="5bc31-290">Включение CORS на основе конечных `RequireCors` точек, используя в настоящее ***время, не*** поддерживает автоматические предполетные запросы.</span><span class="sxs-lookup"><span data-stu-id="5bc31-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="5bc31-291">Элемент ы в разделе [Test CORS](#testc) этого документа демонстрирует такое поведение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="5bc31-292">Атрибут «HttpOptions» для предполетных запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="5bc31-293">Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="5bc31-294">В некоторых сценариях это может быть не так.</span><span class="sxs-lookup"><span data-stu-id="5bc31-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="5bc31-295">Например, использование [CORS с конечным пунктом разгрома.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="5bc31-296">Следующий код использует атрибут [«HttpOptions»](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) для создания конечных точек для запросов OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="5bc31-297">Ознакомиться с инструкциями по тестированию предыдущего кода можно посмотреть [тест CORS с конечным точечным routing и «HttpOptions».](#tcer)</span><span class="sxs-lookup"><span data-stu-id="5bc31-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="5bc31-298">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="5bc31-298">Set the preflight expiration time</span></span>

<span data-ttu-id="5bc31-299">Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="5bc31-300">Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="5bc31-301">Как работает CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-301">How CORS works</span></span>

<span data-ttu-id="5bc31-302">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.</span><span class="sxs-lookup"><span data-stu-id="5bc31-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="5bc31-303">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="5bc31-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="5bc31-304">CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="5bc31-305">Например, злоумышленник может использовать [кросс-сайт сценариев (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнить запрос на кросс-сайт на свой сайт с поддержкой CORS для кражи информации.</span><span class="sxs-lookup"><span data-stu-id="5bc31-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="5bc31-306">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="5bc31-307">Клиент (браузер) должен обеспечить соблюдение CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="5bc31-308">Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="5bc31-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="5bc31-309">Например, любой из следующих инструментов будет отображать ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="5bc31-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="5bc31-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5bc31-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="5bc31-311">Postman</span><span class="sxs-lookup"><span data-stu-id="5bc31-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="5bc31-312">.NET httpclient</span><span class="sxs-lookup"><span data-stu-id="5bc31-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="5bc31-313">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="5bc31-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="5bc31-314">Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.</span><span class="sxs-lookup"><span data-stu-id="5bc31-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="5bc31-315">Браузеры без CORS не могут делать запросы на кросс-происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="5bc31-316">До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="5bc31-317">JSONP не использует XHR, он `<script>` использует тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="5bc31-318">Скрипты могут быть загружены кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="5bc31-319">[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="5bc31-320">Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="5bc31-321">Пользовательский код JavaScript не требуется для включения CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="5bc31-322">[Кнопка теста PUT](https://cors3.azurewebsites.net/test) на развернутом [образце](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="5bc31-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="5bc31-323">Ниже приводится пример запроса на перекрестное происхождение `https://cors1.azurewebsites.net/api/values`от кнопки тестирования [значений.](https://cors3.azurewebsites.net/)</span><span class="sxs-lookup"><span data-stu-id="5bc31-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="5bc31-324">Заголовок: `Origin`</span><span class="sxs-lookup"><span data-stu-id="5bc31-324">The `Origin` header:</span></span>

* <span data-ttu-id="5bc31-325">Предоставляет домен сайта, который делает запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="5bc31-326">Требуется и должен отличаться от хоста.</span><span class="sxs-lookup"><span data-stu-id="5bc31-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="5bc31-327">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="5bc31-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="5bc31-328">**Заголовки ответов**</span><span class="sxs-lookup"><span data-stu-id="5bc31-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="5bc31-329">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="5bc31-329">**Request headers**</span></span>

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

<span data-ttu-id="5bc31-330">В `OPTIONS` запросах сервер устанавливает заголовок **заголовков ответов** `Access-Control-Allow-Origin: {allowed origin}` в ответе.</span><span class="sxs-lookup"><span data-stu-id="5bc31-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="5bc31-331">Например, развернутый [образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), Удалить кнопку ["EnableCors"](https://cors1.azurewebsites.net/test?number=2) кнопка `OPTIONS` запрос содержит следующие заголовки:</span><span class="sxs-lookup"><span data-stu-id="5bc31-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="5bc31-332">**Общие заголовки**</span><span class="sxs-lookup"><span data-stu-id="5bc31-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="5bc31-333">**Заголовки ответов**</span><span class="sxs-lookup"><span data-stu-id="5bc31-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="5bc31-334">**Заголовки запросов**</span><span class="sxs-lookup"><span data-stu-id="5bc31-334">**Request headers**</span></span>

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

<span data-ttu-id="5bc31-335">В предыдущих **заголовках ответов**сервер устанавливает в ответ заголовок [Access-Control-Allow-Origin.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)</span><span class="sxs-lookup"><span data-stu-id="5bc31-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="5bc31-336">Значение `https://cors1.azurewebsites.net` этого заголовка совпадает с заголовком `Origin` из запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="5bc31-337">Если <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> называется, `Access-Control-Allow-Origin: *`значение подстановочного знака возвращается.</span><span class="sxs-lookup"><span data-stu-id="5bc31-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="5bc31-338">`AllowAnyOrigin`позволяет любого происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="5bc31-339">Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается.</span><span class="sxs-lookup"><span data-stu-id="5bc31-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="5bc31-340">В частности, браузер отдает запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="5bc31-341">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="5bc31-342">Отображение options запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-342">Display OPTIONS requests</span></span>

<span data-ttu-id="5bc31-343">По умолчанию браузеры Chrome и Edge не отображали запросы OPTIONS на сетевой вкладке инструментов F12.</span><span class="sxs-lookup"><span data-stu-id="5bc31-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="5bc31-344">Для отображения запросов OPTIONS в этих браузерах:</span><span class="sxs-lookup"><span data-stu-id="5bc31-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="5bc31-345">`chrome://flags/#out-of-blink-cors` или `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="5bc31-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="5bc31-346">отключить флаг.</span><span class="sxs-lookup"><span data-stu-id="5bc31-346">disable the flag.</span></span>
* <span data-ttu-id="5bc31-347">Перезапустить.</span><span class="sxs-lookup"><span data-stu-id="5bc31-347">restart.</span></span>

<span data-ttu-id="5bc31-348">Firefox показывает запросы OPTIONS по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5bc31-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="5bc31-349">КОРС в IIS</span><span class="sxs-lookup"><span data-stu-id="5bc31-349">CORS in IIS</span></span>

<span data-ttu-id="5bc31-350">При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="5bc31-351">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="5bc31-352">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-352">Test CORS</span></span>

<span data-ttu-id="5bc31-353">[В загрузке образца](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) есть код для тестирования CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="5bc31-354">См. раздел [Практическое руководство. Скачивание файла](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5bc31-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="5bc31-355">Образец представляет собой проект API с добавлением страниц Razor:</span><span class="sxs-lookup"><span data-stu-id="5bc31-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="5bc31-356">`WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5bc31-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="5bc31-357">`ValuesController` Следующие точки для тестирования:</span><span class="sxs-lookup"><span data-stu-id="5bc31-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="5bc31-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) предоставляется [пакетом Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet и отображает информацию о маршруте.</span><span class="sxs-lookup"><span data-stu-id="5bc31-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="5bc31-359">Проверьте предыдущий пример кода, используя один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="5bc31-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="5bc31-360">Используйте развернутое [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)приложение образца на .</span><span class="sxs-lookup"><span data-stu-id="5bc31-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="5bc31-361">Нет необходимости загружать образец.</span><span class="sxs-lookup"><span data-stu-id="5bc31-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="5bc31-362">Выполнить образец с `dotnet run` помощью URL-адреса по `https://localhost:5001`умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5bc31-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="5bc31-363">Выполнить образец из Visual Studio с портом установлен на `https://localhost:44398`44398 для URL .</span><span class="sxs-lookup"><span data-stu-id="5bc31-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="5bc31-364">Использование браузера с инструментами F12:</span><span class="sxs-lookup"><span data-stu-id="5bc31-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="5bc31-365">Выберите кнопку **Значения** и просмотрите заголовки во вкладке **Сети.**</span><span class="sxs-lookup"><span data-stu-id="5bc31-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="5bc31-366">Выберите кнопку **теста PUT.**</span><span class="sxs-lookup"><span data-stu-id="5bc31-366">Select the **PUT test** button.</span></span> <span data-ttu-id="5bc31-367">Смотрите [запросы Display OPTIONS](#options) для инструкций по отображению запроса OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="5bc31-368">**Тест PUT** создает два запроса: предполетный запрос OPTIONS и put-запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="5bc31-369">Выберите **`GetValues2 [DisableCors]`** кнопку, чтобы вызвать неудавшийся запрос CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="5bc31-370">Как уже упоминалось в документе, ответ возвращает 200 успехов, но запрос CORS не сделан.</span><span class="sxs-lookup"><span data-stu-id="5bc31-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="5bc31-371">Выберите вкладку **Консоль,** чтобы увидеть ошибку CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="5bc31-372">В зависимости от браузера отображается ошибка, аналогичная следующему:</span><span class="sxs-lookup"><span data-stu-id="5bc31-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="5bc31-373">Доступ к `'https://cors1.azurewebsites.net/api/values/GetValues2'` получению `'https://cors3.azurewebsites.net'` из происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка «Доступ-контроль-разрешить-Origin».</span><span class="sxs-lookup"><span data-stu-id="5bc31-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="5bc31-374">Если этот непрозрачный ответ вам подходит, задайте для режима запроса значение "no-cors", чтобы извлечь ресурс с отключенным параметром CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="5bc31-375">Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [локон,](https://curl.haxx.se/) [Скрипач](https://www.telerik.com/fiddler)или [Почтальон.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="5bc31-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="5bc31-376">При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей.</span><span class="sxs-lookup"><span data-stu-id="5bc31-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="5bc31-377">Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:</span><span class="sxs-lookup"><span data-stu-id="5bc31-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="5bc31-378">Для обработки запроса CORS Middleware не нужно.</span><span class="sxs-lookup"><span data-stu-id="5bc31-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="5bc31-379">Заголовки CORS не возвращаются в ответ.</span><span class="sxs-lookup"><span data-stu-id="5bc31-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="5bc31-380">Следующая команда `curl` использует для выдачи запроса OPTIONS с информацией:</span><span class="sxs-lookup"><span data-stu-id="5bc31-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="5bc31-381">Тест CORS с конечным точечным разгромом и «HttpOptions»</span><span class="sxs-lookup"><span data-stu-id="5bc31-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="5bc31-382">Включение CORS на основе конечных `RequireCors` точек использования в настоящее время ***не*** поддерживает [автоматические предполетные запросы.](#apf)</span><span class="sxs-lookup"><span data-stu-id="5bc31-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="5bc31-383">Рассмотрим следующий код, который использует [конечную точку разгрома для включения CORS:](#ecors)</span><span class="sxs-lookup"><span data-stu-id="5bc31-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="5bc31-384">`TodoItems1Controller` Следующие конечные точки для тестирования:</span><span class="sxs-lookup"><span data-stu-id="5bc31-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="5bc31-385">Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=1) развернутого [образца.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="5bc31-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="5bc31-386">**Кнопки «Удалить» и** **«GET» (EnableCors)** удаляют `[EnableCors]` ся, так как конечные точки имеют и отвечают на предполетные запросы.</span><span class="sxs-lookup"><span data-stu-id="5bc31-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="5bc31-387">Другие конечные точки не удается.</span><span class="sxs-lookup"><span data-stu-id="5bc31-387">The other endpoints fails.</span></span> <span data-ttu-id="5bc31-388">Кнопка **GET** выходит из строя, так как [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) отправляет:</span><span class="sxs-lookup"><span data-stu-id="5bc31-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="5bc31-389">Следующие `TodoItems2Controller` конечные точки, но включает в себя явный код для ответа на запросы OPTIONS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="5bc31-390">Проверьте предыдущий код со [страницы тестирования](https://cors1.azurewebsites.net/test?number=2) развернутого образца.</span><span class="sxs-lookup"><span data-stu-id="5bc31-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="5bc31-391">В **контроллере** упасть список, выберите **Preflight,** а затем **установить контроллер.**</span><span class="sxs-lookup"><span data-stu-id="5bc31-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="5bc31-392">Все вызовы CORS `TodoItems2Controller` в конечные точки увенчаются успехом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bc31-393">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5bc31-393">Additional resources</span></span>

* [<span data-ttu-id="5bc31-394">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="5bc31-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="5bc31-395">Начало работы с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5bc31-396">Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5bc31-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5bc31-397">В этой статье показано, как включить CORS в ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="5bc31-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="5bc31-398">Безопасность браузера предотвращает запросы на веб-страницу в другой домен, чем тот, который обслуживал веб-страницу.</span><span class="sxs-lookup"><span data-stu-id="5bc31-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5bc31-399">Это ограничение называется *политикой одного и того же происхождения.*</span><span class="sxs-lookup"><span data-stu-id="5bc31-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="5bc31-400">Политика того же происхождения не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта.</span><span class="sxs-lookup"><span data-stu-id="5bc31-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="5bc31-401">Иногда может потребоваться разрешить другим сайтам делать запросы на перекрестное происхождение в вашем приложении.</span><span class="sxs-lookup"><span data-stu-id="5bc31-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="5bc31-402">Для получения дополнительной информации, [см.](https://developer.mozilla.org/docs/Web/HTTP/CORS)</span><span class="sxs-lookup"><span data-stu-id="5bc31-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="5bc31-403">[Совместное использование ресурсов Cross Origin](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="5bc31-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="5bc31-404">Является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="5bc31-405">Это **не** функция безопасности, CORS ослабляет безопасность.</span><span class="sxs-lookup"><span data-stu-id="5bc31-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="5bc31-406">API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="5bc31-407">Для получения дополнительной информации [смотрите, как работает CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="5bc31-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="5bc31-408">Позволяет серверу явно разрешать некоторые запросы кросс-происхождения, отклоняя другие.</span><span class="sxs-lookup"><span data-stu-id="5bc31-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="5bc31-409">Безопаснее и гибче, чем более ранние методы, такие как [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="5bc31-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="5bc31-410">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5bc31-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="5bc31-411">То же происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-411">Same origin</span></span>

<span data-ttu-id="5bc31-412">Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="5bc31-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="5bc31-413">Эти два URL-адреса имеют одно и то же происхождение:</span><span class="sxs-lookup"><span data-stu-id="5bc31-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="5bc31-414">Эти URL-адреса имеют различное происхождение, чем предыдущие два URL-адреса:</span><span class="sxs-lookup"><span data-stu-id="5bc31-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="5bc31-415">`https://example.net`&ndash; Различные домены</span><span class="sxs-lookup"><span data-stu-id="5bc31-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="5bc31-416">`https://www.example.com/foo.html`&ndash; Различные поддомены</span><span class="sxs-lookup"><span data-stu-id="5bc31-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="5bc31-417">`http://example.com/foo.html`&ndash; Различная схема</span><span class="sxs-lookup"><span data-stu-id="5bc31-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="5bc31-418">`https://example.com:9000/foo.html`&ndash; Различные порты</span><span class="sxs-lookup"><span data-stu-id="5bc31-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="5bc31-419">Internet Explorer не учитывает порт при сравнении происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="5bc31-420">CORS с названной политикой и программой</span><span class="sxs-lookup"><span data-stu-id="5bc31-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="5bc31-421">CORS Middleware обрабатывает запросы по перекрестному происхождению.</span><span class="sxs-lookup"><span data-stu-id="5bc31-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="5bc31-422">Следующий код позволяет CORS для всего приложения с указанным происхождением:</span><span class="sxs-lookup"><span data-stu-id="5bc31-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="5bc31-423">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5bc31-423">The preceding code:</span></span>

* <span data-ttu-id="5bc31-424">Устанавливает название полиса\_на "myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="5bc31-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="5bc31-425">Название политики является произвольным.</span><span class="sxs-lookup"><span data-stu-id="5bc31-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="5bc31-426">Вызывает <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> метод расширения, который позволяет CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="5bc31-427">Звонки <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> с [выражением лямбды](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="5bc31-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="5bc31-428">Ламбда берет <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> предмет.</span><span class="sxs-lookup"><span data-stu-id="5bc31-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="5bc31-429">[Параметры конфигурации,](#cors-policy-options)такие как `WithOrigins`, описаны позже в этой статье.</span><span class="sxs-lookup"><span data-stu-id="5bc31-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="5bc31-430">Вызов <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> метода добавляет услуги CORS в контейнер обслуживания приложения:</span><span class="sxs-lookup"><span data-stu-id="5bc31-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="5bc31-431">Для получения дополнительной информации [в](#cpo) этом документе см.</span><span class="sxs-lookup"><span data-stu-id="5bc31-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="5bc31-432">Метод <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> может цепиметоды, как показано в следующем коде:</span><span class="sxs-lookup"><span data-stu-id="5bc31-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="5bc31-433">Примечание: **URL-адрес не** должен содержать`/`задняя черта ().</span><span class="sxs-lookup"><span data-stu-id="5bc31-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="5bc31-434">Если URL завершается `/`с помощью, сравнение возвращается `false` и не возвращается заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="5bc31-435">Следующий код применяет политики CORS ко всем конечным точкам приложений через CORS Middleware:</span><span class="sxs-lookup"><span data-stu-id="5bc31-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="5bc31-436">Примечание: `UseCors` необходимо вызвать перед `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="5bc31-437">[См. Включить CORS в Razor Страницы, контроллеры и методы действий](#ecors) для применения политики CORS на странице / контроллер / действие уровне.</span><span class="sxs-lookup"><span data-stu-id="5bc31-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="5bc31-438">Ознакомиться с [инструкциями](#test) по коду тестирования, аналогичным предыдущему коду, можно ознакомиться с тестом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="5bc31-439">Включить CORS с атрибутами</span><span class="sxs-lookup"><span data-stu-id="5bc31-439">Enable CORS with attributes</span></span>

<span data-ttu-id="5bc31-440">Атрибут [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) является альтернативой применению CORS по всему миру.</span><span class="sxs-lookup"><span data-stu-id="5bc31-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="5bc31-441">Атрибут `[EnableCors]` позволяет CORS для выбранных конечных точек, а не для всех конечных точек.</span><span class="sxs-lookup"><span data-stu-id="5bc31-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="5bc31-442">Используется `[EnableCors]` для указания политики по умолчанию и `[EnableCors("{Policy String}")]` для указания политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="5bc31-443">Атрибут `[EnableCors]` может быть применен к:</span><span class="sxs-lookup"><span data-stu-id="5bc31-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="5bc31-444">Страница бритвы`PageModel`</span><span class="sxs-lookup"><span data-stu-id="5bc31-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="5bc31-445">Контроллер</span><span class="sxs-lookup"><span data-stu-id="5bc31-445">Controller</span></span>
* <span data-ttu-id="5bc31-446">Метод действия контроллера</span><span class="sxs-lookup"><span data-stu-id="5bc31-446">Controller action method</span></span>

<span data-ttu-id="5bc31-447">Можно применить различные политики к контроллеру/странице-модели/действию с атрибутом. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="5bc31-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="5bc31-448">Когда `[EnableCors]` атрибут применяется к методу модели/модели/действий контроллеров/страниц, а CORS включен в промежуточном программном обеспечении, ***применяются обе*** политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="5bc31-449">Мы рекомендуем ***не*** комбинировать политики.</span><span class="sxs-lookup"><span data-stu-id="5bc31-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="5bc31-450">Используйте `[EnableCors]` атрибут или промежуточное программное обеспечение, и**то, и другое.**</span><span class="sxs-lookup"><span data-stu-id="5bc31-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="5bc31-451">При `[EnableCors]`использовании **не** определяется политика по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5bc31-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="5bc31-452">Следующий код применяет различную политику к каждому методу:</span><span class="sxs-lookup"><span data-stu-id="5bc31-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="5bc31-453">Следующий код создает политику по умолчанию `"AnotherPolicy"`CORS и политику под названием:</span><span class="sxs-lookup"><span data-stu-id="5bc31-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="5bc31-454">Отключить CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-454">Disable CORS</span></span>

<span data-ttu-id="5bc31-455">[ &lbrack;Атрибут DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) отстраняет CORS от контроллера/страницы-модели/действия.</span><span class="sxs-lookup"><span data-stu-id="5bc31-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="5bc31-456">Варианты политики CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-456">CORS policy options</span></span>

<span data-ttu-id="5bc31-457">В этом разделе описаны различные параметры, которые могут быть установлены в политике CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="5bc31-458">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="5bc31-459">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="5bc31-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="5bc31-460">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="5bc31-461">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="5bc31-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="5bc31-462">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="5bc31-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="5bc31-463">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="5bc31-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="5bc31-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>вызывается `Startup.ConfigureServices`в .</span><span class="sxs-lookup"><span data-stu-id="5bc31-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5bc31-465">Для некоторых вариантов, это может быть полезно прочитать [Как CORS работает](#how-cors) раздел в первую очередь.</span><span class="sxs-lookup"><span data-stu-id="5bc31-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="5bc31-466">Установить допустимое происхождение</span><span class="sxs-lookup"><span data-stu-id="5bc31-466">Set the allowed origins</span></span>

<span data-ttu-id="5bc31-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Позволяет CORS запросы от всех`http` происхождения с любой схемой (или `https`).</span><span class="sxs-lookup"><span data-stu-id="5bc31-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="5bc31-468">`AllowAnyOrigin`является небезопасным, потому что *любой веб-сайт* может сделать кросс-происхождения запросов на приложение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="5bc31-469">Определение `AllowAnyOrigin` и `AllowCredentials` является небезопасной конфигурацией и может привести к подделке запросов на перекрестный сайт.</span><span class="sxs-lookup"><span data-stu-id="5bc31-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="5bc31-470">Для безопасного приложения укажите точный список истоков, если клиент должен разрешить себе доступ к серверным ресурсам.</span><span class="sxs-lookup"><span data-stu-id="5bc31-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="5bc31-471">`AllowAnyOrigin`влияет на предполетные `Access-Control-Allow-Origin` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="5bc31-472">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5bc31-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Устанавливает <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> свойство политики как функцию, позволяющую origins соответствовать настроенной домен подстановочного знака при оценке, разрешено ли происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="5bc31-474">Установите допустимые методы HTTP</span><span class="sxs-lookup"><span data-stu-id="5bc31-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="5bc31-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="5bc31-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="5bc31-476">Позволяет любой метод HTTP:</span><span class="sxs-lookup"><span data-stu-id="5bc31-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="5bc31-477">Влияет на предполетные `Access-Control-Allow-Methods` запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="5bc31-478">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="5bc31-479">Установите заголовок разрешенных запросов</span><span class="sxs-lookup"><span data-stu-id="5bc31-479">Set the allowed request headers</span></span>

<span data-ttu-id="5bc31-480">Чтобы разрешить отправку конкретных заголовков в запросе CORS, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> вызвали *заголовки запросов автора,* позвоните и укажите разрешенные заголовки:</span><span class="sxs-lookup"><span data-stu-id="5bc31-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5bc31-481">Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="5bc31-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5bc31-482">Эта настройка влияет на `Access-Control-Request-Headers` предполетные запросы и заголовок.</span><span class="sxs-lookup"><span data-stu-id="5bc31-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="5bc31-483">Для получения дополнительной информации смотрите раздел [Предполетных запросов.](#preflight-requests)</span><span class="sxs-lookup"><span data-stu-id="5bc31-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="5bc31-484">CORS Middleware всегда позволяет отправлять `Access-Control-Request-Headers` четыре заголовка, которые должны быть отправлены независимо от значений, настроенных в CorsPolicy.Headers.</span><span class="sxs-lookup"><span data-stu-id="5bc31-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="5bc31-485">Этот список заголовков включает в себя:</span><span class="sxs-lookup"><span data-stu-id="5bc31-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="5bc31-486">Например, рассмотрим приложение, настроенное следующим образом:</span><span class="sxs-lookup"><span data-stu-id="5bc31-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="5bc31-487">CORS Middleware успешно отвечает на предполетный запрос следующим `Content-Language` заголовком запроса, поскольку всегда находится в белом списке:</span><span class="sxs-lookup"><span data-stu-id="5bc31-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="5bc31-488">Установите открытые заголовки ответов</span><span class="sxs-lookup"><span data-stu-id="5bc31-488">Set the exposed response headers</span></span>

<span data-ttu-id="5bc31-489">По умолчанию браузер не предоставляет приложению все заголовки ответов.</span><span class="sxs-lookup"><span data-stu-id="5bc31-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="5bc31-490">Для получения дополнительной информации см. [W3C Cross-Origin Resource Sharing (Терминология): Простой заголовок ответа](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="5bc31-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="5bc31-491">Заголовки ответов, доступные по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="5bc31-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="5bc31-492">Спецификация CORS называет эти заголовки *простыми заголовками ответов.*</span><span class="sxs-lookup"><span data-stu-id="5bc31-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="5bc31-493">Чтобы сделать другие заголовки <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>доступными для приложения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="5bc31-494">Учетные данные в запросах по перекрестному происхождению</span><span class="sxs-lookup"><span data-stu-id="5bc31-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="5bc31-495">Учетные данные требуют специальной обработки в запросе CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="5bc31-496">По умолчанию браузер не отправляет учетные данные с запросом кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="5bc31-497">Учетные данные включают файлы cookie и схемы проверки подлинности HTTP.</span><span class="sxs-lookup"><span data-stu-id="5bc31-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="5bc31-498">Чтобы отправить учетные данные с запросом `XMLHttpRequest.withCredentials` кросс-происхождения, клиент должен установить на `true`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="5bc31-499">Использование `XMLHttpRequest` непосредственно:</span><span class="sxs-lookup"><span data-stu-id="5bc31-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="5bc31-500">Используя j'ери:</span><span class="sxs-lookup"><span data-stu-id="5bc31-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="5bc31-501">Использование [API Fetch:](https://developer.mozilla.org/docs/Web/API/Fetch_API)</span><span class="sxs-lookup"><span data-stu-id="5bc31-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="5bc31-502">Сервер должен разрешить учетные данные.</span><span class="sxs-lookup"><span data-stu-id="5bc31-502">The server must allow the credentials.</span></span> <span data-ttu-id="5bc31-503">Чтобы разрешить учетные данные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>кросс-происхождения, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="5bc31-504">Ответ HTTP включает `Access-Control-Allow-Credentials` в себя заголовок, который сообщает браузеру, что сервер позволяет учетные данные для запроса кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="5bc31-505">Если браузер отправляет учетные данные, но ответ `Access-Control-Allow-Credentials` не включает действительный заголовок, браузер не предоставляет ответ приложению, а запрос о перекрестном происхождении не выполняется.</span><span class="sxs-lookup"><span data-stu-id="5bc31-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="5bc31-506">Разрешение учетных данных по перекрестному происхождению представляет собой угрозу безопасности.</span><span class="sxs-lookup"><span data-stu-id="5bc31-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="5bc31-507">Веб-сайт в другом домене может отправлять учетные данные пользователей, вписанных в приложение, от имени пользователя без ведома пользователя.</span><span class="sxs-lookup"><span data-stu-id="5bc31-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="5bc31-508">Спецификация CORS также гласит, `"*"` что установка происхождения (всех `Access-Control-Allow-Credentials` истоков) является недействительной, если заголовок присутствует.</span><span class="sxs-lookup"><span data-stu-id="5bc31-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="5bc31-509">Предполетные запросы</span><span class="sxs-lookup"><span data-stu-id="5bc31-509">Preflight requests</span></span>

<span data-ttu-id="5bc31-510">Для некоторых запросов CORS браузер отправляет дополнительный запрос, прежде чем сделать фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="5bc31-511">Этот запрос называется *предполетным запросом.*</span><span class="sxs-lookup"><span data-stu-id="5bc31-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="5bc31-512">Браузер может пропустить предполетный запрос, если верны следующие условия:</span><span class="sxs-lookup"><span data-stu-id="5bc31-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="5bc31-513">Метод запроса: GET, HEAD или POST.</span><span class="sxs-lookup"><span data-stu-id="5bc31-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="5bc31-514">Приложение не устанавливает заголовки запросов, `Accept-Language` `Content-Language`кроме `Content-Type` `Accept`, `Last-Event-ID`, , или .</span><span class="sxs-lookup"><span data-stu-id="5bc31-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="5bc31-515">Заголовок, `Content-Type` если он установлен, имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="5bc31-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="5bc31-516">Правило заголовков запросов, установленных для запроса клиента, применяется `setRequestHeader` к `XMLHttpRequest` заголовкам, которые приложение устанавливает, вызывая объект.</span><span class="sxs-lookup"><span data-stu-id="5bc31-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="5bc31-517">Спецификация CORS называет эти заголовки *автор запрос заголовки*.</span><span class="sxs-lookup"><span data-stu-id="5bc31-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="5bc31-518">Правило не распространяется на заголовки, которые может `User-Agent`установить браузер, такие как , `Host`или `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="5bc31-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="5bc31-519">Ниже приводится пример предполетного запроса:</span><span class="sxs-lookup"><span data-stu-id="5bc31-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="5bc31-520">Предполетный запрос использует метод HTTP OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="5bc31-521">Она включает в себя два специальных заголовка:</span><span class="sxs-lookup"><span data-stu-id="5bc31-521">It includes two special headers:</span></span>

* <span data-ttu-id="5bc31-522">`Access-Control-Request-Method`: Метод HTTP, который будет использоваться для фактического запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="5bc31-523">`Access-Control-Request-Headers`: Список заголовков запросов, которые приложение устанавливает на фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="5bc31-524">Как указывалось ранее, это не включает заголовки, `User-Agent`которые устанавливает браузер, такие как .</span><span class="sxs-lookup"><span data-stu-id="5bc31-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="5bc31-525">Когда CORS включен с соответствующей политикой, ASP.NET Core обычно автоматически отвечает на предполетные запросы CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="5bc31-526">Смотрите [атрибут «HttpOptions» для предполетных запросов.](#pro)</span><span class="sxs-lookup"><span data-stu-id="5bc31-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="5bc31-527">Предполетный запрос CORS `Access-Control-Request-Headers` может включать заголовок, который указывает на сервер заголовки, которые отправляются с фактическим запросом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="5bc31-528">Чтобы разрешить конкретные <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>заголовки, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="5bc31-529">Чтобы позволить всем заголовкам запросов автора, позвоните: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="5bc31-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="5bc31-530">Браузеры не полностью последовательны в `Access-Control-Request-Headers`том, как они устанавливают.</span><span class="sxs-lookup"><span data-stu-id="5bc31-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="5bc31-531">Если вы установите заголовки `"*"` на <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>что-либо, кроме `Accept` `Content-Type`(или `Origin`использовать), вы должны включить по крайней мере, , и , а также любые пользовательские заголовки, которые вы хотите поддержать.</span><span class="sxs-lookup"><span data-stu-id="5bc31-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="5bc31-532">Ниже приводится пример ответа на предполетный запрос (при условии, что сервер позволяет запрос):</span><span class="sxs-lookup"><span data-stu-id="5bc31-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="5bc31-533">Ответ включает `Access-Control-Allow-Methods` в себя заголовок, который перечисляет `Access-Control-Allow-Headers` разрешенные методы и опционально заголовок, в котором перечислены разрешенные заголовки.</span><span class="sxs-lookup"><span data-stu-id="5bc31-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="5bc31-534">Если предполетный запрос удается, браузер отправляет фактический запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="5bc31-535">Если предполетный запрос отклонен, приложение возвращает *200 OK* ответ, но не отправляет заголовки CORS обратно.</span><span class="sxs-lookup"><span data-stu-id="5bc31-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="5bc31-536">Таким образом, браузер не пытается кросс-происхождения запроса.</span><span class="sxs-lookup"><span data-stu-id="5bc31-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="5bc31-537">Установить предполетный срок годности</span><span class="sxs-lookup"><span data-stu-id="5bc31-537">Set the preflight expiration time</span></span>

<span data-ttu-id="5bc31-538">Заголовок `Access-Control-Max-Age` определяет, как долго можно кэшировать ответ на предполетный запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="5bc31-539">Чтобы установить этот <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>заголовок, позвоните:</span><span class="sxs-lookup"><span data-stu-id="5bc31-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="5bc31-540">Как работает CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-540">How CORS works</span></span>

<span data-ttu-id="5bc31-541">В этом разделе описывается, что происходит в запросе [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) на уровне сообщений HTTP.</span><span class="sxs-lookup"><span data-stu-id="5bc31-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="5bc31-542">CORS **не** является функцией безопасности.</span><span class="sxs-lookup"><span data-stu-id="5bc31-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="5bc31-543">CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="5bc31-544">Например, злоумышленник может использовать prevent [cross-Site Scripting (XSS)](xref:security/cross-site-scripting) против вашего сайта и выполнять запрос на перекрестный сайт на свой сайт с поддержкой CORS для кражи информации.</span><span class="sxs-lookup"><span data-stu-id="5bc31-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="5bc31-545">Ваш API не является более безопасным, позволяя CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="5bc31-546">Клиент (браузер) должен обеспечить соблюдение CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="5bc31-547">Сервер выполняет запрос и возвращает ответ, это клиент, который возвращает ошибку и блокирует ответ.</span><span class="sxs-lookup"><span data-stu-id="5bc31-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="5bc31-548">Например, любой из следующих инструментов будет отображать ответ сервера:</span><span class="sxs-lookup"><span data-stu-id="5bc31-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="5bc31-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="5bc31-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="5bc31-550">Postman</span><span class="sxs-lookup"><span data-stu-id="5bc31-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="5bc31-551">.NET httpclient</span><span class="sxs-lookup"><span data-stu-id="5bc31-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="5bc31-552">Веб-браузер, введя URL-адрес в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="5bc31-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="5bc31-553">Это способ для сервера, чтобы позволить браузерам выполнять кросс-происхождения [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) или [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) запрос, который в противном случае будет запрещено.</span><span class="sxs-lookup"><span data-stu-id="5bc31-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="5bc31-554">Браузеры (без CORS) не могут делать запросы на кросс-происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="5bc31-555">До [CORS, JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) был использован, чтобы обойти это ограничение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="5bc31-556">JSONP не использует XHR, он `<script>` использует тег для получения ответа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="5bc31-557">Скрипты могут быть загружены кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="5bc31-558">[Спецификация CORS](https://www.w3.org/TR/cors/) представила несколько новых заголовков HTTP, которые позволяют запросы на перекрестное происхождение.</span><span class="sxs-lookup"><span data-stu-id="5bc31-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="5bc31-559">Если браузер поддерживает CORS, он автоматически устанавливает эти заголовки для запросов кросс-происхождения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="5bc31-560">Пользовательский код JavaScript не требуется для включения CORS.</span><span class="sxs-lookup"><span data-stu-id="5bc31-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="5bc31-561">Ниже приводится пример запроса о перекрестном происхождении.</span><span class="sxs-lookup"><span data-stu-id="5bc31-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="5bc31-562">Заголовок `Origin` предоставляет домен сайта, который делает запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="5bc31-563">Заголовок `Origin` необходим и должен отличаться от хостена.</span><span class="sxs-lookup"><span data-stu-id="5bc31-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="5bc31-564">Если сервер разрешает запрос, он `Access-Control-Allow-Origin` устанавливает заголовок в ответе.</span><span class="sxs-lookup"><span data-stu-id="5bc31-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="5bc31-565">Значение этого заголовка либо `Origin` соответствует заголовку из запроса, `"*"`либо является значением подстановочного знака, что означает, что любое происхождение разрешено:</span><span class="sxs-lookup"><span data-stu-id="5bc31-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="5bc31-566">Если ответ не включает `Access-Control-Allow-Origin` заголовок, запрос на перекрестное происхождение не удается.</span><span class="sxs-lookup"><span data-stu-id="5bc31-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="5bc31-567">В частности, браузер отдает запрос.</span><span class="sxs-lookup"><span data-stu-id="5bc31-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="5bc31-568">Даже если сервер возвращает успешный ответ, браузер не делает ответ доступным для клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="5bc31-569">Тестирование CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-569">Test CORS</span></span>

<span data-ttu-id="5bc31-570">Для тестирования CORS:</span><span class="sxs-lookup"><span data-stu-id="5bc31-570">To test CORS:</span></span>

1. <span data-ttu-id="5bc31-571">[Создайте проект API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="5bc31-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="5bc31-572">Кроме того, вы можете [скачать образец](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5bc31-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="5bc31-573">Включите CORS, используя один из подходов в этом документе.</span><span class="sxs-lookup"><span data-stu-id="5bc31-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="5bc31-574">Пример:</span><span class="sxs-lookup"><span data-stu-id="5bc31-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="5bc31-575">`WithOrigins("https://localhost:<port>");`должны использоваться только для тестирования образца приложения, аналогичного [коду загрузки образца](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="5bc31-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="5bc31-576">Создайте проект веб-приложений (Страницы razor или MVC).</span><span class="sxs-lookup"><span data-stu-id="5bc31-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="5bc31-577">В образце используются страницы razor.</span><span class="sxs-lookup"><span data-stu-id="5bc31-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="5bc31-578">Вы можете создать веб-приложение в том же решении, что и проект API.</span><span class="sxs-lookup"><span data-stu-id="5bc31-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="5bc31-579">Добавьте следующий выделенный код в файл *Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="5bc31-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="5bc31-580">В предыдущем коде `url: 'https://<web app>.azurewebsites.net/api/values/1',` замените URL-адрес развернутого приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="5bc31-581">Развертывание проекта API.</span><span class="sxs-lookup"><span data-stu-id="5bc31-581">Deploy the API project.</span></span> <span data-ttu-id="5bc31-582">Например, [развернуть в Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="5bc31-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="5bc31-583">Выполнить Razor Pages или MVC приложение с рабочего стола и нажмите на кнопку **испытаний.**</span><span class="sxs-lookup"><span data-stu-id="5bc31-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="5bc31-584">Используйте инструменты F12 для проверки сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="5bc31-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="5bc31-585">Удалите происхождение `WithOrigins` локального хозяина из приложения и разместите его.</span><span class="sxs-lookup"><span data-stu-id="5bc31-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="5bc31-586">Кроме того, запустите клиентское приложение с другим портом.</span><span class="sxs-lookup"><span data-stu-id="5bc31-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="5bc31-587">Например, запустить из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5bc31-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="5bc31-588">Тест с помощью клиентского приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-588">Test with the client app.</span></span> <span data-ttu-id="5bc31-589">Сбои CORS возвращают ошибку, но сообщение об ошибке недоступно для JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5bc31-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="5bc31-590">Используйте консольную вкладку в инструментах F12, чтобы увидеть ошибку.</span><span class="sxs-lookup"><span data-stu-id="5bc31-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="5bc31-591">В зависимости от браузера, вы получаете ошибку (в f12 консоли инструментов) похож на следующее:</span><span class="sxs-lookup"><span data-stu-id="5bc31-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="5bc31-592">Использование Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="5bc31-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="5bc31-593">**SEC7120: «CORS» `https://localhost:44375` Происхождение не `https://localhost:44375` было нашло в заголовке ответа Access-Control-Allow-Origin для ресурса кросс-происхождения в`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="5bc31-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="5bc31-594">Использование Chrome:</span><span class="sxs-lookup"><span data-stu-id="5bc31-594">Using Chrome:</span></span>

     <span data-ttu-id="5bc31-595">**Доступ к XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` от происхождения был заблокирован политикой CORS: на запрашиваемом ресурсе нет заголовка "Доступ-контроль-Разрешить-Происхождение".**</span><span class="sxs-lookup"><span data-stu-id="5bc31-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="5bc31-596">Конечные точки с поддержкой CORS могут быть протестированы с помощью инструмента, такого как [Fiddler](https://www.telerik.com/fiddler) или [Postman.](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="5bc31-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="5bc31-597">При использовании инструмента происхождение запроса, `Origin` указанного заголовком, должно отличаться от принимающей стороны, принимающей, принимающей.</span><span class="sxs-lookup"><span data-stu-id="5bc31-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="5bc31-598">Если запрос не является *перекрестным происхождением* `Origin` на основе значения заголовка:</span><span class="sxs-lookup"><span data-stu-id="5bc31-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="5bc31-599">Для обработки запроса CORS Middleware не нужно.</span><span class="sxs-lookup"><span data-stu-id="5bc31-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="5bc31-600">Заголовки CORS не возвращаются в ответ.</span><span class="sxs-lookup"><span data-stu-id="5bc31-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="5bc31-601">КОРС в IIS</span><span class="sxs-lookup"><span data-stu-id="5bc31-601">CORS in IIS</span></span>

<span data-ttu-id="5bc31-602">При развертывании в IIS CORS должен работать до проверки подлинности Windows, если сервер не настроен для анонимного доступа.</span><span class="sxs-lookup"><span data-stu-id="5bc31-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="5bc31-603">Для поддержки этого сценария необходимо установить и настроить [модуль IIS CORS](https://www.iis.net/downloads/microsoft/iis-cors-module) для приложения.</span><span class="sxs-lookup"><span data-stu-id="5bc31-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5bc31-604">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5bc31-604">Additional resources</span></span>

* [<span data-ttu-id="5bc31-605">Общий доступ к ресурсам независимо от источника (CORS)</span><span class="sxs-lookup"><span data-stu-id="5bc31-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="5bc31-606">Начало работы с модулем IIS CORS</span><span class="sxs-lookup"><span data-stu-id="5bc31-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
