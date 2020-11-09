---
title: Внутрипроцессное размещение в службах IIS и ASP.NET Core
author: rick-anderson
description: Сведения о внутрипроцессном размещении с использованием служб IIS и модуля ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058484"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="9b97a-103">Внутрипроцессное размещение в службах IIS и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b97a-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="9b97a-104">При внутрипроцессном размещении приложение ASP.NET Core выполняется в том же процессе, что и рабочий процесс IIS.</span><span class="sxs-lookup"><span data-stu-id="9b97a-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="9b97a-105">При этом повышается производительность по сравнению с внепроцессным размещением, так как запросы не передаются через адаптер замыкания на себя (сетевой интерфейс, который возвращает исходящий сетевой трафик на тот же компьютер).</span><span class="sxs-lookup"><span data-stu-id="9b97a-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="9b97a-106">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением, размещенным внутри процесса.</span><span class="sxs-lookup"><span data-stu-id="9b97a-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Модуль ASP.NET Core в сценарии внутрипроцессного размещения](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="9b97a-108">Включение внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="9b97a-108">Enable in-process hosting</span></span>

<span data-ttu-id="9b97a-109">Начиная с ASP.NET Core 3.0, внутрипроцессное размещение включено по умолчанию для всех приложений, развертываемых в службах IIS.</span><span class="sxs-lookup"><span data-stu-id="9b97a-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="9b97a-110">Чтобы явным образом настроить внутрипроцессное размещение для приложения, задайте для свойства `<AspNetCoreHostingModel>` значение `InProcess` в файле проекта (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="9b97a-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="9b97a-111">Общая архитектура</span><span class="sxs-lookup"><span data-stu-id="9b97a-111">General architecture</span></span>

<span data-ttu-id="9b97a-112">В общем виде запрос выполняется и обрабатывается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="9b97a-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="9b97a-113">Запрос поступает из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="9b97a-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="9b97a-114">Драйвер направляет собственный запрос к IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9b97a-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="9b97a-115">Модуль ASP.NET Core получает собственный запрос и передает его на HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="9b97a-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="9b97a-116">HTTP-сервер IIS — это реализация внутрипроцессного сервера для IIS, в которой запрос преобразовывается из собственной формы в управляемую.</span><span class="sxs-lookup"><span data-stu-id="9b97a-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="9b97a-117">После того как HTTP-сервер IIS обработает запрос:</span><span class="sxs-lookup"><span data-stu-id="9b97a-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="9b97a-118">Запрос отправляется в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b97a-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="9b97a-119">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="9b97a-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="9b97a-120">Ответ приложения передается обратно в службы IIS через HTTP-сервер IIS.</span><span class="sxs-lookup"><span data-stu-id="9b97a-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="9b97a-121">IIS отправляет ответ клиенту, который инициировал запрос.</span><span class="sxs-lookup"><span data-stu-id="9b97a-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="9b97a-122">`CreateDefaultBuilder` добавляет экземпляр <xref:Microsoft.AspNetCore.Hosting.Server.IServer>. При этом вызывается метод <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> для загрузки [CoreCLR](/dotnet/standard/glossary#coreclr) и размещения приложения внутри рабочего процесса IIS (`w3wp.exe` или `iisexpress.exe`).</span><span class="sxs-lookup"><span data-stu-id="9b97a-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="9b97a-123">Тесты производительности показывают, что размещение приложения .NET Core внутри процесса позволяет обрабатывать значительно больше запросов, чем при размещении приложения вне процесса с перенаправлением запросов к [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="9b97a-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="9b97a-124">Приложения, опубликованные в виде одного исполняемого файла, не могут быть загружены по модели внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="9b97a-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="9b97a-125">Настройка приложения</span><span class="sxs-lookup"><span data-stu-id="9b97a-125">Application configuration</span></span>

<span data-ttu-id="9b97a-126">Чтобы настроить параметры IIS, включите конфигурацию служб для <xref:Microsoft.AspNetCore.Builder.IISServerOptions> в <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span><span class="sxs-lookup"><span data-stu-id="9b97a-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="9b97a-127">В следующем примере показано, как отключить AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="9b97a-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="9b97a-128">Параметр</span><span class="sxs-lookup"><span data-stu-id="9b97a-128">Option</span></span> | <span data-ttu-id="9b97a-129">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="9b97a-129">Default</span></span> | <span data-ttu-id="9b97a-130">Параметр</span><span class="sxs-lookup"><span data-stu-id="9b97a-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="9b97a-131">Если значение — `true`, сервер IIS задает свойство `HttpContext.User`, использующее [проверку подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="9b97a-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="9b97a-132">Если значение — `false`, сервер только предоставляет идентификатор для `HttpContext.User` и отвечает на явные запросы защиты от `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="9b97a-133">Для работы `AutomaticAuthentication` необходимо включить в службах IIS проверку подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="9b97a-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="9b97a-134">Дополнительные сведения: [Проверка подлинности Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="9b97a-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="9b97a-135">Задает отображаемое имя для пользователей на страницах входа.</span><span class="sxs-lookup"><span data-stu-id="9b97a-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="9b97a-136">Разрешены ли синхронные операции ввода-вывода для `HttpContext.Request` и `HttpContext.Response`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="9b97a-137">Возвращает или задает максимальный размер текста запроса для `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="9b97a-138">Обратите внимание, что сами службы IIS ограничены параметром `maxAllowedContentLength`, который обрабатывается перед тем, как `MaxRequestBodySize` задается в `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="9b97a-139">Изменение `MaxRequestBodySize` не влияет на `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="9b97a-140">Чтобы увеличить `maxAllowedContentLength`, добавьте запись в `web.config`, чтобы задать `maxAllowedContentLength` большее значение.</span><span class="sxs-lookup"><span data-stu-id="9b97a-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="9b97a-141">Дополнительные сведения см. в разделе [Конфигурация](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="9b97a-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="9b97a-142">Различия между внутрипроцессным и внепроцессным размещением</span><span class="sxs-lookup"><span data-stu-id="9b97a-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="9b97a-143">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="9b97a-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="9b97a-144">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="9b97a-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="9b97a-145">При внутрипроцессном размещении [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> в следующих целях.</span><span class="sxs-lookup"><span data-stu-id="9b97a-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="9b97a-146">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="9b97a-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="9b97a-147">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9b97a-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="9b97a-148">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="9b97a-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="9b97a-149">[Атрибут `requestTimeout`](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="9b97a-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="9b97a-150">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="9b97a-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="9b97a-151">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="9b97a-151">Use one app pool per app.</span></span>

* <span data-ttu-id="9b97a-152">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="9b97a-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="9b97a-153">Например, для приложений, опубликованных для 32-разрядной архитектуры (x86), необходимо включить поддержку этой архитектуры для пулов приложений IIS.</span><span class="sxs-lookup"><span data-stu-id="9b97a-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="9b97a-154">Дополнительные сведения см. в разделе [Создание сайта IIS](xref:tutorials/publish-to-iis#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="9b97a-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="9b97a-155">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="9b97a-155">Client disconnects are detected.</span></span> <span data-ttu-id="9b97a-156">Маркер отмены [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) аннулируется при отключении клиента.</span><span class="sxs-lookup"><span data-stu-id="9b97a-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="9b97a-157">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="9b97a-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="9b97a-158">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="9b97a-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="9b97a-159">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="9b97a-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* <span data-ttu-id="9b97a-160">[Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="9b97a-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
