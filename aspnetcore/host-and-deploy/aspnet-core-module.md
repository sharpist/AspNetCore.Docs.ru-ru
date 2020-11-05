---
title: Модуль ASP.NET Core
author: rick-anderson
description: Сведения о модуле ASP.NET Core для размещения приложений ASP.NET Core в службах IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: a644214c208ece38bc118c31cf3c9265706706ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061500"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="4b6de-103">Модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b6de-103">ASP.NET Core Module</span></span>

<span data-ttu-id="4b6de-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Рик Штраль](https://github.com/RickStrahl) (Rick Strahl), [Крис Росс](https://github.com/Tratcher) (Chris Ross), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Сураб Ширхатти](https://twitter.com/sshirhatti) (Sourabh Shirhatti) и [Джастин Коталик](https://github.com/jkotalik) (Justin Kotalik)</span><span class="sxs-lookup"><span data-stu-id="4b6de-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4b6de-105">Модуль ASP.NET Core — это собственный модуль IIS, который подключается к конвейеру IIS, чтобы приложения ASP.NET Core могли работать со службами IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="4b6de-106">Приложения ASP.NET Core могут выполняться со службами IIS одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="4b6de-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="4b6de-107">размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`); это так называемая [модель внутрипроцессного размещения](xref:host-and-deploy/iis/in-process-hosting);</span><span class="sxs-lookup"><span data-stu-id="4b6de-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="4b6de-108">переадресация веб-запросов к серверной части приложения ASP.NET Core на сервере Kestrel; это [модель размещения вне процесса](xref:host-and-deploy/iis/out-of-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="4b6de-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="4b6de-109">Каждая из моделей размещения имеет свои преимущества и недостатки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="4b6de-110">По умолчанию используется модель внутрипроцессного размещения из-за лучшей производительности и диагностики.</span><span class="sxs-lookup"><span data-stu-id="4b6de-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="4b6de-111">Установка модуля ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b6de-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="4b6de-112">Скачайте установщик по следующей ссылке:</span><span class="sxs-lookup"><span data-stu-id="4b6de-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="4b6de-113">Текущий установщик пакета размещения .NET Core (прямая загрузка)</span><span class="sxs-lookup"><span data-stu-id="4b6de-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="4b6de-114">Подробные инструкции по установке модуля ASP.NET Core или установке других версий модуля см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4b6de-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="4b6de-115">Руководство по публикации приложения ASP.NET Core на сервере IIS см. по этой ссылке: <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4b6de-116">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="4b6de-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="4b6de-117">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4b6de-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="4b6de-118">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4b6de-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="4b6de-119">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="4b6de-119">Supported Windows versions:</span></span>

* <span data-ttu-id="4b6de-120">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-120">Windows 7 or later</span></span>
* <span data-ttu-id="4b6de-121">Windows Server 2012 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="4b6de-122">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4b6de-123">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4b6de-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="4b6de-124">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4b6de-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4b6de-125">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4b6de-126">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-126">In-process hosting model</span></span>

<span data-ttu-id="4b6de-127">По умолчанию приложения ASP.NET Core используют модель внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="4b6de-128">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="4b6de-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="4b6de-129">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="4b6de-130">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="4b6de-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="4b6de-131">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="4b6de-132">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="4b6de-133">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="4b6de-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="4b6de-134">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="4b6de-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="4b6de-135">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="4b6de-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="4b6de-136">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-136">Use one app pool per app.</span></span>

* <span data-ttu-id="4b6de-137">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла `app_offline.htm` в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4b6de-138">Например, подключение WebSocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="4b6de-139">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="4b6de-140">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-140">Client disconnects are detected.</span></span> <span data-ttu-id="4b6de-141">Маркер отмены [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) аннулируется при отключении клиента.</span><span class="sxs-lookup"><span data-stu-id="4b6de-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="4b6de-142">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, `C:\Windows\System32\inetsrv` для `w3wp.exe`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="4b6de-143">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс `CurrentDirectoryHelpers`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="4b6de-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="4b6de-144">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="4b6de-145">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="4b6de-146">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="4b6de-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="4b6de-147">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4b6de-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="4b6de-148">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="4b6de-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="4b6de-149">[Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="4b6de-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4b6de-150">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="4b6de-150">Out-of-process hosting model</span></span>

<span data-ttu-id="4b6de-151">Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (`.csproj`):</span><span class="sxs-lookup"><span data-stu-id="4b6de-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4b6de-152">Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4b6de-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="4b6de-153">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="4b6de-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4b6de-154">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4b6de-155">При размещении вне процесса [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> в следующих целях.</span><span class="sxs-lookup"><span data-stu-id="4b6de-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="4b6de-156">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="4b6de-157">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="4b6de-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="4b6de-158">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-158">Hosting model changes</span></span>

<span data-ttu-id="4b6de-159">Если параметр `hostingModel` изменяется в файле `web.config` (как описано в разделе [Конфигурация с помощью `web.config`](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="4b6de-160">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="4b6de-161">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="4b6de-162">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="4b6de-162">Process name</span></span>

<span data-ttu-id="4b6de-163">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="4b6de-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="4b6de-164">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="4b6de-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4b6de-165">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4b6de-166">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4b6de-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4b6de-167">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4b6de-168">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4b6de-169">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="4b6de-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4b6de-170">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b6de-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4b6de-171">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4b6de-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4b6de-172">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-172">Configuration with web.config</span></span>

<span data-ttu-id="4b6de-173">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="4b6de-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4b6de-174">Следующий файл `web.config` публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="4b6de-175">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="4b6de-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="4b6de-176">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="4b6de-177">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-178">Путь сохраняет журналы stdout в папке `LogFiles`, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="4b6de-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4b6de-179">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4b6de-180">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4b6de-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4b6de-181">Атрибут</span><span class="sxs-lookup"><span data-stu-id="4b6de-181">Attribute</span></span> | <span data-ttu-id="4b6de-182">Описание</span><span class="sxs-lookup"><span data-stu-id="4b6de-182">Description</span></span> | <span data-ttu-id="4b6de-183">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="4b6de-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4b6de-184">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-184">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-185">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4b6de-186">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-187">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4b6de-188">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-189">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт `%ASPNETCORE_PORT%`, как заголовок `'MS-ASPNETCORE-WINAUTHTOKEN'` каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="4b6de-190">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="4b6de-191">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-191">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-192">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="4b6de-193">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-194">Указывает число экземпляров процесса, заданное в параметре **processPath** , которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4b6de-195">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="4b6de-196">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4b6de-197">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4b6de-198">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-198">Default: `1`</span></span><br><span data-ttu-id="4b6de-199">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-199">Min: `1`</span></span><br><span data-ttu-id="4b6de-200">Максимум: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="4b6de-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="4b6de-201">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-201">Required string attribute.</span></span></p><p><span data-ttu-id="4b6de-202">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4b6de-203">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-203">Relative paths are supported.</span></span> <span data-ttu-id="4b6de-204">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4b6de-205">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-206">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="4b6de-207">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="4b6de-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="4b6de-208">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="4b6de-209">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-209">Default: `10`</span></span><br><span data-ttu-id="4b6de-210">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-210">Min: `0`</span></span><br><span data-ttu-id="4b6de-211">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="4b6de-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4b6de-212">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="4b6de-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4b6de-213">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="4b6de-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4b6de-214">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="4b6de-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="4b6de-215">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="4b6de-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="4b6de-216">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="4b6de-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="4b6de-217">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="4b6de-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="4b6de-218">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="4b6de-219">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-219">Default: `00:02:00`</span></span><br><span data-ttu-id="4b6de-220">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-220">Min: `00:00:00`</span></span><br><span data-ttu-id="4b6de-221">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4b6de-222">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-223">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="4b6de-224">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-224">Default: `10`</span></span><br><span data-ttu-id="4b6de-225">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-225">Min: `0`</span></span><br><span data-ttu-id="4b6de-226">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4b6de-227">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-228">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4b6de-229">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="4b6de-230">*Внутрипроцессное* размещение. Процесс **не** перезапускается, и параметр **rapidFailsPerMinute** **не** используется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-230">When hosting *in-process* : The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="4b6de-231">Размещение *вне процесса*. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-231">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4b6de-232">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="4b6de-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4b6de-233">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="4b6de-233">Default: `120`</span></span><br><span data-ttu-id="4b6de-234">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-234">Min: `0`</span></span><br><span data-ttu-id="4b6de-235">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4b6de-236">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-237">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath** , перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4b6de-238">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-238">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-239">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="4b6de-240">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4b6de-241">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4b6de-242">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-243">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( `.log`) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="4b6de-244">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как `stdout_20180205194132_1934.log` в папке `logs` с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="4b6de-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="4b6de-245">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="4b6de-245">Set environment variables</span></span>

<span data-ttu-id="4b6de-246">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4b6de-247">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="4b6de-248">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="4b6de-249">В приведенном ниже примере устанавливаются две переменные среды в `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="4b6de-250">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4b6de-251">Разработчик может временно задать это значение в файле `web.config`, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4b6de-252">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="4b6de-253">Вместо установки среды напрямую в `web.config` можно включить свойство `<EnvironmentName>` в [профиль публикации (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4b6de-254">При этом подходе во время публикации проекта среда задается в файле `web.config`:</span><span class="sxs-lookup"><span data-stu-id="4b6de-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="4b6de-255">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="4b6de-256">Если в корневом каталоге приложения обнаружен файл с именем `app_offline.htm`, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4b6de-257">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4b6de-258">Хотя файл `app_offline.htm` присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="4b6de-259">Когда `app_offline.htm` файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="4b6de-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="4b6de-260">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4b6de-261">Например, подключение WebSocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4b6de-262">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="4b6de-262">Start-up error page</span></span>

<span data-ttu-id="4b6de-263">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="4b6de-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="4b6de-264">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-265">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-266">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-267">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4b6de-268">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4b6de-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4b6de-269">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="4b6de-269">Log creation and redirection</span></span>

<span data-ttu-id="4b6de-270">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4b6de-271">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-272">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="4b6de-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="4b6de-273">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4b6de-274">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="4b6de-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4b6de-275">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4b6de-276">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4b6de-277">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4b6de-278">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4b6de-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4b6de-279">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4b6de-280">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( `.log`) к последнему сегменту атрибута пути `stdoutLogFile` (обычно `stdout`) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="4b6de-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="4b6de-281">Если атрибут пути `stdoutLogFile` заканчивается элементом `stdout`, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="4b6de-282">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="4b6de-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="4b6de-283">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="4b6de-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="4b6de-284">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4b6de-285">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="4b6de-286">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-287">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="4b6de-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4b6de-288">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="4b6de-289">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4b6de-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="4b6de-290">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="4b6de-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="4b6de-291">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="4b6de-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="4b6de-292">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="4b6de-293">Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="4b6de-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="4b6de-294">Все папки, указанные в пути (`logs` в приведенном выше примере), создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-295">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).</span><span class="sxs-lookup"><span data-stu-id="4b6de-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="4b6de-296">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="4b6de-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="4b6de-297">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="4b6de-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="4b6de-298">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="4b6de-298">ERROR</span></span>
* <span data-ttu-id="4b6de-299">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="4b6de-299">WARNING</span></span>
* <span data-ttu-id="4b6de-300">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="4b6de-300">INFO</span></span>
* <span data-ttu-id="4b6de-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="4b6de-301">TRACE</span></span>

<span data-ttu-id="4b6de-302">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="4b6de-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="4b6de-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="4b6de-303">CONSOLE</span></span>
* <span data-ttu-id="4b6de-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="4b6de-304">EVENTLOG</span></span>
* <span data-ttu-id="4b6de-305">FILE</span><span class="sxs-lookup"><span data-stu-id="4b6de-305">FILE</span></span>

<span data-ttu-id="4b6de-306">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="4b6de-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="4b6de-307">`ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="4b6de-308">(Значение по умолчанию: `aspnetcore-debug.log`.)</span><span class="sxs-lookup"><span data-stu-id="4b6de-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="4b6de-309">`ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="4b6de-310">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="4b6de-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="4b6de-311">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="4b6de-311">The size of the log isn't limited.</span></span> <span data-ttu-id="4b6de-312">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="4b6de-313">См. пример элемента `aspNetCore` в файле `web.config` в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="4b6de-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="4b6de-314">Изменение размера стека</span><span class="sxs-lookup"><span data-stu-id="4b6de-314">Modify the stack size</span></span>

<span data-ttu-id="4b6de-315">*Применяется только при использовании модели внутрипроцессного размещения.*</span><span class="sxs-lookup"><span data-stu-id="4b6de-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="4b6de-316">Настройте размер управляемого стека, задав для параметра `stackSize` значение в байтах в `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="4b6de-317">Размер по умолчанию — 1 048 576 байт (1 МБ).</span><span class="sxs-lookup"><span data-stu-id="4b6de-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4b6de-318">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="4b6de-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4b6de-319">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="4b6de-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="4b6de-320">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b6de-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4b6de-321">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4b6de-322">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="4b6de-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4b6de-323">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4b6de-324">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="4b6de-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4b6de-325">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4b6de-326">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4b6de-327">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4b6de-328">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4b6de-329">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="4b6de-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4b6de-330">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="4b6de-331">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле `applicationHost.config` общей папки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="4b6de-332">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="4b6de-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="4b6de-333">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="4b6de-334">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4b6de-335">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="4b6de-335">Run the installer.</span></span>
1. <span data-ttu-id="4b6de-336">Экспортируйте обновленный файл `applicationHost.config` в общую папку.</span><span class="sxs-lookup"><span data-stu-id="4b6de-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="4b6de-337">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4b6de-338">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="4b6de-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4b6de-339">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4b6de-340">В системе размещения перейдите к папке `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="4b6de-341">Найдите файл `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="4b6de-342">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="4b6de-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4b6de-343">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="4b6de-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4b6de-344">Журналы установщика пакета размещения для модуля находятся в папке `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="4b6de-345">Имя файла — `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4b6de-346">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="4b6de-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4b6de-347">Module</span><span class="sxs-lookup"><span data-stu-id="4b6de-347">Module</span></span>

<span data-ttu-id="4b6de-348">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4b6de-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="4b6de-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4b6de-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="4b6de-350">Схема</span><span class="sxs-lookup"><span data-stu-id="4b6de-350">Schema</span></span>

<span data-ttu-id="4b6de-351">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="4b6de-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="4b6de-353">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="4b6de-353">Configuration</span></span>

<span data-ttu-id="4b6de-354">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="4b6de-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-355">**IIS Express**</span></span>

* <span data-ttu-id="4b6de-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="4b6de-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="4b6de-357">CLI *iisexpress.exe* : `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="4b6de-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="4b6de-358">Файлы можно найти путем поиска `aspnetcore` в файле `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4b6de-359">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="4b6de-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="4b6de-360">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4b6de-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="4b6de-361">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="4b6de-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="4b6de-362">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="4b6de-362">Supported Windows versions:</span></span>

* <span data-ttu-id="4b6de-363">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-363">Windows 7 or later</span></span>
* <span data-ttu-id="4b6de-364">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b6de-365">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4b6de-366">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4b6de-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="4b6de-367">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4b6de-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="4b6de-368">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="4b6de-369">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-369">In-process hosting model</span></span>

<span data-ttu-id="4b6de-370">Чтобы настроить приложение для внутрипроцессного размещения, добавьте свойство `<AspNetCoreHostingModel>` к файлу проекта приложения со значением `InProcess` (размещение вне процесса имеет значение `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="4b6de-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4b6de-371">Модель внутрипроцессного размещения не поддерживается для приложений ASP.NET Core, предназначенных для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="4b6de-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="4b6de-372">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="4b6de-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4b6de-373">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="4b6de-374">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="4b6de-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="4b6de-375">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="4b6de-376">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="4b6de-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="4b6de-377">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="4b6de-378">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="4b6de-379">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="4b6de-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="4b6de-380">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="4b6de-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="4b6de-381">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="4b6de-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="4b6de-382">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-382">Use one app pool per app.</span></span>

* <span data-ttu-id="4b6de-383">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4b6de-384">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="4b6de-385">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="4b6de-386">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-386">Client disconnects are detected.</span></span> <span data-ttu-id="4b6de-387">При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).</span><span class="sxs-lookup"><span data-stu-id="4b6de-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="4b6de-388">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe* ).</span><span class="sxs-lookup"><span data-stu-id="4b6de-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe* ).</span></span>

  <span data-ttu-id="4b6de-389">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="4b6de-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="4b6de-390">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="4b6de-391">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="4b6de-392">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="4b6de-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="4b6de-393">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="4b6de-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="4b6de-394">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="4b6de-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="4b6de-395">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="4b6de-395">Out-of-process hosting model</span></span>

<span data-ttu-id="4b6de-396">Чтобы настроить приложение для размещения вне процесса, используйте один из следующих подходов в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="4b6de-397">Не указывайте свойство `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="4b6de-398">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="4b6de-399">Установите для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` (внутрипроцессное размещение имеет значение `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="4b6de-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="4b6de-400">Это значение не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="4b6de-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="4b6de-401">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="4b6de-402">Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="4b6de-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="4b6de-403">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="4b6de-404">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="4b6de-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="4b6de-405">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="4b6de-405">Hosting model changes</span></span>

<span data-ttu-id="4b6de-406">Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="4b6de-407">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="4b6de-408">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="4b6de-409">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="4b6de-409">Process name</span></span>

<span data-ttu-id="4b6de-410">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="4b6de-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="4b6de-411">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="4b6de-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4b6de-412">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4b6de-413">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4b6de-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4b6de-414">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4b6de-415">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4b6de-416">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="4b6de-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4b6de-417">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b6de-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4b6de-418">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4b6de-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4b6de-419">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-419">Configuration with web.config</span></span>

<span data-ttu-id="4b6de-420">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="4b6de-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4b6de-421">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="4b6de-422">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="4b6de-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="4b6de-423">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="4b6de-424">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-425">Путь сохраняет журналы stdout в папке *LogFiles* , расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="4b6de-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4b6de-426">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4b6de-427">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4b6de-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4b6de-428">Атрибут</span><span class="sxs-lookup"><span data-stu-id="4b6de-428">Attribute</span></span> | <span data-ttu-id="4b6de-429">Описание</span><span class="sxs-lookup"><span data-stu-id="4b6de-429">Description</span></span> | <span data-ttu-id="4b6de-430">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="4b6de-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4b6de-431">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-431">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-432">Аргументы для исполняемого файла, указанного в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4b6de-433">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-434">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4b6de-435">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-436">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="4b6de-437">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="4b6de-438">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-438">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-439">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="4b6de-440">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-441">Указывает число экземпляров процесса, заданное в параметре `processPath`, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4b6de-442">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="4b6de-443">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4b6de-444">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4b6de-445">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-445">Default: `1`</span></span><br><span data-ttu-id="4b6de-446">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-446">Min: `1`</span></span><br><span data-ttu-id="4b6de-447">Максимум: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="4b6de-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="4b6de-448">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-448">Required string attribute.</span></span></p><p><span data-ttu-id="4b6de-449">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4b6de-450">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-450">Relative paths are supported.</span></span> <span data-ttu-id="4b6de-451">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4b6de-452">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-453">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="4b6de-454">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="4b6de-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="4b6de-455">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="4b6de-456">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-456">Default: `10`</span></span><br><span data-ttu-id="4b6de-457">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-457">Min: `0`</span></span><br><span data-ttu-id="4b6de-458">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="4b6de-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4b6de-459">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="4b6de-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4b6de-460">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="4b6de-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4b6de-461">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="4b6de-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="4b6de-462">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="4b6de-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="4b6de-463">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="4b6de-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="4b6de-464">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="4b6de-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="4b6de-465">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="4b6de-466">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-466">Default: `00:02:00`</span></span><br><span data-ttu-id="4b6de-467">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-467">Min: `00:00:00`</span></span><br><span data-ttu-id="4b6de-468">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4b6de-469">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-470">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="4b6de-471">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-471">Default: `10`</span></span><br><span data-ttu-id="4b6de-472">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-472">Min: `0`</span></span><br><span data-ttu-id="4b6de-473">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4b6de-474">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-475">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4b6de-476">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="4b6de-477">*Внутрипроцессное* размещение. Процесс **не** перезапускается, и параметр `rapidFailsPerMinute` **не** используется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-477">When hosting *in-process* : The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="4b6de-478">Размещение *вне процесса*. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте `rapidFailsPerMinute` количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-478">When hosting *out-of-process* : The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4b6de-479">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="4b6de-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4b6de-480">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="4b6de-480">Default: `120`</span></span><br><span data-ttu-id="4b6de-481">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-481">Min: `0`</span></span><br><span data-ttu-id="4b6de-482">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4b6de-483">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-484">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте `processPath`, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4b6de-485">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-485">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-486">Указывает относительный или абсолютный путь к файлу, для которого регистрируются `stdout` и `stderr` из процесса, указанного в `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="4b6de-487">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4b6de-488">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4b6de-489">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-490">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( `.log`) добавляются к последнему сегменту пути журнала `stdoutLogFile`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="4b6de-491">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как `stdout_20180205194132_1934.log` в папке `logs` с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="4b6de-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="4b6de-492">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="4b6de-492">Setting environment variables</span></span>

<span data-ttu-id="4b6de-493">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4b6de-494">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="4b6de-495">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="4b6de-496">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-496">The following example sets two environment variables.</span></span> <span data-ttu-id="4b6de-497">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4b6de-498">Разработчик может временно задать это значение в файле `web.config`, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4b6de-499">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="4b6de-500">Вместо установки среды напрямую в `web.config` включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="4b6de-501">При этом подходе во время публикации проекта среда задается в файле `web.config`:</span><span class="sxs-lookup"><span data-stu-id="4b6de-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="4b6de-502">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="4b6de-503">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="4b6de-503">app_offline.htm</span></span>

<span data-ttu-id="4b6de-504">Если в корневом каталоге приложения обнаружен файл с именем `app_offline.htm`, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4b6de-505">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4b6de-506">Хотя файл `app_offline.htm` присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="4b6de-507">Когда `app_offline.htm` файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="4b6de-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="4b6de-508">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="4b6de-509">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4b6de-510">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="4b6de-510">Start-up error page</span></span>

<span data-ttu-id="4b6de-511">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="4b6de-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="4b6de-512">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-513">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-514">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="4b6de-515">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4b6de-516">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4b6de-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4b6de-517">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="4b6de-517">Log creation and redirection</span></span>

<span data-ttu-id="4b6de-518">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4b6de-519">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-520">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).</span><span class="sxs-lookup"><span data-stu-id="4b6de-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="4b6de-521">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4b6de-522">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="4b6de-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4b6de-523">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4b6de-524">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4b6de-525">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4b6de-526">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4b6de-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4b6de-527">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4b6de-528">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( `.log`) к последнему сегменту атрибута пути `stdoutLogFile` (обычно `stdout`) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="4b6de-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="4b6de-529">Если атрибут пути `stdoutLogFile` заканчивается элементом `stdout`, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="4b6de-530">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="4b6de-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="4b6de-531">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="4b6de-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="4b6de-532">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4b6de-533">Убедитесь в том, что идентификатор пользователя пула приложений имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="4b6de-534">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-535">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="4b6de-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4b6de-536">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4b6de-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="4b6de-537">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="4b6de-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="4b6de-538">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="4b6de-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="4b6de-539">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в `web.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="4b6de-540">Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="4b6de-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="4b6de-541">Папки, указанные в пути к значению `<handlerSetting>` (`logs` в приведенном выше примере), не создаются модулем автоматически и должны заранее существовать в развертывании.</span><span class="sxs-lookup"><span data-stu-id="4b6de-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="4b6de-542">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\{APP POOL NAME}` для предоставления разрешения на запись, где заполнитель `{APP POOL NAME}` — это имя пула приложений).</span><span class="sxs-lookup"><span data-stu-id="4b6de-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="4b6de-543">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="4b6de-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="4b6de-544">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="4b6de-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="4b6de-545">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="4b6de-545">ERROR</span></span>
* <span data-ttu-id="4b6de-546">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="4b6de-546">WARNING</span></span>
* <span data-ttu-id="4b6de-547">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="4b6de-547">INFO</span></span>
* <span data-ttu-id="4b6de-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="4b6de-548">TRACE</span></span>

