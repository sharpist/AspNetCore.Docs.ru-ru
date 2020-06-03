---
<span data-ttu-id="431c8-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-102">'Blazor'</span></span>
- <span data-ttu-id="431c8-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-103">'Identity'</span></span>
- <span data-ttu-id="431c8-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-105">'Razor'</span></span>
- <span data-ttu-id="431c8-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="431c8-107">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="431c8-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="431c8-108">Автор: [Крис Росс](https://github.com/Tratcher) (Chris Ross)</span><span class="sxs-lookup"><span data-stu-id="431c8-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="431c8-109">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="431c8-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="431c8-110">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="431c8-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="431c8-111">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="431c8-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="431c8-112">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="431c8-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="431c8-113">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="431c8-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="431c8-114">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="431c8-114">Forwarded headers</span></span>

<span data-ttu-id="431c8-115">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="431c8-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="431c8-116">Header</span><span class="sxs-lookup"><span data-stu-id="431c8-116">Header</span></span> | <span data-ttu-id="431c8-117">Описание</span><span class="sxs-lookup"><span data-stu-id="431c8-117">Description</span></span> |
| ---
<span data-ttu-id="431c8-118">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-119">'Blazor'</span></span>
- <span data-ttu-id="431c8-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-120">'Identity'</span></span>
- <span data-ttu-id="431c8-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-122">'Razor'</span></span>
- <span data-ttu-id="431c8-123">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-123">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-124">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-125">'Blazor'</span></span>
- <span data-ttu-id="431c8-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-126">'Identity'</span></span>
- <span data-ttu-id="431c8-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-128">'Razor'</span></span>
- <span data-ttu-id="431c8-129">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-130">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-131">'Blazor'</span></span>
- <span data-ttu-id="431c8-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-132">'Identity'</span></span>
- <span data-ttu-id="431c8-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-134">'Razor'</span></span>
- <span data-ttu-id="431c8-135">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-136">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-137">'Blazor'</span></span>
- <span data-ttu-id="431c8-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-138">'Identity'</span></span>
- <span data-ttu-id="431c8-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-140">'Razor'</span></span>
- <span data-ttu-id="431c8-141">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-141">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-142">------ | | X-Forwarded-For | Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="431c8-143">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="431c8-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="431c8-144">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="431c8-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="431c8-145">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="431c8-146">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="431c8-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="431c8-147">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="431c8-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="431c8-148">| | X-Forwarded-Proto | Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="431c8-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="431c8-149">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="431c8-150">| | X-Forwarded-Host | Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="431c8-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="431c8-151">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="431c8-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="431c8-152">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="431c8-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="431c8-153">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="431c8-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="431c8-154">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="431c8-154">The middleware updates:</span></span>

* <span data-ttu-id="431c8-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="431c8-156">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="431c8-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="431c8-157">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="431c8-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="431c8-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="431c8-160">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="431c8-161">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="431c8-161">The default settings are:</span></span>

* <span data-ttu-id="431c8-162">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="431c8-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="431c8-163">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="431c8-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="431c8-164">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="431c8-165">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="431c8-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="431c8-166">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="431c8-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="431c8-167">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="431c8-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="431c8-168">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="431c8-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="431c8-169">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="431c8-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="431c8-170">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="431c8-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="431c8-171">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="431c8-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="431c8-172">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="431c8-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="431c8-173">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="431c8-174">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="431c8-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="431c8-175">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="431c8-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="431c8-176">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="431c8-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="431c8-177">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="431c8-178">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="431c8-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="431c8-179">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="431c8-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="431c8-180">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="431c8-181">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="431c8-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="431c8-182">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="431c8-182">Nginx configuration</span></span>

<span data-ttu-id="431c8-183">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="431c8-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="431c8-184">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="431c8-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="431c8-185">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="431c8-185">Apache configuration</span></span>

<span data-ttu-id="431c8-186">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="431c8-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="431c8-187">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="431c8-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="431c8-188">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="431c8-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="431c8-189">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="431c8-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="431c8-190">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="431c8-190">The following example changes the default values:</span></span>

* <span data-ttu-id="431c8-191">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="431c8-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="431c8-192">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="431c8-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="431c8-193">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="431c8-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="431c8-194">Параметр</span><span class="sxs-lookup"><span data-stu-id="431c8-194">Option</span></span> | <span data-ttu-id="431c8-195">Описание</span><span class="sxs-lookup"><span data-stu-id="431c8-195">Description</span></span> |
| ---
<span data-ttu-id="431c8-196">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-197">'Blazor'</span></span>
- <span data-ttu-id="431c8-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-198">'Identity'</span></span>
- <span data-ttu-id="431c8-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-200">'Razor'</span></span>
- <span data-ttu-id="431c8-201">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-201">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-202">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-203">'Blazor'</span></span>
- <span data-ttu-id="431c8-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-204">'Identity'</span></span>
- <span data-ttu-id="431c8-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-206">'Razor'</span></span>
- <span data-ttu-id="431c8-207">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-208">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-209">'Blazor'</span></span>
- <span data-ttu-id="431c8-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-210">'Identity'</span></span>
- <span data-ttu-id="431c8-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-212">'Razor'</span></span>
- <span data-ttu-id="431c8-213">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-214">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-215">'Blazor'</span></span>
- <span data-ttu-id="431c8-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-216">'Identity'</span></span>
- <span data-ttu-id="431c8-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-218">'Razor'</span></span>
- <span data-ttu-id="431c8-219">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-219">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="431c8-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="431c8-221">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="431c8-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="431c8-222">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="431c8-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="431c8-223">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="431c8-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="431c8-224">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="431c8-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="431c8-225">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="431c8-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="431c8-226">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="431c8-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="431c8-227">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="431c8-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="431c8-228">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="431c8-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="431c8-229">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="431c8-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="431c8-230">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="431c8-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="431c8-231">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="431c8-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="431c8-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="431c8-233">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-234">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="431c8-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="431c8-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="431c8-236">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="431c8-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="431c8-237">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="431c8-238">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="431c8-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="431c8-240">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-241">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="431c8-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="431c8-243">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-244">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="431c8-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="431c8-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="431c8-246">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="431c8-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="431c8-247">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="431c8-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="431c8-248">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="431c8-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="431c8-249">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="431c8-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="431c8-250">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="431c8-250">The default is `1`.</span></span> <span data-ttu-id="431c8-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="431c8-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="431c8-252">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="431c8-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="431c8-253">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="431c8-254">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-255">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="431c8-256">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="431c8-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="431c8-257">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="431c8-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="431c8-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="431c8-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="431c8-259">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="431c8-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="431c8-260">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="431c8-261">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-262">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="431c8-263">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="431c8-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="431c8-264">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="431c8-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="431c8-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="431c8-266">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="431c8-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="431c8-268">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="431c8-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="431c8-270">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="431c8-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="431c8-272">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="431c8-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="431c8-273">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="431c8-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="431c8-274">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="431c8-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="431c8-275">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="431c8-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="431c8-276">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="431c8-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="431c8-277">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="431c8-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="431c8-278">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="431c8-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="431c8-279">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="431c8-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="431c8-280">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="431c8-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="431c8-281">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="431c8-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="431c8-282">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="431c8-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="431c8-283">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="431c8-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="431c8-284">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="431c8-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="431c8-285">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="431c8-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="431c8-286">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="431c8-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="431c8-287">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="431c8-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="431c8-288">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="431c8-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="431c8-289">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="431c8-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="431c8-290">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="431c8-291">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-292">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="431c8-293">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="431c8-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="431c8-294">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="431c8-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="431c8-295">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="431c8-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="431c8-296">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="431c8-296">Converted prefix length: 104</span></span>

<span data-ttu-id="431c8-297">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="431c8-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="431c8-298">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="431c8-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="431c8-299">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="431c8-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="431c8-300">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="431c8-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="431c8-301">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="431c8-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="431c8-302">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="431c8-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="431c8-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="431c8-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="431c8-304">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="431c8-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="431c8-305">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="431c8-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="431c8-306">Переадресация сертификатов</span><span class="sxs-lookup"><span data-stu-id="431c8-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="431c8-307">Azure</span><span class="sxs-lookup"><span data-stu-id="431c8-307">Azure</span></span>

<span data-ttu-id="431c8-308">Сведения о настройке Службы приложений Azure для переадресации сертификатов см. в статье [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth) (Настройка взаимной проверки подлинности TLS для Службы приложений Azure).</span><span class="sxs-lookup"><span data-stu-id="431c8-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="431c8-309">Приведенные ниже инструкции применимы к настройке приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="431c8-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="431c8-310">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="431c8-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="431c8-311">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка, который использует Azure.</span><span class="sxs-lookup"><span data-stu-id="431c8-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="431c8-312">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="431c8-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="431c8-313">Другие веб-прокси</span><span class="sxs-lookup"><span data-stu-id="431c8-313">Other web proxies</span></span>

<span data-ttu-id="431c8-314">Если вы используете прокси-сервер не IIS или маршрутизацию запросов приложений (ARR) Службы приложений Azure, настройте прокси-сервер для переадресации сертификата, полученного в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="431c8-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="431c8-315">В `Startup.Configure` добавьте указанный ниже код перед вызовом `app.UseAuthentication();`:</span><span class="sxs-lookup"><span data-stu-id="431c8-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="431c8-316">Настройте ПО промежуточного слоя переадресации сертификатов, чтобы указать имя заголовка.</span><span class="sxs-lookup"><span data-stu-id="431c8-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="431c8-317">В `Startup.ConfigureServices` добавьте указанный ниже код, чтобы настроить заголовок, из которого ПО промежуточного слоя создает сертификат:</span><span class="sxs-lookup"><span data-stu-id="431c8-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="431c8-318">Если прокси-сервер не выполняет шифрование сертификата в кодировке base64 (как в случае с Nginx), задайте параметр `HeaderConverter`.</span><span class="sxs-lookup"><span data-stu-id="431c8-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="431c8-319">Рассмотрим следующий пример в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="431c8-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="431c8-320">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="431c8-320">Troubleshoot</span></span>

<span data-ttu-id="431c8-321">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="431c8-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="431c8-322">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="431c8-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="431c8-323">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="431c8-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="431c8-324">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="431c8-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="431c8-325">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="431c8-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="431c8-326">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="431c8-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="431c8-327">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="431c8-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="431c8-328">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="431c8-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="431c8-329">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="431c8-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="431c8-330">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="431c8-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="431c8-331">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="431c8-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="431c8-332">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="431c8-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="431c8-333">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="431c8-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="431c8-334">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="431c8-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="431c8-335">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="431c8-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="431c8-336">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="431c8-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="431c8-337">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="431c8-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="431c8-338">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="431c8-339">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="431c8-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="431c8-340">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="431c8-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="431c8-341">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="431c8-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="431c8-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="431c8-342">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="431c8-343">В рекомендуемой конфигурации для ASP.NET Core приложение размещается с использованием модуля ASP.NET Core для IIS, Nginx или Apache.</span><span class="sxs-lookup"><span data-stu-id="431c8-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="431c8-344">Прокси-серверы, подсистемы балансировки нагрузки и другие сетевые устройства часто скрывают сведения о запросах, еще не достигших целевого приложения.</span><span class="sxs-lookup"><span data-stu-id="431c8-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="431c8-345">Если прокси-сервер передает HTTPS-запросы через протокол HTTP, исходная схема (HTTPS) теряется и ее нужно дополнительно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="431c8-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="431c8-346">Так как приложение получает запрос от прокси-сервера, а не от правильного источника в Интернете или корпоративной сети, исходный IP-адрес клиента также нужно передать в заголовке.</span><span class="sxs-lookup"><span data-stu-id="431c8-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="431c8-347">Эти сведения могут быть важны при обработке запросов, например для перенаправления, аутентификации, создания ссылок, оценки политик и геолокации клиентов.</span><span class="sxs-lookup"><span data-stu-id="431c8-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="431c8-348">Перенаправленные заголовки</span><span class="sxs-lookup"><span data-stu-id="431c8-348">Forwarded headers</span></span>

<span data-ttu-id="431c8-349">По действующему соглашению прокси-серверы передают данные в заголовках HTTP.</span><span class="sxs-lookup"><span data-stu-id="431c8-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="431c8-350">Header</span><span class="sxs-lookup"><span data-stu-id="431c8-350">Header</span></span> | <span data-ttu-id="431c8-351">Описание</span><span class="sxs-lookup"><span data-stu-id="431c8-351">Description</span></span> |
| ---
<span data-ttu-id="431c8-352">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-353">'Blazor'</span></span>
- <span data-ttu-id="431c8-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-354">'Identity'</span></span>
- <span data-ttu-id="431c8-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-356">'Razor'</span></span>
- <span data-ttu-id="431c8-357">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-357">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-358">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-359">'Blazor'</span></span>
- <span data-ttu-id="431c8-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-360">'Identity'</span></span>
- <span data-ttu-id="431c8-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-362">'Razor'</span></span>
- <span data-ttu-id="431c8-363">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-364">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-365">'Blazor'</span></span>
- <span data-ttu-id="431c8-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-366">'Identity'</span></span>
- <span data-ttu-id="431c8-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-368">'Razor'</span></span>
- <span data-ttu-id="431c8-369">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-370">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-371">'Blazor'</span></span>
- <span data-ttu-id="431c8-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-372">'Identity'</span></span>
- <span data-ttu-id="431c8-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-374">'Razor'</span></span>
- <span data-ttu-id="431c8-375">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-375">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-376">------ | | X-Forwarded-For | Содержит сведения о клиенте, который создал этот запрос, и обо всех предыдущих узлах в цепочке прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="431c8-377">Этот параметр может содержать IP-адреса (и номера портов, если потребуется).</span><span class="sxs-lookup"><span data-stu-id="431c8-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="431c8-378">В цепочке прокси-серверов первый параметр обозначает клиента, отправившего исходный запрос.</span><span class="sxs-lookup"><span data-stu-id="431c8-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="431c8-379">За ним следуют идентификаторы всех последующих прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="431c8-380">В списке параметров отсутствует последний прокси-сервер в цепочке.</span><span class="sxs-lookup"><span data-stu-id="431c8-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="431c8-381">IP-адрес последнего прокси-сервера (и номер порта, если нужно) поступает в формате удаленного IP-адреса на транспортном уровне.</span><span class="sxs-lookup"><span data-stu-id="431c8-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="431c8-382">| | X-Forwarded-Proto | Значение исходной схемы передачи данных (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="431c8-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="431c8-383">Здесь может быть указан целый список схем, если запрос прошел через несколько прокси-серверов.</span><span class="sxs-lookup"><span data-stu-id="431c8-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="431c8-384">| | X-Forwarded-Host | Исходное значение поля Host в заголовке запроса.</span><span class="sxs-lookup"><span data-stu-id="431c8-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="431c8-385">Обычно прокси-серверы не изменяют заголовок Host.</span><span class="sxs-lookup"><span data-stu-id="431c8-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="431c8-386">В [рекомендациях Макрософт по безопасности CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) представлены сведения о связанной с повышением привилегий уязвимости, которая затрагивает системы, где прокси-сервер не проверяет заголовок Host и не ограничивает его значения известными допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="431c8-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="431c8-387">ПО промежуточного слоя перенаправления заголовков, входящее в пакет [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/), считывает эти заголовки и заполняет соответствующие поля <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="431c8-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="431c8-388">Это ПО промежуточного слоя обновляет следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="431c8-388">The middleware updates:</span></span>

* <span data-ttu-id="431c8-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) — задайте с помощью значения заголовка `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="431c8-390">Дополнительные параметры влияют на значение `RemoteIpAddress`, которое устанавливает ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="431c8-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="431c8-391">Дополнительные сведения см. в разделе [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="431c8-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) — задайте с помощью значения заголовка `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="431c8-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) — задайте с помощью значения заголовка `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="431c8-394">Для ПО промежуточного слоя перенаправления заголовков можно настроить [параметры по умолчанию](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="431c8-395">По умолчанию используются следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="431c8-395">The default settings are:</span></span>

* <span data-ttu-id="431c8-396">Существует только *один прокси* между приложением и источником запросов.</span><span class="sxs-lookup"><span data-stu-id="431c8-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="431c8-397">Для известных прокси-серверов и известных сетей настраиваются только петлевые адреса.</span><span class="sxs-lookup"><span data-stu-id="431c8-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="431c8-398">Перенаправленным заголовками заданы имена `X-Forwarded-For` и `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="431c8-399">Не все сетевые устройства добавляют заголовки `X-Forwarded-For` и `X-Forwarded-Proto` без дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="431c8-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="431c8-400">Если запросы, поступающие в приложение через прокси-сервер, не содержат эти заголовки, обратитесь к руководствам изготовителя устройства.</span><span class="sxs-lookup"><span data-stu-id="431c8-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="431c8-401">Если устройство использует имена заголовков, отличные от `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми устройством.</span><span class="sxs-lookup"><span data-stu-id="431c8-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="431c8-402">Дополнительные сведения см. в разделах [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options) и [Конфигурация для прокси-сервера, использующего другие имена заголовков](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="431c8-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="431c8-403">IIS и IIS Express с модулем ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="431c8-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="431c8-404">[ПО промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) по умолчанию включает ПО промежуточного слоя перенаправления заголовков при размещении приложения [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model) за IIS и модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="431c8-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="431c8-405">При использовании модуля ASP.NET Core ПО промежуточного слоя перенаправления заголовков настраивается так, чтобы оно запускалось первым в конвейере ПО промежуточного слоя и использовало специальную ограниченную конфигурацию, так как перенаправленные заголовки создают дополнительные проблемы с доверием (например, проблему [IP-спуфинга](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="431c8-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="431c8-406">В ПО промежуточного слоя настраивается пересылка заголовков `X-Forwarded-For` и `X-Forwarded-Proto`, и оно может работать только с одним локальным прокси-сервером.</span><span class="sxs-lookup"><span data-stu-id="431c8-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="431c8-407">Если вам нужны другие настройки, изучите раздел [Параметры ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="431c8-408">Другие сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="431c8-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="431c8-409">Если [интеграция IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) не используется при размещении [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model), ПО промежуточного слоя перенаправления заголовков не включается по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="431c8-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="431c8-410">ПО промежуточного слоя перенаправления заголовков нужно включить, чтобы приложение могло обрабатывать перенаправленные заголовки с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="431c8-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="431c8-411">После включения ПО промежуточного слоя, если не задан параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>, для свойства [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) по умолчанию устанавливается значение [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="431c8-412">В ПО промежуточного слоя с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> настройте перенаправление заголовков `X-Forwarded-For` и `X-Forwarded-Proto` в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="431c8-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="431c8-413">Вызовите метод <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`, прежде чем вызывать другое ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="431c8-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="431c8-414">Если класс <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> не указан в `Startup.ConfigureServices` или непосредственно в методе расширения с <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, по умолчанию будут перенаправляться заголовки [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="431c8-415">Свойство <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> должно быть настроено с заголовками для перенаправления.</span><span class="sxs-lookup"><span data-stu-id="431c8-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="431c8-416">Конфигурация Nginx</span><span class="sxs-lookup"><span data-stu-id="431c8-416">Nginx configuration</span></span>

<span data-ttu-id="431c8-417">Сведения о перенаправлении заголовков `X-Forwarded-For` и `X-Forwarded-Proto` см. в разделе <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="431c8-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="431c8-418">Дополнительные сведения см. в статье об [использовании заголовка Forwarded в NGINX](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="431c8-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="431c8-419">Конфигурация Apache</span><span class="sxs-lookup"><span data-stu-id="431c8-419">Apache configuration</span></span>

<span data-ttu-id="431c8-420">`X-Forwarded-For` добавляется автоматически (см. документацию по [заголовкам обратных прокси-запросов в модуле Apache mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="431c8-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="431c8-421">Сведения о перенаправлении `X-Forwarded-Proto` заголовка см. в разделе <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="431c8-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="431c8-422">Параметры ПО промежуточного слоя перенаправления заголовков</span><span class="sxs-lookup"><span data-stu-id="431c8-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="431c8-423">Параметр <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> позволяет управлять поведением ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="431c8-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="431c8-424">В следующем примере показано изменение значений по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="431c8-424">The following example changes the default values:</span></span>

* <span data-ttu-id="431c8-425">Ограничьте количество записей в перенаправленных заголовках до `2`.</span><span class="sxs-lookup"><span data-stu-id="431c8-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="431c8-426">Добавьте известный адрес прокси сервера `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="431c8-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="431c8-427">Измените имя перенаправленного заголовка с заданного по умолчанию `X-Forwarded-For` на `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="431c8-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="431c8-428">Параметр</span><span class="sxs-lookup"><span data-stu-id="431c8-428">Option</span></span> | <span data-ttu-id="431c8-429">Описание</span><span class="sxs-lookup"><span data-stu-id="431c8-429">Description</span></span> |
| ---
<span data-ttu-id="431c8-430">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-431">'Blazor'</span></span>
- <span data-ttu-id="431c8-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-432">'Identity'</span></span>
- <span data-ttu-id="431c8-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-434">'Razor'</span></span>
- <span data-ttu-id="431c8-435">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-435">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-436">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-437">'Blazor'</span></span>
- <span data-ttu-id="431c8-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-438">'Identity'</span></span>
- <span data-ttu-id="431c8-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-440">'Razor'</span></span>
- <span data-ttu-id="431c8-441">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-442">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-443">'Blazor'</span></span>
- <span data-ttu-id="431c8-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-444">'Identity'</span></span>
- <span data-ttu-id="431c8-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-446">'Razor'</span></span>
- <span data-ttu-id="431c8-447">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="431c8-448">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="431c8-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="431c8-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="431c8-449">'Blazor'</span></span>
- <span data-ttu-id="431c8-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="431c8-450">'Identity'</span></span>
- <span data-ttu-id="431c8-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="431c8-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="431c8-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="431c8-452">'Razor'</span></span>
- <span data-ttu-id="431c8-453">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="431c8-453">'SignalR' uid:</span></span> 

<span data-ttu-id="431c8-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ограничивает узлы в заголовке `X-Forwarded-Host` списком указанных значений.</span><span class="sxs-lookup"><span data-stu-id="431c8-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="431c8-455">Значения сравниваются по порядковым номерам без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="431c8-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="431c8-456">Номера портов указывать не нужно.</span><span class="sxs-lookup"><span data-stu-id="431c8-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="431c8-457">Если список пуст, разрешены все узлы.</span><span class="sxs-lookup"><span data-stu-id="431c8-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="431c8-458">Подстановочный знак верхнего уровня `*` разрешает все непустые значения узлов.</span><span class="sxs-lookup"><span data-stu-id="431c8-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="431c8-459">Подстановочные знаки поддоменов допускаются, но не могут указывать на корневой домен.</span><span class="sxs-lookup"><span data-stu-id="431c8-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="431c8-460">Например, `*.contoso.com` соответствует поддомену `foo.contoso.com`, но не корневому домену `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="431c8-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="431c8-461">Имена узлов в Юникоде разрешены, но для сопоставления они преобразуются в [Punycode](https://tools.ietf.org/html/rfc3492).</span><span class="sxs-lookup"><span data-stu-id="431c8-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="431c8-462">[IPv6-адреса](https://tools.ietf.org/html/rfc4291) должны включать ограничивающие квадратные скобки и иметь [стандартный формат](https://tools.ietf.org/html/rfc4291#section-2.2) (например, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="431c8-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="431c8-463">IPv6-адреса не приводятся к специальным правилам регистра для логического соответствия разных форматов, и к ним не применяется канонизация.</span><span class="sxs-lookup"><span data-stu-id="431c8-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="431c8-464">Если список разрешенных узлов не будет ограничен, злоумышленник сможет подделать ссылки, создаваемые службой.</span><span class="sxs-lookup"><span data-stu-id="431c8-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="431c8-465">Значение по умолчанию — пустой объект `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="431c8-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="431c8-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="431c8-467">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-For`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-468">Значение по умолчанию — `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="431c8-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Определяет, какие серверы пересылки будут обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="431c8-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="431c8-470">В разделе [Перечисление ForwardedHeaders](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) приведен список применимых полей.</span><span class="sxs-lookup"><span data-stu-id="431c8-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="431c8-471">Обычно для этого свойства задаются такие значения: `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="431c8-472">Значение по умолчанию — [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="431c8-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="431c8-474">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Host`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-475">Значение по умолчанию — `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="431c8-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="431c8-477">Этот параметр применяется в случае, если для перенаправления данных прокси-сервер или сервер пересылки не используют заголовок `X-Forwarded-Proto`, а используют другой заголовок.</span><span class="sxs-lookup"><span data-stu-id="431c8-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="431c8-478">Значение по умолчанию — `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="431c8-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Ограничивает количество записей в обрабатываемых заголовках.</span><span class="sxs-lookup"><span data-stu-id="431c8-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="431c8-480">Установите значение `null`, чтобы отключить это ограничение, но только в том случае, если настроено свойство `KnownProxies` или `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="431c8-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="431c8-481">Установленное значение, отличное от `null`, послужит мерой предосторожности (но не гарантией) для защиты от неправильной настройки прокси-серверов и вредоносных запросов, поступающих по сторонним каналам сети.</span><span class="sxs-lookup"><span data-stu-id="431c8-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="431c8-482">ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="431c8-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="431c8-483">Если используется значение по умолчанию (`1`), обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="431c8-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="431c8-484">Значение по умолчанию — `1`.</span><span class="sxs-lookup"><span data-stu-id="431c8-484">The default is `1`.</span></span> <span data-ttu-id="431c8-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Диапазоны адресов известных сетей, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="431c8-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="431c8-486">Диапазоны IP-адресов указываются в нотации CIDR.</span><span class="sxs-lookup"><span data-stu-id="431c8-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="431c8-487">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="431c8-488">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-489">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="431c8-490">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="431c8-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="431c8-491">Значение по умолчанию — `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> с одной записью для `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="431c8-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="431c8-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Адреса известных прокси-серверов, от которых можно принимать перенаправленные заголовки.</span><span class="sxs-lookup"><span data-stu-id="431c8-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="431c8-493">Используйте `KnownProxies`, чтобы указать точные IP-адреса.</span><span class="sxs-lookup"><span data-stu-id="431c8-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="431c8-494">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 выглядит так: `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="431c8-495">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-496">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="431c8-497">Дополнительные сведения см. в разделе [Конфигурация для IPv4-адреса, представленного в формате IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="431c8-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="431c8-498">Значение по умолчанию — `IList`\<<xref:System.Net.IPAddress>> с одной записью для `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="431c8-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="431c8-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="431c8-500">Значение по умолчанию — `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="431c8-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="431c8-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="431c8-502">Значение по умолчанию — `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="431c8-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="431c8-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Заголовок, заданный этим свойством, используется вместо заголовка, указанного в параметре [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="431c8-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="431c8-504">Значение по умолчанию — `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="431c8-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="431c8-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Требует синхронизации некоторых значений заголовков во всех обрабатываемых [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="431c8-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="431c8-506">Значение по умолчанию в ASP.NET Core 1.x — `true`.</span><span class="sxs-lookup"><span data-stu-id="431c8-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="431c8-507">Значение по умолчанию в ASP.NET Core 2.0 или более поздней версии — `false`.</span><span class="sxs-lookup"><span data-stu-id="431c8-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="431c8-508">Сценарии и варианты использования</span><span class="sxs-lookup"><span data-stu-id="431c8-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="431c8-509">Отсутствие возможности добавить перенаправленные заголовки, и все запросы считаются безопасными</span><span class="sxs-lookup"><span data-stu-id="431c8-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="431c8-510">В некоторых случаях нет возможности добавлять перенаправленные заголовки в запросы, передаваемые приложению через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="431c8-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="431c8-511">Если прокси-сервер требует схему HTTPS для всех внешних запросов, ее можно задать вручную в `Startup.Configure` перед использованием любого ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="431c8-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="431c8-512">В тестовой среде или среде разработки этот код можно отключить с помощью переменной среды или другого параметра конфигурации.</span><span class="sxs-lookup"><span data-stu-id="431c8-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="431c8-513">Базовый путь и прокси-серверы, которые изменяют путь запроса</span><span class="sxs-lookup"><span data-stu-id="431c8-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="431c8-514">Некоторые прокси-серверы передают путь, но добавляют к нему базовый путь приложения, который нужно удалять для правильной работы маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="431c8-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="431c8-515">ПО промежуточного слоя [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) разделяет путь на два сегмента: [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) и базовый путь приложения [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="431c8-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="431c8-516">Если `/foo` является базовым путем приложения в пути `/foo/api/1`, полученном от прокси-сервера, это ПО промежуточного слоя устанавливает для `Request.PathBase` значение `/foo`, а для `Request.Path` значение `/api/1` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="431c8-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="431c8-517">Исходный путь и базовый путь подставляются в прежнем виде, когда создается обратный запрос к ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="431c8-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="431c8-518">Дополнительные сведения о порядке обработки для ПО промежуточного слоя см. в статье <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="431c8-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="431c8-519">Если прокси-сервер усекает путь (например, перенаправляет `/foo/api/1` в `/api/1`), такие перенаправления и ссылки можно исправить, задав для запроса свойство [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase):</span><span class="sxs-lookup"><span data-stu-id="431c8-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="431c8-520">Если прокси-сервер добавляет к пути данные, лишнюю часть можно отсечь с помощью <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> и присвоить правильное значение свойству <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>:</span><span class="sxs-lookup"><span data-stu-id="431c8-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="431c8-521">Конфигурация для прокси-сервера, использующего другие имена заголовков</span><span class="sxs-lookup"><span data-stu-id="431c8-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="431c8-522">Если для перенаправления адреса или порта прокси-сервера и сведений об исходной схеме прокси-сервер не использует заголовки с именами `X-Forwarded-For` и `X-Forwarded-Proto`, задайте параметрам <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> и <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> значения, совпадающие с именами заголовков, используемыми прокси-сервером:</span><span class="sxs-lookup"><span data-stu-id="431c8-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="431c8-523">Конфигурация для IPv4-адреса, представленного в формате IPv6</span><span class="sxs-lookup"><span data-stu-id="431c8-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="431c8-524">Если сервер использует сокеты с двумя режимами, IPv4-адреса указываются в формате IPv6 (например, IPv4-адрес `10.0.0.1` в формате IPv6 будет выглядеть так: `::ffff:10.0.0.1`, или так: `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="431c8-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="431c8-525">См. статью о методе [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="431c8-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="431c8-526">Определите, нужно ли использовать этот формат, ознакомившись со статьей о свойстве [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="431c8-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="431c8-527">В следующем примере сетевой адрес, по которому предоставляются перенаправленные заголовки, добавляется в формате IPv6 в список `KnownNetworks`.</span><span class="sxs-lookup"><span data-stu-id="431c8-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="431c8-528">IPv4-адрес: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="431c8-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="431c8-529">Преобразованный в IPv6-адрес: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="431c8-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="431c8-530">Длина преобразованного префикса: 104</span><span class="sxs-lookup"><span data-stu-id="431c8-530">Converted prefix length: 104</span></span>

<span data-ttu-id="431c8-531">Можно также указать адрес в шестнадцатеричном формате (`10.11.12.1` в формате IPv6 выглядит как: `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="431c8-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="431c8-532">При преобразовании IPv4-адреса в IPv6-адрес добавьте 96 к длине префикса CIDR (`8` в примере) с учетом дополнительного префикса IPv6 `::ffff:` (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="431c8-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="431c8-533">Переадресация схемы для Linux и обратных прокси-серверов не IIS</span><span class="sxs-lookup"><span data-stu-id="431c8-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="431c8-534">Приложения, вызывающие <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> и <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>, помещают сайт в бесконечный цикл при развертывании в Службе приложений Azure Linux, виртуальной машине Linux в Azure или за любыми другими обратными прокси-серверами, помимо IIS.</span><span class="sxs-lookup"><span data-stu-id="431c8-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="431c8-535">TLS завершается обратным прокси-сервером, и Kestrel не знает о правильной схеме запроса.</span><span class="sxs-lookup"><span data-stu-id="431c8-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="431c8-536">OAuth и OIDC также не работают в этой конфигурации, поскольку создают неверные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="431c8-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="431c8-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> добавляет и настраивает ПО промежуточного слоя переадресованных заголовков при работе за IIS, но для Linux (интеграция с Apache или Nginx) нет аналогичной автоматической конфигурации.</span><span class="sxs-lookup"><span data-stu-id="431c8-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="431c8-538">Для переадресации схемы с прокси-сервера, когда используется не IIS, добавьте и настройте ПО промежуточного слоя перенаправленных заголовков.</span><span class="sxs-lookup"><span data-stu-id="431c8-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="431c8-539">В `Startup.ConfigureServices` используйте следующий код:</span><span class="sxs-lookup"><span data-stu-id="431c8-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="431c8-540">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="431c8-540">Troubleshoot</span></span>

<span data-ttu-id="431c8-541">Если заголовки перенаправляются не так, как ожидалось, включите [ведение журнала](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="431c8-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="431c8-542">Если журналы не содержат достаточно информации для устранения неполадок, просмотрите список заголовков в запросе, полученном сервером.</span><span class="sxs-lookup"><span data-stu-id="431c8-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="431c8-543">Используйте встроенное ПО промежуточного слоя для записи заголовков запроса в ответ приложения или для сохранения заголовков в журнал.</span><span class="sxs-lookup"><span data-stu-id="431c8-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="431c8-544">Чтобы записать заголовки в ответ приложения, используйте следующее встроенное ПО промежуточного слоя на терминале сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="431c8-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="431c8-545">Можно сделать запись в журналы, а не в текст ответа.</span><span class="sxs-lookup"><span data-stu-id="431c8-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="431c8-546">Запись в журналы позволяет сайту нормально работать во время отладки.</span><span class="sxs-lookup"><span data-stu-id="431c8-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="431c8-547">Для записи в журналы, а не в текст ответа, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="431c8-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="431c8-548">Внедрите `ILogger<Startup>` в класс `Startup` как описано в разделе [Создание журналов в классе Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="431c8-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="431c8-549">Поместите следующее встроенное ПО промежуточного слоя сразу после вызова <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="431c8-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="431c8-550">После обработки значения `X-Forwarded-{For|Proto|Host}` перемещаются в `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="431c8-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="431c8-551">Если в некотором заголовке содержится несколько значений, ПО промежуточного слоя перенаправления заголовков обрабатывает их в обратном порядке: справа налево.</span><span class="sxs-lookup"><span data-stu-id="431c8-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="431c8-552">По умолчанию `ForwardLimit` имеет значение `1` (один), а значит обрабатывается только крайнее правое значение из заголовков, если не увеличить значение `ForwardLimit`.</span><span class="sxs-lookup"><span data-stu-id="431c8-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="431c8-553">Удаленный IP-адрес из исходного запроса должен совпадать с записью в списке `KnownProxies` или `KnownNetworks`, чтобы происходила обработка заголовков переадресации.</span><span class="sxs-lookup"><span data-stu-id="431c8-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="431c8-554">Это позволяет избежать некоторых методов спуфинга, отклоняя запросы от ненадежных прокси-серверов пересылки.</span><span class="sxs-lookup"><span data-stu-id="431c8-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="431c8-555">В журнале указываются адреса неизвестных обнаруженных прокси-серверов:</span><span class="sxs-lookup"><span data-stu-id="431c8-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="431c8-556">В приведенном выше примере указан прокси-сервер с адресом 10.0.0.100.</span><span class="sxs-lookup"><span data-stu-id="431c8-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="431c8-557">Если сервер является доверенным прокси-сервером, добавьте его IP-адрес в `KnownProxies` (или добавьте доверенную сеть в `KnownNetworks`) в файле `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="431c8-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="431c8-558">Дополнительные сведения см. в разделе [о параметрах ПО промежуточного слоя перенаправления заголовков](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="431c8-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="431c8-559">Переадресацию заголовков следует разрешить только доверенным прокси-серверам и сетям.</span><span class="sxs-lookup"><span data-stu-id="431c8-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="431c8-560">В противном случае будут возможны атаки [подмены IP-адресов](https://www.iplocation.net/ip-spoofing).</span><span class="sxs-lookup"><span data-stu-id="431c8-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="431c8-561">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="431c8-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* <span data-ttu-id="431c8-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295) (Рекомендации Майкрософт по безопасности CVE-2018-0787: уязвимость, связанная с повышением привилегий в ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="431c8-562">[Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability](https://github.com/aspnet/Announcements/issues/295)</span></span>

::: moniker-end
