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
ms.openlocfilehash: e329b8604b820818167a563b3a21f01f2ab2a6ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776387"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="6ce9d-103">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="6ce9d-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="6ce9d-104">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="6ce9d-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6ce9d-105">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="6ce9d-106">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="6ce9d-107">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="6ce9d-108">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="6ce9d-109">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="6ce9d-110">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="6ce9d-110">Forwarded headers</span></span>

<span data-ttu-id="6ce9d-111">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="6ce9d-112">Заголовок</span><span class="sxs-lookup"><span data-stu-id="6ce9d-112">Header</span></span> | <span data-ttu-id="6ce9d-113">Description</span><span class="sxs-lookup"><span data-stu-id="6ce9d-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="6ce9d-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="6ce9d-114">X-Forwarded-For</span></span> | <span data-ttu-id="6ce9d-115">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="6ce9d-116">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="6ce9d-117">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="6ce9d-118">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="6ce9d-119">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="6ce9d-120">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="6ce9d-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="6ce9d-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="6ce9d-122">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="6ce9d-123">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="6ce9d-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="6ce9d-124">X-Forwarded-Host</span></span> | <span data-ttu-id="6ce9d-125">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-125">The original value of the Host header field.</span></span> <span data-ttu-id="6ce9d-126">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="6ce9d-127">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="6ce9d-128">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="6ce9d-129">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-129">The middleware updates:</span></span>

* <span data-ttu-id="6ce9d-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; устанавливается на основе значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="6ce9d-131">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="6ce9d-132">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="6ce9d-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; устанавливается на основе значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="6ce9d-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; устанавливается на основе значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="6ce9d-135">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="6ce9d-136">Параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-136">The default settings are:</span></span>

* <span data-ttu-id="6ce9d-137">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="6ce9d-138">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="6ce9d-139">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="6ce9d-140">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="6ce9d-141">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="6ce9d-142">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="6ce9d-143">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="6ce9d-144">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ce9d-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="6ce9d-145">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="6ce9d-146">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="6ce9d-147">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="6ce9d-148">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ce9d-149">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="6ce9d-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ce9d-150">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="6ce9d-151">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="6ce9d-152">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="6ce9d-153">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ce9d-154">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="6ce9d-155">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="6ce9d-156">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="6ce9d-157">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="6ce9d-157">Nginx configuration</span></span>