<span data-ttu-id="4b6de-549">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="4b6de-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="4b6de-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="4b6de-550">CONSOLE</span></span>
* <span data-ttu-id="4b6de-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="4b6de-551">EVENTLOG</span></span>
* <span data-ttu-id="4b6de-552">FILE</span><span class="sxs-lookup"><span data-stu-id="4b6de-552">FILE</span></span>

<span data-ttu-id="4b6de-553">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="4b6de-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="4b6de-554">`ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="4b6de-555">(Значение по умолчанию: `aspnetcore-debug.log`.)</span><span class="sxs-lookup"><span data-stu-id="4b6de-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="4b6de-556">`ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="4b6de-557">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="4b6de-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="4b6de-558">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="4b6de-558">The size of the log isn't limited.</span></span> <span data-ttu-id="4b6de-559">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="4b6de-560">См. пример элемента `aspNetCore` в файле `web.config` в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="4b6de-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4b6de-561">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="4b6de-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4b6de-562">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="4b6de-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="4b6de-563">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b6de-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4b6de-564">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4b6de-565">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="4b6de-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4b6de-566">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4b6de-567">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="4b6de-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4b6de-568">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4b6de-569">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4b6de-570">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4b6de-571">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4b6de-572">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="4b6de-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4b6de-573">Программа установки модуля ASP.NET Core запускается с правами учетной записи `TrustedInstaller`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="4b6de-574">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле `applicationHost.config` общей папки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="4b6de-575">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="4b6de-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="4b6de-576">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="4b6de-577">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4b6de-578">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="4b6de-578">Run the installer.</span></span>
1. <span data-ttu-id="4b6de-579">Экспортируйте обновленный файл `applicationHost.config` в общую папку.</span><span class="sxs-lookup"><span data-stu-id="4b6de-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="4b6de-580">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4b6de-581">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="4b6de-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4b6de-582">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4b6de-583">В системе размещения перейдите к папке `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="4b6de-584">Найдите файл `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="4b6de-585">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="4b6de-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4b6de-586">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="4b6de-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4b6de-587">Журналы установщика пакета размещения для модуля находятся в папке `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="4b6de-588">Файл имеет имя `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, где заполнитель `{TIMESTAMP}` — это метка времени.</span><span class="sxs-lookup"><span data-stu-id="4b6de-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4b6de-589">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="4b6de-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4b6de-590">Module</span><span class="sxs-lookup"><span data-stu-id="4b6de-590">Module</span></span>

<span data-ttu-id="4b6de-591">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="4b6de-591">**IIS (x86/amd64)** :</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="4b6de-592">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="4b6de-592">**IIS Express (x86/amd64)** :</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="4b6de-593">Схема</span><span class="sxs-lookup"><span data-stu-id="4b6de-593">Schema</span></span>

<span data-ttu-id="4b6de-594">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="4b6de-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="4b6de-596">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="4b6de-596">Configuration</span></span>

<span data-ttu-id="4b6de-597">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="4b6de-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-598">**IIS Express**</span></span>

* <span data-ttu-id="4b6de-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="4b6de-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="4b6de-600">CLI *iisexpress.exe* : `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="4b6de-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="4b6de-601">Файлы можно найти путем поиска `aspnetcore` в файле `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4b6de-602">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для переадресации веб-запросов в серверные приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="4b6de-603">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="4b6de-603">Supported Windows versions:</span></span>

