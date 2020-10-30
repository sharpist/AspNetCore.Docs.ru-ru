---
title: Принудительное применение HTTPS в ASP.NET Core
author: rick-anderson
description: Узнайте, как требовать использования HTTPS/TLS в ASP.NET Core веб-приложении.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: e473da9a7cbd91a601ad4af0c7c02c7f576f348c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051126"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="fe161-103">Принудительное применение HTTPS в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe161-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="fe161-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="fe161-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fe161-105">В этом документе показано, как:</span><span class="sxs-lookup"><span data-stu-id="fe161-105">This document shows how to:</span></span>

* <span data-ttu-id="fe161-106">Требовать протокол HTTPS для всех запросов.</span><span class="sxs-lookup"><span data-stu-id="fe161-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="fe161-107">Перенаправляет все запросы HTTP на HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="fe161-108">Ни один API не может предотвратить отправку конфиденциальных данных клиентом при первом запросе.</span><span class="sxs-lookup"><span data-stu-id="fe161-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="fe161-109">Проекты API</span><span class="sxs-lookup"><span data-stu-id="fe161-109">API projects</span></span>
>
> <span data-ttu-id="fe161-110">**Не** используйте [Рекуирехттпсаттрибуте](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) в веб-API, которые получают конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="fe161-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="fe161-111">`RequireHttpsAttribute` использует коды состояния HTTP для перенаправления браузеров из HTTP в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="fe161-112">Клиенты API могут не распознать или соблюдать перенаправления от HTTP к HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="fe161-113">Такие клиенты могут передавать данные по протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="fe161-114">Веб-API должны быть:</span><span class="sxs-lookup"><span data-stu-id="fe161-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="fe161-115">Не прослушивать по протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="fe161-116">Закройте подключение с кодом состояния 400 (недопустимый запрос) и не обслуживает запрос.</span><span class="sxs-lookup"><span data-stu-id="fe161-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="fe161-117">Проекты HSTS и API</span><span class="sxs-lookup"><span data-stu-id="fe161-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="fe161-118">Проекты API по умолчанию не включают [HSTS](#hsts) , так как HSTS обычно является инструкцией только браузера.</span><span class="sxs-lookup"><span data-stu-id="fe161-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="fe161-119">Другие вызывающие объекты, такие как приложения для телефонов или настольных систем, **не** подчиняются инструкции.</span><span class="sxs-lookup"><span data-stu-id="fe161-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="fe161-120">Даже в обозревателях, один вызов API через HTTP, прошедший проверку подлинности, имеет риски в незащищенных сетях.</span><span class="sxs-lookup"><span data-stu-id="fe161-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="fe161-121">Безопасный подход заключается в настройке проектов API для прослушивания и реагирования по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="fe161-122">Проекты API</span><span class="sxs-lookup"><span data-stu-id="fe161-122">API projects</span></span>
>
> <span data-ttu-id="fe161-123">**Не** используйте [Рекуирехттпсаттрибуте](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) в веб-API, которые получают конфиденциальную информацию.</span><span class="sxs-lookup"><span data-stu-id="fe161-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="fe161-124">`RequireHttpsAttribute` использует коды состояния HTTP для перенаправления браузеров из HTTP в HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="fe161-125">Клиенты API могут не распознать или соблюдать перенаправления от HTTP к HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="fe161-126">Такие клиенты могут передавать данные по протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="fe161-127">Веб-API должны быть:</span><span class="sxs-lookup"><span data-stu-id="fe161-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="fe161-128">Не прослушивать по протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="fe161-129">Закройте подключение с кодом состояния 400 (недопустимый запрос) и не обслуживает запрос.</span><span class="sxs-lookup"><span data-stu-id="fe161-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="fe161-130">Требование использовать HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe161-130">Require HTTPS</span></span>

<span data-ttu-id="fe161-131">В рабочей ASP.NET Core веб-приложений рекомендуется использовать:</span><span class="sxs-lookup"><span data-stu-id="fe161-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="fe161-132">По промежуточного слоя перенаправления HTTPS ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) для перенаправления HTTP-запросов к HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="fe161-133">HSTS по промежуточного слоя ([усехстс](#http-strict-transport-security-protocol-hsts)) для отправки клиентам заголовков протокола безопасности транспорта HTTP (HSTS).</span><span class="sxs-lookup"><span data-stu-id="fe161-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="fe161-134">Приложения, развернутые в конфигурации обратных прокси-серверов, позволяют прокси-серверу управлять безопасностью подключения (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fe161-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="fe161-135">Если прокси-сервер также обрабатывает перенаправление HTTPS, нет необходимости использовать по промежуточного слоя перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="fe161-136">Если прокси-сервер также обрабатывает запись заголовков HSTS (например, [поддержка собственного HSTS в IIS 10,0 (1709) или более поздней версии](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), по промежуточного слоя HSTS не требуется для приложения.</span><span class="sxs-lookup"><span data-stu-id="fe161-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="fe161-137">Дополнительные сведения см. [в разделе отказ от HTTPS/HSTS при создании проекта](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="fe161-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="fe161-138">усехттпсредиректион</span><span class="sxs-lookup"><span data-stu-id="fe161-138">UseHttpsRedirection</span></span>

<span data-ttu-id="fe161-139">Следующий код вызывает `UseHttpsRedirection` в `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="fe161-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="fe161-140">Предыдущий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="fe161-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="fe161-141">Использует значение по умолчанию [хттпсредиректионоптионс. редиректстатускоде](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="fe161-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="fe161-142">Использует значение по умолчанию [хттпсредиректионоптионс. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), если оно не переопределено `ASPNETCORE_HTTPS_PORT` переменной среды или [исервераддрессесфеатуре](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="fe161-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="fe161-143">Рекомендуется использовать временные перенаправления, а не постоянные перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fe161-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="fe161-144">Кэширование ссылок может привести к нестабильной работе в средах разработки.</span><span class="sxs-lookup"><span data-stu-id="fe161-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="fe161-145">Если вы предпочитаете отправить код состояния с постоянным перенаправлением, когда приложение находится в среде, не являющейся средой разработки, см. раздел [Настройка постоянных перенаправлений в производстве](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="fe161-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="fe161-146">Мы рекомендуем использовать [HSTS](#http-strict-transport-security-protocol-hsts) для передачи клиентам, что только защищенные запросы ресурсов должны отправляться в приложение (только в рабочей среде).</span><span class="sxs-lookup"><span data-stu-id="fe161-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="fe161-147">Конфигурация порта</span><span class="sxs-lookup"><span data-stu-id="fe161-147">Port configuration</span></span>

<span data-ttu-id="fe161-148">Порт должен быть доступен для промежуточного слоя, чтобы перенаправить незащищенный запрос на HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="fe161-149">Если порт недоступен:</span><span class="sxs-lookup"><span data-stu-id="fe161-149">If no port is available:</span></span>

* <span data-ttu-id="fe161-150">Перенаправление на HTTPS не выполняется.</span><span class="sxs-lookup"><span data-stu-id="fe161-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="fe161-151">По промежуточного слоя регистрируется предупреждение "не удалось определить HTTPS порт для перенаправления".</span><span class="sxs-lookup"><span data-stu-id="fe161-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="fe161-152">Укажите HTTPS порт, используя любой из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="fe161-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="fe161-153">Задайте [хттпсредиректионоптионс. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="fe161-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="fe161-154">Задайте `https_port` [параметр узла](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="fe161-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="fe161-155">В конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="fe161-155">In host configuration.</span></span>
  * <span data-ttu-id="fe161-156">Путем задания `ASPNETCORE_HTTPS_PORT` переменной среды.</span><span class="sxs-lookup"><span data-stu-id="fe161-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="fe161-157">Путем добавления записи верхнего уровня в *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="fe161-157">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="fe161-158">Укажите порт с защитой схемы, используя [переменную среды ASPNETCORE_URLS](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="fe161-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="fe161-159">Переменная среды настраивает сервер.</span><span class="sxs-lookup"><span data-stu-id="fe161-159">The environment variable configures the server.</span></span> <span data-ttu-id="fe161-160">По промежуточного слоя косвенно обнаруживает HTTPS порт с помощью <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="fe161-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="fe161-161">Этот подход не работает в обратных развертываниях прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="fe161-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="fe161-162">Задайте `https_port` [параметр узла](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="fe161-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="fe161-163">В конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="fe161-163">In host configuration.</span></span>
  * <span data-ttu-id="fe161-164">Путем задания `ASPNETCORE_HTTPS_PORT` переменной среды.</span><span class="sxs-lookup"><span data-stu-id="fe161-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="fe161-165">Путем добавления записи верхнего уровня в *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="fe161-165">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="fe161-166">Укажите порт с защитой схемы, используя [переменную среды ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="fe161-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="fe161-167">Переменная среды настраивает сервер.</span><span class="sxs-lookup"><span data-stu-id="fe161-167">The environment variable configures the server.</span></span> <span data-ttu-id="fe161-168">По промежуточного слоя косвенно обнаруживает HTTPS порт с помощью <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="fe161-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="fe161-169">Этот подход не работает в обратных развертываниях прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="fe161-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="fe161-170">В среде разработки задайте URL-адрес HTTPS в *launchsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="fe161-170">In development, set an HTTPS URL in *launchsettings.json* .</span></span> <span data-ttu-id="fe161-171">При использовании IIS Express включите протокол HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="fe161-172">Настройте конечную точку HTTPS URL для общедоступного пограничной развертывания сервера [Kestrel](xref:fundamentals/servers/kestrel) или сервера [HTTP.sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="fe161-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="fe161-173">Приложение использует только **один HTTPS-порт** .</span><span class="sxs-lookup"><span data-stu-id="fe161-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="fe161-174">По промежуточного слоя обнаруживает порт через <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="fe161-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="fe161-175">Если приложение запускается в конфигурации обратного прокси-сервера, оно <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> недоступно.</span><span class="sxs-lookup"><span data-stu-id="fe161-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="fe161-176">Задайте порт с помощью одного из других подходов, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="fe161-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="fe161-177">Развертывания Edge</span><span class="sxs-lookup"><span data-stu-id="fe161-177">Edge deployments</span></span> 

<span data-ttu-id="fe161-178">Если Kestrel или HTTP.sys используется в качестве общедоступного пограничной сервера, Kestrel или HTTP.sys должны быть настроены для прослушивания обоих типов:</span><span class="sxs-lookup"><span data-stu-id="fe161-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="fe161-179">Безопасный порт, на который перенаправляется клиент (как правило, 443 в рабочей среде и 5001 в разработке).</span><span class="sxs-lookup"><span data-stu-id="fe161-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="fe161-180">Небезопасный порт (как правило, 80 в рабочей среде и 5000 в разработке).</span><span class="sxs-lookup"><span data-stu-id="fe161-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="fe161-181">Клиент должен получить доступ к незащищенному порту, чтобы приложение получило незащищенный запрос и перенаправлять клиент на безопасный порт.</span><span class="sxs-lookup"><span data-stu-id="fe161-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="fe161-182">Дополнительные сведения см. в разделе [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) или <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="fe161-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="fe161-183">Сценарии развертывания</span><span class="sxs-lookup"><span data-stu-id="fe161-183">Deployment scenarios</span></span>

<span data-ttu-id="fe161-184">Все брандмауэры между клиентом и сервером также должны иметь открытые порты связи для трафика.</span><span class="sxs-lookup"><span data-stu-id="fe161-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="fe161-185">Если запросы перенаправляются в конфигурации обратных прокси-серверов, используйте [промежуточный слой перенаправленных заголовков](xref:host-and-deploy/proxy-load-balancer) перед вызовом по промежуточного слоя перенаправления HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="fe161-186">По промежуточного слоя перенаправленных заголовков обновляет `Request.Scheme` , используя `X-Forwarded-Proto` заголовок.</span><span class="sxs-lookup"><span data-stu-id="fe161-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="fe161-187">По промежуточного слоя позволяет правильно работать с URI перенаправления и другими политиками безопасности.</span><span class="sxs-lookup"><span data-stu-id="fe161-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="fe161-188">Если по промежуточного слоя перенаправленных заголовков не используется, серверное приложение может не получить правильную схему и завершить цикл перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fe161-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="fe161-189">Обычное сообщение об ошибке конечного пользователя состоит в том, что произошло слишком много перенаправлений.</span><span class="sxs-lookup"><span data-stu-id="fe161-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="fe161-190">При развертывании в службе приложений Azure следуйте указаниям в [руководстве по связыванию существующего настраиваемого SSL-сертификата с Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="fe161-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="fe161-191">Параметры</span><span class="sxs-lookup"><span data-stu-id="fe161-191">Options</span></span>

<span data-ttu-id="fe161-192">Следующий выделенный код вызывает [аддхттпсредиректион](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) для настройки параметров промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="fe161-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="fe161-193">Вызов `AddHttpsRedirection` необходим только для изменения значений `HttpsPort` или `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="fe161-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="fe161-194">Предыдущий выделенный код:</span><span class="sxs-lookup"><span data-stu-id="fe161-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="fe161-195">Устанавливает [хттпсредиректионоптионс. редиректстатускоде](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) в <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> значение, которое является значением по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fe161-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="fe161-196">Используйте поля <xref:Microsoft.AspNetCore.Http.StatusCodes> класса для назначений в `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="fe161-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="fe161-197">Устанавливает HTTPS порт 5001.</span><span class="sxs-lookup"><span data-stu-id="fe161-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="fe161-198">Настройка постоянных перенаправлений в рабочей среде</span><span class="sxs-lookup"><span data-stu-id="fe161-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="fe161-199">По промежуточного слоя по умолчанию отправляется [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) со всеми перенаправлениями.</span><span class="sxs-lookup"><span data-stu-id="fe161-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="fe161-200">Если вы предпочитаете отправить код состояния с постоянным перенаправлением, когда приложение находится в среде, не являющейся средой разработки, заключите конфигурацию параметров по промежуточного слоя в условную проверку для среды без разработки.</span><span class="sxs-lookup"><span data-stu-id="fe161-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe161-201">При настройке служб в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="fe161-201">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="fe161-202">При настройке служб в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="fe161-202">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="fe161-203">Альтернативный подход по промежуточного слоя перенаправления HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe161-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="fe161-204">Альтернативой по промежуточного слоя перенаправления HTTPS ( `UseHttpsRedirection` ) является использование по промежуточного слоя переписывания URL-адресов ( `AddRedirectToHttps` ).</span><span class="sxs-lookup"><span data-stu-id="fe161-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="fe161-205">`AddRedirectToHttps` также может задавать код состояния и порт при выполнении перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fe161-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="fe161-206">Дополнительные сведения см. в разделе по [промежуточного слоя перезаписи URL-адресов](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="fe161-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="fe161-207">При перенаправлении по протоколу HTTPS без требования дополнительных правил перенаправления рекомендуется использовать по промежуточного слоя перенаправления HTTPS ( `UseHttpsRedirection` ), описанные в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="fe161-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="fe161-208">Протокол HTTPS протокола безопасности транспорта HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="fe161-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="fe161-209">В [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [обеспечение безопасности транспорта по протоколу HTTP (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) является расширением безопасности, которое определяется веб-приложением с помощью заголовка ответа.</span><span class="sxs-lookup"><span data-stu-id="fe161-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="fe161-210">Когда [браузер, поддерживающий HSTS,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) получает этот заголовок:</span><span class="sxs-lookup"><span data-stu-id="fe161-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="fe161-211">В браузере хранится конфигурация домена, которая предотвращает отправку обмена данными по протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="fe161-212">Браузер принудительно выполняет все связи по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="fe161-213">Браузер не разрешает пользователю использовать недоверенные или недопустимые сертификаты.</span><span class="sxs-lookup"><span data-stu-id="fe161-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="fe161-214">Браузер отключает запросы, позволяющие пользователю временно доверять такому сертификату.</span><span class="sxs-lookup"><span data-stu-id="fe161-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="fe161-215">Так как HSTS применяется клиентом, у него есть некоторые ограничения.</span><span class="sxs-lookup"><span data-stu-id="fe161-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="fe161-216">Клиент должен поддерживать HSTS.</span><span class="sxs-lookup"><span data-stu-id="fe161-216">The client must support HSTS.</span></span>
* <span data-ttu-id="fe161-217">HSTS требует по крайней мере одного успешного запроса HTTPS для установки политики HSTS.</span><span class="sxs-lookup"><span data-stu-id="fe161-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="fe161-218">Приложение должно проверять каждый HTTP-запрос и перенаправлять или отклонять HTTP-запрос.</span><span class="sxs-lookup"><span data-stu-id="fe161-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="fe161-219">ASP.NET Core 2,1 и более поздних версий реализует HSTS с помощью `UseHsts` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="fe161-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="fe161-220">Следующий код вызывает, `UseHsts` когда приложение не находится в [режиме разработки](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="fe161-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="fe161-221">`UseHsts` не рекомендуется в разработке, так как параметры HSTS очень кэшируются браузерами.</span><span class="sxs-lookup"><span data-stu-id="fe161-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="fe161-222">По умолчанию `UseHsts` исключает локальный петлевой адрес.</span><span class="sxs-lookup"><span data-stu-id="fe161-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="fe161-223">Для рабочих сред, которые реализуют протокол HTTPS в первый раз, задайте для свойства Initial [хстсоптионс. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) небольшое значение с помощью одного из <xref:System.TimeSpan> методов.</span><span class="sxs-lookup"><span data-stu-id="fe161-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="fe161-224">Задайте в качестве значения в часах не более одного дня на случай, если необходимо вернуть инфраструктуру HTTPS к протоколу HTTP.</span><span class="sxs-lookup"><span data-stu-id="fe161-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="fe161-225">Убедившись в устойчивости конфигурации HTTPS, увеличьте значение HSTS. `max-age` часто используемое значение — один год.</span><span class="sxs-lookup"><span data-stu-id="fe161-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="fe161-226">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="fe161-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="fe161-227">Задает параметр предварительной загрузки `Strict-Transport-Security` заголовка.</span><span class="sxs-lookup"><span data-stu-id="fe161-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="fe161-228">Предварительная загрузка не является частью [спецификации RFC HSTS](https://tools.ietf.org/html/rfc6797), но поддерживается веб-браузерами для предварительной загрузки HSTS сайтов при новой установке.</span><span class="sxs-lookup"><span data-stu-id="fe161-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="fe161-229">Дополнительные сведения см. в статье [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="fe161-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="fe161-230">Включает [инклудесубдомаин](https://tools.ietf.org/html/rfc6797#section-6.1.2), который применяет политику HSTS для размещения поддоменов.</span><span class="sxs-lookup"><span data-stu-id="fe161-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="fe161-231">Явно задает `max-age` `Strict-Transport-Security` для параметра заголовка значение 60 дней.</span><span class="sxs-lookup"><span data-stu-id="fe161-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="fe161-232">Если значение не задано, по умолчанию используется значение 30 дней.</span><span class="sxs-lookup"><span data-stu-id="fe161-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="fe161-233">Дополнительные сведения см. в разделе [директива max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="fe161-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="fe161-234">Добавляет `example.com` в список узлов для исключения.</span><span class="sxs-lookup"><span data-stu-id="fe161-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="fe161-235">`UseHsts` исключает следующие узлы замыкания на себя:</span><span class="sxs-lookup"><span data-stu-id="fe161-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="fe161-236">`localhost` : Адрес замыкания на себя IPv4.</span><span class="sxs-lookup"><span data-stu-id="fe161-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="fe161-237">`127.0.0.1` : Адрес замыкания на себя IPv4.</span><span class="sxs-lookup"><span data-stu-id="fe161-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="fe161-238">`[::1]` : IPv6-адрес замыкания на себя.</span><span class="sxs-lookup"><span data-stu-id="fe161-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="fe161-239">Отказ от HTTPS/HSTS при создании проекта</span><span class="sxs-lookup"><span data-stu-id="fe161-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="fe161-240">В некоторых сценариях серверной службы, где безопасность подключения обрабатывается на общедоступной границе сети, Настройка безопасности подключения на каждом узле не требуется.</span><span class="sxs-lookup"><span data-stu-id="fe161-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="fe161-241">Веб-приложения, созданные на основе шаблонов в Visual Studio или команды [DotNet New](/dotnet/core/tools/dotnet-new) , включают [Перенаправление HTTPS](#require-https) и [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="fe161-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="fe161-242">Для развертываний, которые не нуждаются в этих сценариях, можно отказаться от HTTPS/HSTS при создании приложения из шаблона.</span><span class="sxs-lookup"><span data-stu-id="fe161-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="fe161-243">Чтобы отказаться от HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="fe161-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe161-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe161-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="fe161-245">Снимите флажок **настроить для HTTPS** .</span><span class="sxs-lookup"><span data-stu-id="fe161-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Диалоговое окно нового ASP.NET Core веб-приложения с снятым флажком настроить для HTTPS.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Диалоговое окно нового ASP.NET Core веб-приложения с снятым флажком настроить для HTTPS.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="fe161-248">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="fe161-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="fe161-249">Использовать параметр `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="fe161-249">Use the `--no-https` option.</span></span> <span data-ttu-id="fe161-250">Например.</span><span class="sxs-lookup"><span data-stu-id="fe161-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="fe161-251">Доверять сертификату разработки ASP.NET Core HTTPS в Windows и macOS</span><span class="sxs-lookup"><span data-stu-id="fe161-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="fe161-252">Пакет SDK для .NET Core включает сертификат разработки HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fe161-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="fe161-253">Сертификат устанавливается в рамках первого запуска.</span><span class="sxs-lookup"><span data-stu-id="fe161-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="fe161-254">Например, `dotnet --info` создает разновидность следующих выходных данных:</span><span class="sxs-lookup"><span data-stu-id="fe161-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="fe161-255">При установке пакета SDK для .NET Core в локальное хранилище сертификатов пользователя устанавливается сертификат разработки HTTPS ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fe161-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="fe161-256">Сертификат установлен, но не является доверенным.</span><span class="sxs-lookup"><span data-stu-id="fe161-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="fe161-257">Чтобы доверять сертификату, выполните однократный шаг, чтобы запустить `dev-certs` средство DotNet:</span><span class="sxs-lookup"><span data-stu-id="fe161-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="fe161-258">Следующая команда вызывает справку по средству `dev-certs`.</span><span class="sxs-lookup"><span data-stu-id="fe161-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="fe161-259">Как настроить сертификат разработчика для DOCKER</span><span class="sxs-lookup"><span data-stu-id="fe161-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="fe161-260">Также см. [эту проблему в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="fe161-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="fe161-261">Доверенный HTTPS сертификат в Linux</span><span class="sxs-lookup"><span data-stu-id="fe161-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="fe161-262">Инструкции по Linux см. в документации по дистрибутиву.</span><span class="sxs-lookup"><span data-stu-id="fe161-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="fe161-263">Доверенный HTTPS сертификат из подсистемы Windows для Linux</span><span class="sxs-lookup"><span data-stu-id="fe161-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="fe161-264">Подсистема Windows для Linux (WSL) создает самозаверяющий сертификат HTTPS. Чтобы настроить хранилище сертификатов Windows для доверия к сертификату WSL, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="fe161-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="fe161-265">Выполните следующую команду, чтобы экспортировать созданный WSL сертификат:</span><span class="sxs-lookup"><span data-stu-id="fe161-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="fe161-266">В окне WSL выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="fe161-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="fe161-267">Приведенная выше команда задает переменные среды, чтобы в Linux использовался доверенный сертификат Windows.</span><span class="sxs-lookup"><span data-stu-id="fe161-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="fe161-268">Устранение неполадок с сертификатами</span><span class="sxs-lookup"><span data-stu-id="fe161-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="fe161-269">В этом разделе содержатся сведения о том, когда сертификат разработки ASP.NET Core HTTPS [установлен и является доверенным](#trust), но по-прежнему имеются предупреждения браузера о том, что сертификат не является доверенным.</span><span class="sxs-lookup"><span data-stu-id="fe161-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="fe161-270">Сертификат разработки ASP.NET Core HTTPS используется [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="fe161-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="fe161-271">Все платформы — сертификат не является доверенным</span><span class="sxs-lookup"><span data-stu-id="fe161-271">All platforms - certificate not trusted</span></span>

<span data-ttu-id="fe161-272">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fe161-272">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="fe161-273">Закройте все открытые экземпляры браузера.</span><span class="sxs-lookup"><span data-stu-id="fe161-273">Close any browser instances open.</span></span> <span data-ttu-id="fe161-274">Откройте новое окно браузера для приложения.</span><span class="sxs-lookup"><span data-stu-id="fe161-274">Open a new browser window to app.</span></span> <span data-ttu-id="fe161-275">Доверие сертификатов кэшируется браузерами.</span><span class="sxs-lookup"><span data-stu-id="fe161-275">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="fe161-276">Предыдущие команды решают большинство проблем с доверием к браузеру.</span><span class="sxs-lookup"><span data-stu-id="fe161-276">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="fe161-277">Если браузер по-прежнему не доверяет сертификату, следуйте приведенным ниже рекомендациям для конкретной платформы.</span><span class="sxs-lookup"><span data-stu-id="fe161-277">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="fe161-278">DOCKER — сертификат не является доверенным</span><span class="sxs-lookup"><span data-stu-id="fe161-278">Docker - certificate not trusted</span></span>

* <span data-ttu-id="fe161-279">Удалите папку *\{ \Аппдата\роаминг\асп.нет\хттпс C:\Users User}* .</span><span class="sxs-lookup"><span data-stu-id="fe161-279">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="fe161-280">Очистите решение.</span><span class="sxs-lookup"><span data-stu-id="fe161-280">Clean the solution.</span></span> <span data-ttu-id="fe161-281">Удалите папки *bin* и *obj* .</span><span class="sxs-lookup"><span data-stu-id="fe161-281">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="fe161-282">Перезапустите средство разработки.</span><span class="sxs-lookup"><span data-stu-id="fe161-282">Restart the development tool.</span></span> <span data-ttu-id="fe161-283">Например, Visual Studio, Visual Studio Code или Visual Studio для Mac.</span><span class="sxs-lookup"><span data-stu-id="fe161-283">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="fe161-284">Windows — сертификат не является доверенным</span><span class="sxs-lookup"><span data-stu-id="fe161-284">Windows - certificate not trusted</span></span>

* <span data-ttu-id="fe161-285">Проверьте сертификаты в хранилище сертификатов.</span><span class="sxs-lookup"><span data-stu-id="fe161-285">Check the certificates in the certificate store.</span></span> <span data-ttu-id="fe161-286">Должен быть `localhost` сертификат с `ASP.NET Core HTTPS development certificate` понятным именем в `Current User > Personal > Certificates` и `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="fe161-286">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="fe161-287">Удалите все найденные сертификаты из личных и доверенных корневых центров сертификации.</span><span class="sxs-lookup"><span data-stu-id="fe161-287">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="fe161-288">**Не** удаляйте сертификат IIS Express localhost.</span><span class="sxs-lookup"><span data-stu-id="fe161-288">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="fe161-289">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fe161-289">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="fe161-290">Закройте все открытые экземпляры браузера.</span><span class="sxs-lookup"><span data-stu-id="fe161-290">Close any browser instances open.</span></span> <span data-ttu-id="fe161-291">Откройте новое окно браузера для приложения.</span><span class="sxs-lookup"><span data-stu-id="fe161-291">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="fe161-292">OS X — сертификат не является доверенным</span><span class="sxs-lookup"><span data-stu-id="fe161-292">OS X - certificate not trusted</span></span>

* <span data-ttu-id="fe161-293">Откройте доступ к цепочке ключей.</span><span class="sxs-lookup"><span data-stu-id="fe161-293">Open KeyChain Access.</span></span>
* <span data-ttu-id="fe161-294">Выберите цепочку ключей системы.</span><span class="sxs-lookup"><span data-stu-id="fe161-294">Select the System keychain.</span></span>
* <span data-ttu-id="fe161-295">Проверьте наличие сертификата localhost.</span><span class="sxs-lookup"><span data-stu-id="fe161-295">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="fe161-296">Убедитесь, что он содержит `+` символ на значке, чтобы указать, что он является доверенным для всех пользователей.</span><span class="sxs-lookup"><span data-stu-id="fe161-296">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="fe161-297">Удалите сертификат из цепочки ключей системы.</span><span class="sxs-lookup"><span data-stu-id="fe161-297">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="fe161-298">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fe161-298">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="fe161-299">Закройте все открытые экземпляры браузера.</span><span class="sxs-lookup"><span data-stu-id="fe161-299">Close any browser instances open.</span></span> <span data-ttu-id="fe161-300">Откройте новое окно браузера для приложения.</span><span class="sxs-lookup"><span data-stu-id="fe161-300">Open a new browser window to app.</span></span>

<span data-ttu-id="fe161-301">Устранение неполадок с сертификатами в Visual Studio см. [в статье об ошибке HTTPS с использованием IIS Express (DotNet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) .</span><span class="sxs-lookup"><span data-stu-id="fe161-301">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="fe161-302">IIS Express SSL-сертификат, используемый в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe161-302">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="fe161-303">Чтобы устранить проблемы с сертификатом IIS Express, выберите **восстановить** в Visual Studio Installer.</span><span class="sxs-lookup"><span data-stu-id="fe161-303">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="fe161-304">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="fe161-304">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="fe161-305">Дополнительные сведения</span><span class="sxs-lookup"><span data-stu-id="fe161-305">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="fe161-306">Размещение ASP.NET Core в Linux с помощью Apache: Конфигурация HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe161-306">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="fe161-307">Размещение ASP.NET Core в Linux с помощью nginx: Конфигурация HTTPS</span><span class="sxs-lookup"><span data-stu-id="fe161-307">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="fe161-308">Настройка SSL в службах IIS</span><span class="sxs-lookup"><span data-stu-id="fe161-308">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="fe161-309">Поддержка браузера OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="fe161-309">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)