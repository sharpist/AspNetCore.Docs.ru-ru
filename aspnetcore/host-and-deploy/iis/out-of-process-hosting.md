---
title: Внепроцессное размещение в службах IIS и ASP.NET Core
author: rick-anderson
description: Сведения о внепроцессном размещении с использованием служб IIS и модуля ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060421"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="bac4b-103">Внепроцессное размещение в службах IIS и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bac4b-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="bac4b-104">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, модуль ASP.NET Core обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="bac4b-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="bac4b-105">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое или завершении работы.</span><span class="sxs-lookup"><span data-stu-id="bac4b-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="bac4b-106">Это, по сути, совпадает с поведением приложений, выполняемых внутрипроцессно и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="bac4b-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="bac4b-107">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным вне процесса.</span><span class="sxs-lookup"><span data-stu-id="bac4b-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Модуль ASP.NET Core в сценарии внепроцессного размещения](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="bac4b-109">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="bac4b-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="bac4b-110">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="bac4b-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="bac4b-111">Обычно это порт 80 (HTTP) или порт 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="bac4b-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="bac4b-112">Модуль перенаправляет запросы Kestrel на случайный порт для приложения.</span><span class="sxs-lookup"><span data-stu-id="bac4b-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="bac4b-113">В качестве случайного порта не могут использоваться порты 80 и 443.</span><span class="sxs-lookup"><span data-stu-id="bac4b-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="bac4b-114">Модуль ASP.NET Core задает порт с помощью переменной среды при запуске.</span><span class="sxs-lookup"><span data-stu-id="bac4b-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="bac4b-115">Расширение <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> настраивает сервер для прослушивания `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="bac4b-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="bac4b-116">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="bac4b-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="bac4b-117">Модуль не поддерживает переадресацию по HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bac4b-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="bac4b-118">Запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bac4b-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="bac4b-119">После того как Kestrel получает запрос из модуля, запрос пересылается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bac4b-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="bac4b-120">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="bac4b-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="bac4b-121">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="bac4b-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="bac4b-122">Ответ приложения передается обратно в службу IIS, которая пересылает его HTTP-клиенту, инициировавшему запрос.</span><span class="sxs-lookup"><span data-stu-id="bac4b-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="bac4b-123">Инструкции по настройке модуля ASP.NET Core см. в статье <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="bac4b-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="bac4b-124">Дополнительные сведения о размещении см. в разделе [Размещение в ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="bac4b-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="bac4b-125">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="bac4b-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="bac4b-126">Включение компонентов IISIntegration</span><span class="sxs-lookup"><span data-stu-id="bac4b-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="bac4b-127">При создании узла в `CreateHostBuilder` (`Program.cs`) вызовите метод <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, чтобы включить интеграцию IIS:</span><span class="sxs-lookup"><span data-stu-id="bac4b-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="bac4b-128">Дополнительные сведения о методе `CreateDefaultBuilder` см. в разделе <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="bac4b-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="bac4b-129">**Модель размещения вне процесса**</span><span class="sxs-lookup"><span data-stu-id="bac4b-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="bac4b-130">Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="bac4b-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="bac4b-131">В следующем примере приложению запрещается заполнение `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="bac4b-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="bac4b-132">Параметр</span><span class="sxs-lookup"><span data-stu-id="bac4b-132">Option</span></span>                         | <span data-ttu-id="bac4b-133">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="bac4b-133">Default</span></span> | <span data-ttu-id="bac4b-134">Параметр</span><span class="sxs-lookup"><span data-stu-id="bac4b-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="bac4b-135">Если значение — `true`, [ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) задает свойство `HttpContext.User`, которое прошло [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="bac4b-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="bac4b-136">Если значение — `false`, ПО промежуточного слоя только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="bac4b-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="bac4b-137">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="bac4b-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="bac4b-138">Дополнительные сведения см. в статье о [проверке подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="bac4b-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="bac4b-139">Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="bac4b-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="bac4b-140">Если значение — `true` и если присутствует заголовок запроса `MS-ASPNETCORE-CLIENTCERT`, происходит заполнение `HttpContext.Connection.ClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="bac4b-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="bac4b-141">Сценарии использования прокси-сервера и подсистемы балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="bac4b-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="bac4b-142">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) и модуль ASP.NET Core настроены для пересылки:</span><span class="sxs-lookup"><span data-stu-id="bac4b-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="bac4b-143">схемы (HTTP/HTTPS);</span><span class="sxs-lookup"><span data-stu-id="bac4b-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="bac4b-144">удаленного IP-адреса, на котором возник запрос.</span><span class="sxs-lookup"><span data-stu-id="bac4b-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="bac4b-145">[ПО промежуточного слоя для интеграции IIS](#enable-the-iisintegration-components) настраивает ПО промежуточного слоя переадресации заголовков.</span><span class="sxs-lookup"><span data-stu-id="bac4b-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="bac4b-146">Для приложений, размещенных за дополнительными прокси-серверами и подсистемами балансировки нагрузки, может потребоваться дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="bac4b-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="bac4b-147">Дополнительные сведения см. в разделе [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="bac4b-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="bac4b-148">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="bac4b-148">Out-of-process hosting model</span></span>

<span data-ttu-id="bac4b-149">Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (`.csproj`):</span><span class="sxs-lookup"><span data-stu-id="bac4b-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="bac4b-150">Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="bac4b-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="bac4b-151">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="bac4b-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="bac4b-152">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="bac4b-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="bac4b-153">При размещении вне процесса [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> в следующих целях.</span><span class="sxs-lookup"><span data-stu-id="bac4b-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="bac4b-154">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bac4b-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="bac4b-155">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="bac4b-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="bac4b-156">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="bac4b-156">Process name</span></span>

<span data-ttu-id="bac4b-157">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="bac4b-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="bac4b-158">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="bac4b-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="bac4b-159">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="bac4b-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="bac4b-160">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="bac4b-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="bac4b-161">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="bac4b-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="bac4b-162">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="bac4b-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="bac4b-163">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="bac4b-163">Forward Windows authentication tokens.</span></span>