* <span data-ttu-id="4b6de-604">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-604">Windows 7 or later</span></span>
* <span data-ttu-id="4b6de-605">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="4b6de-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b6de-606">Модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4b6de-606">The module only works with Kestrel.</span></span> <span data-ttu-id="4b6de-607">Модуль несовместим с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4b6de-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="4b6de-608">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль также обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="4b6de-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="4b6de-609">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое.</span><span class="sxs-lookup"><span data-stu-id="4b6de-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="4b6de-610">Это, по сути, совпадает с поведением приложений ASP.NET 4.x, выполняемых внутрипроцессно в IIS и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="4b6de-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="4b6de-611">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением:</span><span class="sxs-lookup"><span data-stu-id="4b6de-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Модуль ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="4b6de-613">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="4b6de-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="4b6de-614">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="4b6de-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="4b6de-615">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="4b6de-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="4b6de-616">Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="4b6de-617">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="4b6de-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="4b6de-618">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="4b6de-619">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="4b6de-620">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="4b6de-621">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="4b6de-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="4b6de-622">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="4b6de-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="4b6de-623">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="4b6de-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="4b6de-624">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="4b6de-625">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4b6de-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="4b6de-626">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="4b6de-627">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="4b6de-628">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="4b6de-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="4b6de-629">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b6de-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="4b6de-630">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="4b6de-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="4b6de-631">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-631">Configuration with web.config</span></span>

