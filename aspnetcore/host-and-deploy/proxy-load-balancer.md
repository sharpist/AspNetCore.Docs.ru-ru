---
title: Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки
author: rick-anderson
description: Сведения о конфигурации приложений, размещаемых за прокси-серверами и подсистемами балансировки нагрузки, которые могут мешать передаче важных сведений в запросах.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: ad4c3bbb30a672dcd56b51fb949285c9da326c96
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074341"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="7c4d6-103">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="7c4d6-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="7c4d6-104">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="7c4d6-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c4d6-105">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="7c4d6-106">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="7c4d6-107">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="7c4d6-108">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="7c4d6-109">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="7c4d6-110">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="7c4d6-110">Forwarded headers</span></span>

<span data-ttu-id="7c4d6-111">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="7c4d6-112">Header</span><span class="sxs-lookup"><span data-stu-id="7c4d6-112">Header</span></span> | <span data-ttu-id="7c4d6-113">Описание</span><span class="sxs-lookup"><span data-stu-id="7c4d6-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="7c4d6-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="7c4d6-114">X-Forwarded-For</span></span> | <span data-ttu-id="7c4d6-115">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="7c4d6-116">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="7c4d6-117">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="7c4d6-118">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="7c4d6-119">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="7c4d6-120">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="7c4d6-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="7c4d6-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="7c4d6-122">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="7c4d6-123">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="7c4d6-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="7c4d6-124">X-Forwarded-Host</span></span> | <span data-ttu-id="7c4d6-125">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-125">The original value of the Host header field.</span></span> <span data-ttu-id="7c4d6-126">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="7c4d6-127">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="7c4d6-128">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="7c4d6-129">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-129">The middleware updates:</span></span>

* <span data-ttu-id="7c4d6-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="7c4d6-131">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="7c4d6-132">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="7c4d6-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="7c4d6-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="7c4d6-135">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="7c4d6-136">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-136">The default settings are:</span></span>

* <span data-ttu-id="7c4d6-137">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="7c4d6-138">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="7c4d6-139">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="7c4d6-140">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="7c4d6-141">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="7c4d6-142">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="7c4d6-143">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="7c4d6-144">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c4d6-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="7c4d6-145">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="7c4d6-146">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="7c4d6-147">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="7c4d6-148">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7c4d6-149">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="7c4d6-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7c4d6-150">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="7c4d6-151">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="7c4d6-152">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="7c4d6-153">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="7c4d6-154">Порядок ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="7c4d6-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="7c4d6-155">ПО промежуточного слоя перенаправления заголовков должно выполняться до другого ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="7c4d6-156">Такой порядок гарантирует, что ПО промежуточного слоя, полагающееся на сведения о перенаправленных заголовках, может использовать значения заголовков для обработки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="7c4d6-157">ПО промежуточного слоя перенаправления заголовков может выполняться после диагностики и обработки ошибок, однако его необходимо выполнить перед вызовом `UseHsts`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="7c4d6-158">Кроме того, можно вызвать `UseForwardedHeaders` перед диагностикой:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="7c4d6-159">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="7c4d6-160">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="7c4d6-161">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="7c4d6-161">Nginx configuration</span></span>

<span data-ttu-id="7c4d6-162">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="7c4d6-163">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="7c4d6-164">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="7c4d6-164">Apache configuration</span></span>

<span data-ttu-id="7c4d6-165">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="7c4d6-166">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="7c4d6-167">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="7c4d6-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="7c4d6-168">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="7c4d6-169">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-169">The following example changes the default values:</span></span>