<span data-ttu-id="6ce9d-158">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="6ce9d-159">Дополнительные сведения см. в разделе [NGINX. Использование перенаправленного заголовка](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="6ce9d-160">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="6ce9d-160">Apache configuration</span></span>

<span data-ttu-id="6ce9d-161">`X-Forwarded-For` добавляется автоматически (см. раздел [Apache Module mod_proxy: заголовки обратных прокси-запросов](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="6ce9d-162">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="6ce9d-163">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="6ce9d-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="6ce9d-164">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="6ce9d-165">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-165">The following example changes the default values:</span></span>

* <span data-ttu-id="6ce9d-166">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="6ce9d-167">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="6ce9d-168">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="6ce9d-169">Параметр</span><span class="sxs-lookup"><span data-stu-id="6ce9d-169">Option</span></span> | <span data-ttu-id="6ce9d-170">Description</span><span class="sxs-lookup"><span data-stu-id="6ce9d-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="6ce9d-171">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="6ce9d-172">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="6ce9d-173">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="6ce9d-174">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="6ce9d-175">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="6ce9d-176">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="6ce9d-177">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="6ce9d-178">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="6ce9d-179">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="6ce9d-180">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="6ce9d-181">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="6ce9d-182">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="6ce9d-183">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="6ce9d-184">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-185">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="6ce9d-186">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="6ce9d-187">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="6ce9d-188">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="6ce9d-189">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="6ce9d-190">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="6ce9d-191">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-192">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="6ce9d-193">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="6ce9d-194">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-195">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="6ce9d-196">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="6ce9d-197">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="6ce9d-198">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="6ce9d-199">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6ce9d-200">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="6ce9d-201">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="6ce9d-202">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="6ce9d-203">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="6ce9d-204">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6ce9d-205">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-206">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6ce9d-207">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6ce9d-208">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="6ce9d-209">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="6ce9d-210">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="6ce9d-211">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6ce9d-212">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-213">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6ce9d-214">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6ce9d-215">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="6ce9d-216">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-217">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="6ce9d-218">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-219">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="6ce9d-220">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-221">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="6ce9d-222">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="6ce9d-223">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="6ce9d-224">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="6ce9d-225">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="6ce9d-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="6ce9d-226">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="6ce9d-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="6ce9d-227">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="6ce9d-228">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="6ce9d-229">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="6ce9d-230">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="6ce9d-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="6ce9d-231">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="6ce9d-232">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="6ce9d-233">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="6ce9d-234">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="6ce9d-235">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="6ce9d-236">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="6ce9d-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="6ce9d-237">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="6ce9d-238">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="6ce9d-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="6ce9d-239">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="6ce9d-240">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="6ce9d-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="6ce9d-241">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="6ce9d-242">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-243">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="6ce9d-244">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="6ce9d-245">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="6ce9d-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="6ce9d-246">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="6ce9d-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="6ce9d-247">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="6ce9d-247">Converted prefix length: 104</span></span>

<span data-ttu-id="6ce9d-248">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="6ce9d-249">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="6ce9d-250">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="6ce9d-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="6ce9d-251">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="6ce9d-252">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="6ce9d-253">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="6ce9d-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="6ce9d-255">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="6ce9d-256">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="6ce9d-257">Переадресация сертификатов</span><span class="sxs-lookup"><span data-stu-id="6ce9d-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="6ce9d-258">Azure</span><span class="sxs-lookup"><span data-stu-id="6ce9d-258">Azure</span></span>

<span data-ttu-id="6ce9d-259">Сведения о настройке Службы приложений Azure для переадресации сертификатов см. в статье [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth) (Настройка взаимной проверки подлинности TLS для Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="6ce9d-260">Приведенные ниже инструкции применимы к настройке приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="6ce9d-261">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="6ce9d-262">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка, который использует Azure.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="6ce9d-263">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="6ce9d-264">Другие веб-прокси</span><span class="sxs-lookup"><span data-stu-id="6ce9d-264">Other web proxies</span></span>

<span data-ttu-id="6ce9d-265">Если вы используете прокси-сервер не IIS или маршрутизацию запросов приложений (ARR) Службы приложений Azure, настройте прокси-сервер для переадресации сертификата, полученного в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="6ce9d-266">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="6ce9d-267">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="6ce9d-268">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="6ce9d-269">Если прокси-сервер не выполняет шифрование сертификата в кодировке base64 (как в случае с Nginx), задайте параметр `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="6ce9d-270">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="6ce9d-271">Диагностика</span><span class="sxs-lookup"><span data-stu-id="6ce9d-271">Troubleshoot</span></span>

<span data-ttu-id="6ce9d-272">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6ce9d-273">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="6ce9d-274">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="6ce9d-275">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="6ce9d-276">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="6ce9d-277">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="6ce9d-278">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="6ce9d-279">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="6ce9d-280">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6ce9d-281">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="6ce9d-282">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6ce9d-283">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="6ce9d-284">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="6ce9d-285">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="6ce9d-286">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="6ce9d-287">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="6ce9d-288">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ce9d-289">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="6ce9d-290">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="6ce9d-291">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ce9d-292">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6ce9d-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="6ce9d-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-293">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6ce9d-294">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="6ce9d-295">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="6ce9d-296">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="6ce9d-297">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="6ce9d-298">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="6ce9d-299">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="6ce9d-299">Forwarded headers</span></span>

<span data-ttu-id="6ce9d-300">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="6ce9d-301">Заголовок</span><span class="sxs-lookup"><span data-stu-id="6ce9d-301">Header</span></span> | <span data-ttu-id="6ce9d-302">Description</span><span class="sxs-lookup"><span data-stu-id="6ce9d-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="6ce9d-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="6ce9d-303">X-Forwarded-For</span></span> | <span data-ttu-id="6ce9d-304">Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="6ce9d-305">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="6ce9d-306">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="6ce9d-307">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="6ce9d-308">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="6ce9d-309">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="6ce9d-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="6ce9d-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="6ce9d-311">Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="6ce9d-312">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="6ce9d-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="6ce9d-313">X-Forwarded-Host</span></span> | <span data-ttu-id="6ce9d-314">Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-314">The original value of the Host header field.</span></span> <span data-ttu-id="6ce9d-315">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="6ce9d-316">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="6ce9d-317">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="6ce9d-318">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-318">The middleware updates:</span></span>

* <span data-ttu-id="6ce9d-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; устанавливается на основе значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="6ce9d-320">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="6ce9d-321">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="6ce9d-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; устанавливается на основе значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="6ce9d-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; устанавливается на основе значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="6ce9d-324">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="6ce9d-325">Параметры по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-325">The default settings are:</span></span>

* <span data-ttu-id="6ce9d-326">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="6ce9d-327">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="6ce9d-328">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="6ce9d-329">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="6ce9d-330">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="6ce9d-331">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="6ce9d-332">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="6ce9d-333">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ce9d-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="6ce9d-334">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="6ce9d-335">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="6ce9d-336">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="6ce9d-337">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6ce9d-338">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="6ce9d-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6ce9d-339">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="6ce9d-340">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="6ce9d-341">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="6ce9d-342">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ce9d-343">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="6ce9d-344">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="6ce9d-345">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="6ce9d-346">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="6ce9d-346">Nginx configuration</span></span>

<span data-ttu-id="6ce9d-347">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="6ce9d-348">Дополнительные сведения см. в разделе [NGINX. Использование перенаправленного заголовка](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="6ce9d-349">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="6ce9d-349">Apache configuration</span></span>

<span data-ttu-id="6ce9d-350">`X-Forwarded-For` добавляется автоматически (см. раздел [Apache Module mod_proxy: заголовки обратных прокси-запросов](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="6ce9d-351">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="6ce9d-352">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="6ce9d-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="6ce9d-353">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="6ce9d-354">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-354">The following example changes the default values:</span></span>

* <span data-ttu-id="6ce9d-355">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="6ce9d-356">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="6ce9d-357">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="6ce9d-358">Параметр</span><span class="sxs-lookup"><span data-stu-id="6ce9d-358">Option</span></span> | <span data-ttu-id="6ce9d-359">Description</span><span class="sxs-lookup"><span data-stu-id="6ce9d-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="6ce9d-360">Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="6ce9d-361">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="6ce9d-362">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="6ce9d-363">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="6ce9d-364">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="6ce9d-365">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="6ce9d-366">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="6ce9d-367">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="6ce9d-368">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="6ce9d-369">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="6ce9d-370">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="6ce9d-371">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="6ce9d-372">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="6ce9d-373">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-374">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="6ce9d-375">Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="6ce9d-376">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="6ce9d-377">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="6ce9d-378">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="6ce9d-379">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="6ce9d-380">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-381">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="6ce9d-382">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="6ce9d-383">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="6ce9d-384">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="6ce9d-385">Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="6ce9d-386">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="6ce9d-387">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="6ce9d-388">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6ce9d-389">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="6ce9d-390">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="6ce9d-391">Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="6ce9d-392">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="6ce9d-393">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6ce9d-394">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-395">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6ce9d-396">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6ce9d-397">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="6ce9d-398">Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="6ce9d-399">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="6ce9d-400">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="6ce9d-401">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-402">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="6ce9d-403">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="6ce9d-404">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="6ce9d-405">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-406">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="6ce9d-407">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-408">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="6ce9d-409">Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="6ce9d-410">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="6ce9d-411">Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="6ce9d-412">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="6ce9d-413">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="6ce9d-414">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="6ce9d-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="6ce9d-415">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="6ce9d-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="6ce9d-416">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="6ce9d-417">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="6ce9d-418">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="6ce9d-419">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="6ce9d-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="6ce9d-420">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="6ce9d-421">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="6ce9d-422">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="6ce9d-423">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="6ce9d-424">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="6ce9d-425">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="6ce9d-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="6ce9d-426">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="6ce9d-427">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="6ce9d-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="6ce9d-428">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="6ce9d-429">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="6ce9d-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="6ce9d-430">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="6ce9d-431">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="6ce9d-432">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="6ce9d-433">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="6ce9d-434">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="6ce9d-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="6ce9d-435">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="6ce9d-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="6ce9d-436">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="6ce9d-436">Converted prefix length: 104</span></span>

<span data-ttu-id="6ce9d-437">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="6ce9d-438">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="6ce9d-439">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="6ce9d-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="6ce9d-440">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="6ce9d-441">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="6ce9d-442">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="6ce9d-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="6ce9d-444">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="6ce9d-445">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="6ce9d-446">Диагностика</span><span class="sxs-lookup"><span data-stu-id="6ce9d-446">Troubleshoot</span></span>

<span data-ttu-id="6ce9d-447">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="6ce9d-448">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="6ce9d-449">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="6ce9d-450">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="6ce9d-451">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="6ce9d-452">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="6ce9d-453">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="6ce9d-454">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="6ce9d-455">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6ce9d-456">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="6ce9d-457">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="6ce9d-458">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="6ce9d-459">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="6ce9d-460">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="6ce9d-461">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="6ce9d-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="6ce9d-462">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="6ce9d-463">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6ce9d-464">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="6ce9d-465">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="6ce9d-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="6ce9d-466">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ce9d-467">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6ce9d-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="6ce9d-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="6ce9d-468">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