<span data-ttu-id="4b6de-632">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="4b6de-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="4b6de-633">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="4b6de-634">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="4b6de-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="4b6de-635">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-636">Путь сохраняет журналы stdout в папке *LogFiles* , расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="4b6de-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="4b6de-637">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="4b6de-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="4b6de-638">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="4b6de-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="4b6de-639">Атрибут</span><span class="sxs-lookup"><span data-stu-id="4b6de-639">Attribute</span></span> | <span data-ttu-id="4b6de-640">Описание</span><span class="sxs-lookup"><span data-stu-id="4b6de-640">Description</span></span> | <span data-ttu-id="4b6de-641">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="4b6de-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="4b6de-642">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-642">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-643">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="4b6de-644">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-645">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="4b6de-646">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-647">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="4b6de-648">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4b6de-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="4b6de-649">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-650">Указывает число экземпляров процесса, заданное в параметре **processPath** , которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="4b6de-651">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="4b6de-652">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="4b6de-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="4b6de-653">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-653">Default: `1`</span></span><br><span data-ttu-id="4b6de-654">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="4b6de-654">Min: `1`</span></span><br><span data-ttu-id="4b6de-655">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="4b6de-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="4b6de-656">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-656">Required string attribute.</span></span></p><p><span data-ttu-id="4b6de-657">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="4b6de-658">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-658">Relative paths are supported.</span></span> <span data-ttu-id="4b6de-659">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="4b6de-660">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-661">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="4b6de-662">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="4b6de-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="4b6de-663">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-663">Default: `10`</span></span><br><span data-ttu-id="4b6de-664">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-664">Min: `0`</span></span><br><span data-ttu-id="4b6de-665">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="4b6de-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="4b6de-666">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="4b6de-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="4b6de-667">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="4b6de-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="4b6de-668">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="4b6de-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="4b6de-669">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-669">Default: `00:02:00`</span></span><br><span data-ttu-id="4b6de-670">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-670">Min: `00:00:00`</span></span><br><span data-ttu-id="4b6de-671">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="4b6de-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="4b6de-672">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-673">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="4b6de-674">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="4b6de-674">Default: `10`</span></span><br><span data-ttu-id="4b6de-675">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-675">Min: `0`</span></span><br><span data-ttu-id="4b6de-676">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="4b6de-677">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="4b6de-678">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="4b6de-679">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="4b6de-680">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="4b6de-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="4b6de-681">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="4b6de-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="4b6de-682">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="4b6de-682">Default: `120`</span></span><br><span data-ttu-id="4b6de-683">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="4b6de-683">Min: `0`</span></span><br><span data-ttu-id="4b6de-684">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="4b6de-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="4b6de-685">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="4b6de-686">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath** , перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="4b6de-687">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="4b6de-687">Optional string attribute.</span></span></p><p><span data-ttu-id="4b6de-688">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="4b6de-689">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="4b6de-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="4b6de-690">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="4b6de-691">Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="4b6de-692">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log* ) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-692">Using underscore delimiters, a timestamp, process ID, and file extension ( *.log* ) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="4b6de-693">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="4b6de-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="4b6de-694">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="4b6de-694">Setting environment variables</span></span>