* <span data-ttu-id="7c4d6-170">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="7c4d6-171">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="7c4d6-172">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="7c4d6-173">Параметр</span><span class="sxs-lookup"><span data-stu-id="7c4d6-173">Option</span></span> | <span data-ttu-id="7c4d6-174">Описание</span><span class="sxs-lookup"><span data-stu-id="7c4d6-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="7c4d6-175">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="7c4d6-176">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="7c4d6-177">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="7c4d6-178">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="7c4d6-179">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="7c4d6-180">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="7c4d6-181">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="7c4d6-182">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="7c4d6-183">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="7c4d6-184">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="7c4d6-185">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="7c4d6-186">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="7c4d6-187">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="7c4d6-188">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-189">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="7c4d6-190">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="7c4d6-191">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="7c4d6-192">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="7c4d6-193">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="7c4d6-194">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="7c4d6-195">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-196">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="7c4d6-197">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="7c4d6-198">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-199">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="7c4d6-200">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="7c4d6-201">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="7c4d6-202">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="7c4d6-203">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="7c4d6-204">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="7c4d6-205">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="7c4d6-206">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="7c4d6-207">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="7c4d6-208">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="7c4d6-209">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-210">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="7c4d6-211">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="7c4d6-212">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="7c4d6-213">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="7c4d6-214">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="7c4d6-215">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="7c4d6-216">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-217">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="7c4d6-218">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="7c4d6-219">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="7c4d6-220">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-221">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="7c4d6-222">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-223">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="7c4d6-224">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-225">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="7c4d6-226">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="7c4d6-227">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="7c4d6-228">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="7c4d6-229">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="7c4d6-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="7c4d6-230">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="7c4d6-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="7c4d6-231">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="7c4d6-232">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="7c4d6-233">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="7c4d6-234">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="7c4d6-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="7c4d6-235">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="7c4d6-236">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="7c4d6-237">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="7c4d6-238">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="7c4d6-239">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="7c4d6-240">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="7c4d6-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="7c4d6-241">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="7c4d6-242">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="7c4d6-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="7c4d6-243">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="7c4d6-244">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="7c4d6-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="7c4d6-245">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="7c4d6-246">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-247">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="7c4d6-248">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="7c4d6-249">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="7c4d6-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="7c4d6-250">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="7c4d6-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="7c4d6-251">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="7c4d6-251">Converted prefix length: 104</span></span>

<span data-ttu-id="7c4d6-252">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="7c4d6-253">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="7c4d6-254">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="7c4d6-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="7c4d6-255">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="7c4d6-256">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="7c4d6-257">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="7c4d6-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="7c4d6-259">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="7c4d6-260">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="7c4d6-261">Переадресация сертификатов</span><span class="sxs-lookup"><span data-stu-id="7c4d6-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="7c4d6-262">Azure</span><span class="sxs-lookup"><span data-stu-id="7c4d6-262">Azure</span></span>