<span data-ttu-id="4b6de-695">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="4b6de-696">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="4b6de-697">Переменные среды, заданные в этом разделе, конфликтуют с переменными системной среды с такими же именами.</span><span class="sxs-lookup"><span data-stu-id="4b6de-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="4b6de-698">Если переменная среды задана и в файле *web.config* , и на уровне системы в Windows, значение из файла *web.config* добавляется к значению переменной системной среды (например, `ASPNETCORE_ENVIRONMENT: Development;Development`), которая препятствует запуску приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="4b6de-699">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-699">The following example sets two environment variables.</span></span> <span data-ttu-id="4b6de-700">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="4b6de-701">Разработчик может временно задать это значение в файле *web.config* , чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="4b6de-702">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="4b6de-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="4b6de-703">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="4b6de-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="4b6de-704">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="4b6de-704">app_offline.htm</span></span>

<span data-ttu-id="4b6de-705">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm* , модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="4b6de-706">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="4b6de-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="4b6de-707">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="4b6de-708">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="4b6de-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="4b6de-709">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="4b6de-709">Start-up error page</span></span>

<span data-ttu-id="4b6de-710">Если модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="4b6de-711">Чтобы подавить эту страницу и вернуться к странице IIS кода состояния 502 по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="4b6de-712">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="4b6de-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="4b6de-713">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="4b6de-713">Log creation and redirection</span></span>

<span data-ttu-id="4b6de-714">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="4b6de-715">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="4b6de-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="4b6de-716">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="4b6de-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="4b6de-717">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="4b6de-718">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="4b6de-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="4b6de-719">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="4b6de-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="4b6de-720">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="4b6de-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="4b6de-721">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="4b6de-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="4b6de-722">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="4b6de-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="4b6de-723">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="4b6de-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="4b6de-724">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log* ) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout* ) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="4b6de-724">The log file name is composed by appending the timestamp, process ID, and file extension ( *.log* ) to the last segment of the `stdoutLogFile` path (typically *stdout* ) delimited by underscores.</span></span> <span data-ttu-id="4b6de-725">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout* , журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-725">If the `stdoutLogFile` path ends with *stdout* , a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="4b6de-726">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="4b6de-727">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="4b6de-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="4b6de-728">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="4b6de-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="4b6de-729">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="4b6de-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="4b6de-730">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4b6de-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="4b6de-731">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="4b6de-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="4b6de-732">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="4b6de-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="4b6de-733">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="4b6de-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="4b6de-734">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="4b6de-735">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="4b6de-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="4b6de-736">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="4b6de-737">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="4b6de-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="4b6de-738">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="4b6de-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="4b6de-739">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="4b6de-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="4b6de-740">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="4b6de-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="4b6de-741">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="4b6de-742">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="4b6de-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="4b6de-743">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="4b6de-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="4b6de-744">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="4b6de-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="4b6de-745">При использовании общей конфигурации IIS выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="4b6de-746">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="4b6de-747">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="4b6de-747">Run the installer.</span></span>
1. <span data-ttu-id="4b6de-748">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="4b6de-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="4b6de-749">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="4b6de-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="4b6de-750">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="4b6de-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="4b6de-751">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="4b6de-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="4b6de-752">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="4b6de-753">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="4b6de-754">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="4b6de-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="4b6de-755">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="4b6de-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="4b6de-756">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="4b6de-757">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="4b6de-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="4b6de-758">Module</span><span class="sxs-lookup"><span data-stu-id="4b6de-758">Module</span></span>

<span data-ttu-id="4b6de-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4b6de-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="4b6de-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4b6de-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="4b6de-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4b6de-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="4b6de-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="4b6de-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="4b6de-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4b6de-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="4b6de-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="4b6de-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="4b6de-765">Схема</span><span class="sxs-lookup"><span data-stu-id="4b6de-765">Schema</span></span>

<span data-ttu-id="4b6de-766">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-766">**IIS**</span></span>

* <span data-ttu-id="4b6de-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="4b6de-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="4b6de-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-768">**IIS Express**</span></span>

* <span data-ttu-id="4b6de-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="4b6de-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="4b6de-770">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="4b6de-770">Configuration</span></span>

<span data-ttu-id="4b6de-771">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="4b6de-771">**IIS**</span></span>

* <span data-ttu-id="4b6de-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="4b6de-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="4b6de-773">**IIS Express**</span></span>

* <span data-ttu-id="4b6de-774">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="4b6de-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="4b6de-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="4b6de-776">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="4b6de-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4b6de-777">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4b6de-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="4b6de-778">[Справочные материалы по модулю ASP.NET Core (главная ветвь)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Используйте раскрывающийся список **Branch** (Ветвь), чтобы выбрать нужный выпуск (например, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="4b6de-778">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