<span data-ttu-id="7c4d6-263">Сведения о настройке Службы приложений Azure для переадресации сертификатов см. в статье [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth) (Настройка взаимной проверки подлинности TLS для Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="7c4d6-264">Приведенные ниже инструкции применимы к настройке приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="7c4d6-265">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="7c4d6-266">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка, который использует Azure.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="7c4d6-267">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="7c4d6-268">Другие веб-прокси</span><span class="sxs-lookup"><span data-stu-id="7c4d6-268">Other web proxies</span></span>

<span data-ttu-id="7c4d6-269">Если вы используете прокси-сервер не IIS или маршрутизацию запросов приложений (ARR) Службы приложений Azure, настройте прокси-сервер для переадресации сертификата, полученного в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="7c4d6-270">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="7c4d6-271">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="7c4d6-272">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="7c4d6-273">Если прокси-сервер не выполняет шифрование сертификата в кодировке base64 (как в случае с Nginx), задайте параметр `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="7c4d6-274">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="7c4d6-275">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="7c4d6-275">Troubleshoot</span></span>

<span data-ttu-id="7c4d6-276">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7c4d6-277">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="7c4d6-278">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="7c4d6-279">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="7c4d6-280">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="7c4d6-281">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="7c4d6-282">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="7c4d6-283">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="7c4d6-284">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="7c4d6-285">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="7c4d6-286">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="7c4d6-287">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="7c4d6-288">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="7c4d6-289">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="7c4d6-290">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="7c4d6-291">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="7c4d6-292">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7c4d6-293">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="7c4d6-294">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="7c4d6-295">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c4d6-296">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7c4d6-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="7c4d6-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-297">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c4d6-298">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="7c4d6-299">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="7c4d6-300">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="7c4d6-301">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="7c4d6-302">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="7c4d6-303">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="7c4d6-303">Forwarded headers</span></span>

<span data-ttu-id="7c4d6-304">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="7c4d6-305">Header</span><span class="sxs-lookup"><span data-stu-id="7c4d6-305">Header</span></span> | <span data-ttu-id="7c4d6-306">Описание</span><span class="sxs-lookup"><span data-stu-id="7c4d6-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="7c4d6-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="7c4d6-307">X-Forwarded-For</span></span> | <span data-ttu-id="7c4d6-308">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="7c4d6-309">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="7c4d6-310">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="7c4d6-311">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="7c4d6-312">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="7c4d6-313">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="7c4d6-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="7c4d6-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="7c4d6-315">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="7c4d6-316">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="7c4d6-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="7c4d6-317">X-Forwarded-Host</span></span> | <span data-ttu-id="7c4d6-318">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-318">The original value of the Host header field.</span></span> <span data-ttu-id="7c4d6-319">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="7c4d6-320">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="7c4d6-321">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="7c4d6-322">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-322">The middleware updates:</span></span>

* <span data-ttu-id="7c4d6-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="7c4d6-324">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="7c4d6-325">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="7c4d6-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="7c4d6-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="7c4d6-328">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="7c4d6-329">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-329">The default settings are:</span></span>

* <span data-ttu-id="7c4d6-330">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="7c4d6-331">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="7c4d6-332">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="7c4d6-333">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="7c4d6-334">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="7c4d6-335">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="7c4d6-336">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="7c4d6-337">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c4d6-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="7c4d6-338">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="7c4d6-339">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="7c4d6-340">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="7c4d6-341">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7c4d6-342">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="7c4d6-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7c4d6-343">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="7c4d6-344">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="7c4d6-345">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="7c4d6-346">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7c4d6-347">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="7c4d6-348">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="7c4d6-349">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="7c4d6-350">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="7c4d6-350">Nginx configuration</span></span>

<span data-ttu-id="7c4d6-351">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="7c4d6-352">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="7c4d6-353">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="7c4d6-353">Apache configuration</span></span>

<span data-ttu-id="7c4d6-354">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="7c4d6-355">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="7c4d6-356">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="7c4d6-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="7c4d6-357">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="7c4d6-358">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-358">The following example changes the default values:</span></span>

* <span data-ttu-id="7c4d6-359">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="7c4d6-360">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="7c4d6-361">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="7c4d6-362">Параметр</span><span class="sxs-lookup"><span data-stu-id="7c4d6-362">Option</span></span> | <span data-ttu-id="7c4d6-363">Описание</span><span class="sxs-lookup"><span data-stu-id="7c4d6-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="7c4d6-364">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="7c4d6-365">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="7c4d6-366">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="7c4d6-367">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="7c4d6-368">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="7c4d6-369">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="7c4d6-370">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="7c4d6-371">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="7c4d6-372">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="7c4d6-373">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="7c4d6-374">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="7c4d6-375">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="7c4d6-376">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="7c4d6-377">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-378">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="7c4d6-379">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="7c4d6-380">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="7c4d6-381">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="7c4d6-382">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="7c4d6-383">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="7c4d6-384">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-385">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="7c4d6-386">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="7c4d6-387">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="7c4d6-388">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="7c4d6-389">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="7c4d6-390">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="7c4d6-391">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="7c4d6-392">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="7c4d6-393">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="7c4d6-394">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="7c4d6-395">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="7c4d6-396">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="7c4d6-397">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="7c4d6-398">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-399">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="7c4d6-400">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="7c4d6-401">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="7c4d6-402">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="7c4d6-403">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="7c4d6-404">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="7c4d6-405">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-406">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="7c4d6-407">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="7c4d6-408">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="7c4d6-409">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-410">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="7c4d6-411">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-412">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="7c4d6-413">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="7c4d6-414">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="7c4d6-415">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="7c4d6-416">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="7c4d6-417">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="7c4d6-418">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="7c4d6-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="7c4d6-419">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="7c4d6-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="7c4d6-420">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="7c4d6-421">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="7c4d6-422">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="7c4d6-423">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="7c4d6-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="7c4d6-424">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="7c4d6-425">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="7c4d6-426">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="7c4d6-427">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="7c4d6-428">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="7c4d6-429">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="7c4d6-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="7c4d6-430">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="7c4d6-431">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="7c4d6-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="7c4d6-432">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="7c4d6-433">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="7c4d6-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="7c4d6-434">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="7c4d6-435">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="7c4d6-436">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="7c4d6-437">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="7c4d6-438">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="7c4d6-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="7c4d6-439">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="7c4d6-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="7c4d6-440">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="7c4d6-440">Converted prefix length: 104</span></span>

<span data-ttu-id="7c4d6-441">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="7c4d6-442">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="7c4d6-443">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="7c4d6-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="7c4d6-444">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="7c4d6-445">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="7c4d6-446">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="7c4d6-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="7c4d6-448">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="7c4d6-449">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="7c4d6-450">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="7c4d6-450">Troubleshoot</span></span>

<span data-ttu-id="7c4d6-451">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7c4d6-452">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="7c4d6-453">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="7c4d6-454">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="7c4d6-455">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="7c4d6-456">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="7c4d6-457">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="7c4d6-458">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="7c4d6-459">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="7c4d6-460">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="7c4d6-461">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="7c4d6-462">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="7c4d6-463">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="7c4d6-464">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="7c4d6-465">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="7c4d6-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="7c4d6-466">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="7c4d6-467">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7c4d6-468">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="7c4d6-469">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="7c4d6-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="7c4d6-470">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c4d6-471">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7c4d6-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="7c4d6-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="7c4d6-472">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
