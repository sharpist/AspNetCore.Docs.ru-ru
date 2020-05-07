---
title: Модуль ASP.NET Core
author: rick-anderson
description: Сведения о настройке модуля ASP.NET Core для размещения приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: c9cc340c9a3d6ff1b3783686bd779dfe04b14275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775275"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="af955-103">Модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af955-103">ASP.NET Core Module</span></span>

<span data-ttu-id="af955-104">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Рик Штраль](https://github.com/RickStrahl) (Rick Strahl), [Крис Росс](https://github.com/Tratcher) (Chris Ross), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Сураб Ширхатти](https://twitter.com/sshirhatti) (Sourabh Shirhatti) и [Джастин Коталик](https://github.com/jkotalik) (Justin Kotalik)</span><span class="sxs-lookup"><span data-stu-id="af955-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="af955-105">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="af955-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="af955-106">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af955-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="af955-107">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af955-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="af955-108">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="af955-108">Supported Windows versions:</span></span>

* <span data-ttu-id="af955-109">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-109">Windows 7 or later</span></span>
* <span data-ttu-id="af955-110">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="af955-111">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af955-112">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af955-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="af955-113">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af955-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="af955-114">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="af955-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="af955-115">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="af955-115">In-process hosting model</span></span>

<span data-ttu-id="af955-116">По умолчанию приложения ASP.NET Core используют модель внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="af955-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="af955-117">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="af955-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="af955-118">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="af955-119">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="af955-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="af955-120">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="af955-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="af955-121">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="af955-122">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="af955-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="af955-123">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="af955-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="af955-124">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="af955-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="af955-125">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-125">Use one app pool per app.</span></span>

* <span data-ttu-id="af955-126">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="af955-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af955-127">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="af955-128">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="af955-129">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="af955-129">Client disconnects are detected.</span></span> <span data-ttu-id="af955-130">При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).</span><span class="sxs-lookup"><span data-stu-id="af955-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="af955-131">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="af955-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="af955-132">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="af955-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="af955-133">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="af955-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="af955-134">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="af955-135">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="af955-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="af955-136">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="af955-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="af955-137">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="af955-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="af955-138">[Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="af955-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="af955-139">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="af955-139">Out-of-process hosting model</span></span>

<span data-ttu-id="af955-140">Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (*CSPROJ*):</span><span class="sxs-lookup"><span data-stu-id="af955-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af955-141">Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="af955-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="af955-142">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="af955-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af955-143">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af955-144">Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="af955-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="af955-145">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="af955-146">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="af955-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="af955-147">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="af955-147">Hosting model changes</span></span>

<span data-ttu-id="af955-148">Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="af955-149">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="af955-150">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="af955-151">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="af955-151">Process name</span></span>

<span data-ttu-id="af955-152">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="af955-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="af955-153">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="af955-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af955-154">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="af955-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af955-155">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af955-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af955-156">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="af955-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af955-157">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="af955-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af955-158">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="af955-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af955-159">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af955-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af955-160">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af955-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af955-161">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="af955-161">Configuration with web.config</span></span>

<span data-ttu-id="af955-162">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="af955-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af955-163">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="af955-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af955-164">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="af955-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af955-165">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<расположение>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="af955-166">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-167">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="af955-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af955-168">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="af955-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af955-169">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af955-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af955-170">Атрибут</span><span class="sxs-lookup"><span data-stu-id="af955-170">Attribute</span></span> | <span data-ttu-id="af955-171">Описание</span><span class="sxs-lookup"><span data-stu-id="af955-171">Description</span></span> | <span data-ttu-id="af955-172">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="af955-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af955-173">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-173">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-174">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af955-175">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-176">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="af955-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af955-177">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-178">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af955-179">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="af955-180">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-180">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-181">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="af955-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="af955-182">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-183">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af955-184">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="af955-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="af955-185">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="af955-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af955-186">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="af955-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af955-187">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-187">Default: `1`</span></span><br><span data-ttu-id="af955-188">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-188">Min: `1`</span></span><br><span data-ttu-id="af955-189">Максимум: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="af955-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="af955-190">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-190">Required string attribute.</span></span></p><p><span data-ttu-id="af955-191">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af955-192">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-192">Relative paths are supported.</span></span> <span data-ttu-id="af955-193">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af955-194">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-195">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af955-196">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="af955-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="af955-197">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="af955-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="af955-198">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-198">Default: `10`</span></span><br><span data-ttu-id="af955-199">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-199">Min: `0`</span></span><br><span data-ttu-id="af955-200">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="af955-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af955-201">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="af955-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af955-202">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af955-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af955-203">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="af955-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="af955-204">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="af955-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="af955-205">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="af955-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="af955-206">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="af955-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="af955-207">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="af955-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="af955-208">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af955-208">Default: `00:02:00`</span></span><br><span data-ttu-id="af955-209">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-209">Min: `00:00:00`</span></span><br><span data-ttu-id="af955-210">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af955-211">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-212">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af955-213">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-213">Default: `10`</span></span><br><span data-ttu-id="af955-214">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-214">Min: `0`</span></span><br><span data-ttu-id="af955-215">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="af955-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af955-216">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-217">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="af955-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af955-218">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af955-219">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="af955-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af955-220">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="af955-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af955-221">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="af955-221">Default: `120`</span></span><br><span data-ttu-id="af955-222">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-222">Min: `0`</span></span><br><span data-ttu-id="af955-223">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="af955-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af955-224">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-225">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af955-226">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-226">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-227">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af955-228">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af955-229">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af955-230">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-231">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af955-232">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="af955-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="af955-233">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="af955-233">Set environment variables</span></span>

<span data-ttu-id="af955-234">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="af955-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af955-235">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="af955-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="af955-236">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="af955-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="af955-237">В следующем примере устанавливаются две переменные среды в *web.config*. `ASPNETCORE_ENVIRONMENT` настраивает в качестве среды приложения `Development`.</span><span class="sxs-lookup"><span data-stu-id="af955-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af955-238">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af955-239">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af955-240">Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="af955-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="af955-241">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="af955-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="af955-242">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="af955-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af955-243">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af955-243">app_offline.htm</span></span>

<span data-ttu-id="af955-244">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af955-245">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af955-246">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af955-247">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="af955-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="af955-248">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="af955-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af955-249">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af955-250">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="af955-250">Start-up error page</span></span>

<span data-ttu-id="af955-251">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="af955-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="af955-252">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="af955-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="af955-253">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="af955-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="af955-254">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="af955-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="af955-255">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="af955-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af955-256">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af955-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af955-257">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="af955-257">Log creation and redirection</span></span>

<span data-ttu-id="af955-258">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="af955-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af955-259">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-260">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="af955-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af955-261">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="af955-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af955-262">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="af955-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af955-263">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af955-264">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af955-265">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="af955-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af955-266">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="af955-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af955-267">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="af955-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af955-268">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="af955-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af955-269">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af955-270">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="af955-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="af955-271">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="af955-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="af955-272">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="af955-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af955-273">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="af955-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="af955-274">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-275">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="af955-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af955-276">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="af955-277">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af955-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="af955-278">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="af955-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="af955-279">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="af955-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="af955-280">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="af955-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="af955-281">Все папки, указанные в пути (*logs* в приведенном выше примере), создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-282">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="af955-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af955-283">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="af955-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="af955-284">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="af955-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="af955-285">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="af955-285">ERROR</span></span>
* <span data-ttu-id="af955-286">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="af955-286">WARNING</span></span>
* <span data-ttu-id="af955-287">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="af955-287">INFO</span></span>
* <span data-ttu-id="af955-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="af955-288">TRACE</span></span>

<span data-ttu-id="af955-289">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="af955-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="af955-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="af955-290">CONSOLE</span></span>
* <span data-ttu-id="af955-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="af955-291">EVENTLOG</span></span>
* <span data-ttu-id="af955-292">FILE</span><span class="sxs-lookup"><span data-stu-id="af955-292">FILE</span></span>

<span data-ttu-id="af955-293">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="af955-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="af955-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="af955-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="af955-295">(По умолчанию: *aspnetcore debug.log*)</span><span class="sxs-lookup"><span data-stu-id="af955-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="af955-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="af955-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="af955-297">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="af955-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="af955-298">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="af955-298">The size of the log isn't limited.</span></span> <span data-ttu-id="af955-299">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="af955-300">См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="af955-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="af955-301">Изменение размера стека</span><span class="sxs-lookup"><span data-stu-id="af955-301">Modify the stack size</span></span>

<span data-ttu-id="af955-302">*Применяется только при использовании модели внутрипроцессного размещения.*</span><span class="sxs-lookup"><span data-stu-id="af955-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="af955-303">Настройте размер управляемого стека, задав для параметра `stackSize` значение в байтах в файле *web.config*. Размер по умолчанию — `1048576` байт (1 МБ).</span><span class="sxs-lookup"><span data-stu-id="af955-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af955-304">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="af955-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af955-305">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="af955-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="af955-306">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="af955-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af955-307">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af955-308">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="af955-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af955-309">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="af955-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af955-310">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="af955-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af955-311">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="af955-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af955-312">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="af955-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af955-313">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af955-314">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af955-315">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="af955-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af955-316">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="af955-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af955-317">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="af955-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af955-318">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="af955-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="af955-319">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="af955-320">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af955-321">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="af955-321">Run the installer.</span></span>
1. <span data-ttu-id="af955-322">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="af955-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af955-323">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af955-324">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="af955-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af955-325">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af955-326">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af955-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af955-327">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="af955-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af955-328">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="af955-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af955-329">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="af955-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af955-330">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af955-331">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="af955-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af955-332">Module</span><span class="sxs-lookup"><span data-stu-id="af955-332">Module</span></span>

<span data-ttu-id="af955-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af955-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af955-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="af955-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af955-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af955-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af955-343">Схема</span><span class="sxs-lookup"><span data-stu-id="af955-343">Schema</span></span>

<span data-ttu-id="af955-344">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-344">**IIS**</span></span>

* <span data-ttu-id="af955-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af955-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af955-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="af955-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-347">**IIS Express**</span></span>

* <span data-ttu-id="af955-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af955-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af955-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af955-350">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="af955-350">Configuration</span></span>

<span data-ttu-id="af955-351">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-351">**IIS**</span></span>

* <span data-ttu-id="af955-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af955-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-353">**IIS Express**</span></span>

* <span data-ttu-id="af955-354">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af955-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af955-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af955-356">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="af955-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="af955-357">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="af955-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="af955-358">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af955-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="af955-359">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="af955-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="af955-360">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="af955-360">Supported Windows versions:</span></span>

* <span data-ttu-id="af955-361">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-361">Windows 7 or later</span></span>
* <span data-ttu-id="af955-362">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="af955-363">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af955-364">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af955-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="af955-365">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af955-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="af955-366">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="af955-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="af955-367">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="af955-367">In-process hosting model</span></span>

<span data-ttu-id="af955-368">Чтобы настроить приложение для внутрипроцессного размещения, добавьте свойство `<AspNetCoreHostingModel>` к файлу проекта приложения со значением `InProcess` (размещение вне процесса имеет значение `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="af955-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af955-369">Модель внутрипроцессного размещения не поддерживается для приложений ASP.NET Core, предназначенных для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="af955-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="af955-370">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="af955-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af955-371">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="af955-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="af955-372">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="af955-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="af955-373">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="af955-374">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="af955-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="af955-375">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="af955-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="af955-376">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="af955-377">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="af955-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="af955-378">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="af955-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="af955-379">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="af955-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="af955-380">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-380">Use one app pool per app.</span></span>

* <span data-ttu-id="af955-381">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="af955-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af955-382">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="af955-383">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="af955-384">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="af955-384">Client disconnects are detected.</span></span> <span data-ttu-id="af955-385">При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).</span><span class="sxs-lookup"><span data-stu-id="af955-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="af955-386">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="af955-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="af955-387">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="af955-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="af955-388">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="af955-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="af955-389">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="af955-390">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="af955-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="af955-391">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="af955-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="af955-392">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="af955-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="af955-393">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="af955-393">Out-of-process hosting model</span></span>

<span data-ttu-id="af955-394">Чтобы настроить приложение для размещения вне процесса, используйте один из следующих подходов в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="af955-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="af955-395">Не указывайте свойство `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="af955-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="af955-396">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="af955-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="af955-397">Установите для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` (внутрипроцессное размещение имеет значение `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="af955-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="af955-398">Это значение не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="af955-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="af955-399">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="af955-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="af955-400">Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="af955-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="af955-401">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="af955-402">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="af955-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="af955-403">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="af955-403">Hosting model changes</span></span>

<span data-ttu-id="af955-404">Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="af955-405">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="af955-406">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="af955-407">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="af955-407">Process name</span></span>

<span data-ttu-id="af955-408">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="af955-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="af955-409">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="af955-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af955-410">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="af955-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af955-411">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af955-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af955-412">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="af955-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af955-413">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="af955-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af955-414">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="af955-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af955-415">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af955-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af955-416">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af955-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af955-417">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="af955-417">Configuration with web.config</span></span>

<span data-ttu-id="af955-418">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="af955-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af955-419">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="af955-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af955-420">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="af955-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af955-421">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<расположение>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="af955-422">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-423">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="af955-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af955-424">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="af955-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af955-425">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af955-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af955-426">Атрибут</span><span class="sxs-lookup"><span data-stu-id="af955-426">Attribute</span></span> | <span data-ttu-id="af955-427">Описание</span><span class="sxs-lookup"><span data-stu-id="af955-427">Description</span></span> | <span data-ttu-id="af955-428">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="af955-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af955-429">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-429">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-430">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af955-431">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-432">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="af955-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af955-433">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-434">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af955-435">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="af955-436">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-436">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-437">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="af955-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="af955-438">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-439">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af955-440">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="af955-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="af955-441">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="af955-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af955-442">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="af955-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af955-443">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-443">Default: `1`</span></span><br><span data-ttu-id="af955-444">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-444">Min: `1`</span></span><br><span data-ttu-id="af955-445">Максимум: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="af955-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="af955-446">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-446">Required string attribute.</span></span></p><p><span data-ttu-id="af955-447">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af955-448">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-448">Relative paths are supported.</span></span> <span data-ttu-id="af955-449">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af955-450">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-451">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af955-452">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="af955-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="af955-453">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="af955-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="af955-454">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-454">Default: `10`</span></span><br><span data-ttu-id="af955-455">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-455">Min: `0`</span></span><br><span data-ttu-id="af955-456">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="af955-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af955-457">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="af955-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af955-458">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af955-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af955-459">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="af955-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="af955-460">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="af955-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="af955-461">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="af955-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="af955-462">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="af955-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="af955-463">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="af955-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="af955-464">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af955-464">Default: `00:02:00`</span></span><br><span data-ttu-id="af955-465">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-465">Min: `00:00:00`</span></span><br><span data-ttu-id="af955-466">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af955-467">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-468">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af955-469">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-469">Default: `10`</span></span><br><span data-ttu-id="af955-470">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-470">Min: `0`</span></span><br><span data-ttu-id="af955-471">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="af955-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af955-472">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-473">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="af955-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af955-474">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af955-475">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="af955-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af955-476">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="af955-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af955-477">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="af955-477">Default: `120`</span></span><br><span data-ttu-id="af955-478">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-478">Min: `0`</span></span><br><span data-ttu-id="af955-479">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="af955-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af955-480">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-481">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af955-482">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-482">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-483">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af955-484">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af955-485">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af955-486">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-487">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af955-488">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="af955-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="af955-489">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="af955-489">Setting environment variables</span></span>

<span data-ttu-id="af955-490">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="af955-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af955-491">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="af955-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="af955-492">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="af955-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="af955-493">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="af955-493">The following example sets two environment variables.</span></span> <span data-ttu-id="af955-494">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="af955-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af955-495">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af955-496">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af955-497">Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="af955-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="af955-498">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="af955-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="af955-499">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="af955-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af955-500">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af955-500">app_offline.htm</span></span>

<span data-ttu-id="af955-501">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af955-502">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af955-503">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af955-504">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="af955-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="af955-505">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="af955-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="af955-506">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af955-507">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="af955-507">Start-up error page</span></span>

<span data-ttu-id="af955-508">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="af955-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="af955-509">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="af955-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="af955-510">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="af955-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="af955-511">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="af955-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="af955-512">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="af955-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af955-513">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af955-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af955-514">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="af955-514">Log creation and redirection</span></span>

<span data-ttu-id="af955-515">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="af955-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af955-516">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-517">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="af955-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af955-518">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="af955-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af955-519">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="af955-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af955-520">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af955-521">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af955-522">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="af955-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af955-523">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="af955-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af955-524">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="af955-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af955-525">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="af955-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af955-526">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af955-527">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="af955-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="af955-528">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="af955-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="af955-529">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="af955-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af955-530">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="af955-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="af955-531">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-532">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="af955-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af955-533">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af955-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="af955-534">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="af955-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="af955-535">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="af955-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="af955-536">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="af955-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="af955-537">Папки, указанные в пути к значению `<handlerSetting>` (*logs* в приведенном выше примере), не создаются модулем автоматически и должны заранее существовать в развертывании.</span><span class="sxs-lookup"><span data-stu-id="af955-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="af955-538">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="af955-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af955-539">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="af955-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="af955-540">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="af955-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="af955-541">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="af955-541">ERROR</span></span>
* <span data-ttu-id="af955-542">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="af955-542">WARNING</span></span>
* <span data-ttu-id="af955-543">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="af955-543">INFO</span></span>
* <span data-ttu-id="af955-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="af955-544">TRACE</span></span>

<span data-ttu-id="af955-545">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="af955-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="af955-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="af955-546">CONSOLE</span></span>
* <span data-ttu-id="af955-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="af955-547">EVENTLOG</span></span>
* <span data-ttu-id="af955-548">FILE</span><span class="sxs-lookup"><span data-stu-id="af955-548">FILE</span></span>

<span data-ttu-id="af955-549">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="af955-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="af955-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="af955-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="af955-551">(По умолчанию: *aspnetcore debug.log*)</span><span class="sxs-lookup"><span data-stu-id="af955-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="af955-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="af955-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="af955-553">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="af955-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="af955-554">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="af955-554">The size of the log isn't limited.</span></span> <span data-ttu-id="af955-555">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="af955-556">См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="af955-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af955-557">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="af955-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af955-558">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="af955-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="af955-559">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="af955-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af955-560">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af955-561">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="af955-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af955-562">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="af955-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af955-563">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="af955-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af955-564">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="af955-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af955-565">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="af955-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af955-566">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af955-567">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af955-568">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="af955-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af955-569">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="af955-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af955-570">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="af955-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af955-571">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="af955-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="af955-572">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="af955-573">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af955-574">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="af955-574">Run the installer.</span></span>
1. <span data-ttu-id="af955-575">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="af955-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af955-576">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af955-577">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="af955-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af955-578">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af955-579">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af955-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af955-580">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="af955-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af955-581">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="af955-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af955-582">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="af955-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af955-583">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af955-584">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="af955-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af955-585">Module</span><span class="sxs-lookup"><span data-stu-id="af955-585">Module</span></span>

<span data-ttu-id="af955-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af955-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af955-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="af955-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af955-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="af955-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="af955-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af955-596">Схема</span><span class="sxs-lookup"><span data-stu-id="af955-596">Schema</span></span>

<span data-ttu-id="af955-597">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-597">**IIS**</span></span>

* <span data-ttu-id="af955-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af955-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af955-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="af955-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-600">**IIS Express**</span></span>

* <span data-ttu-id="af955-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="af955-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="af955-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af955-603">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="af955-603">Configuration</span></span>

<span data-ttu-id="af955-604">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-604">**IIS**</span></span>

* <span data-ttu-id="af955-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af955-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-606">**IIS Express**</span></span>

* <span data-ttu-id="af955-607">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af955-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af955-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af955-609">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="af955-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="af955-610">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для переадресации веб-запросов в серверные приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="af955-611">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="af955-611">Supported Windows versions:</span></span>

* <span data-ttu-id="af955-612">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-612">Windows 7 or later</span></span>
* <span data-ttu-id="af955-613">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="af955-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="af955-614">Модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af955-614">The module only works with Kestrel.</span></span> <span data-ttu-id="af955-615">Модуль несовместим с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="af955-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="af955-616">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль также обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="af955-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="af955-617">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое.</span><span class="sxs-lookup"><span data-stu-id="af955-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="af955-618">Это, по сути, совпадает с поведением приложений ASP.NET 4.x, выполняемых внутрипроцессно в IIS и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="af955-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="af955-619">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением:</span><span class="sxs-lookup"><span data-stu-id="af955-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Модуль ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="af955-621">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="af955-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="af955-622">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="af955-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="af955-623">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="af955-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="af955-624">Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="af955-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="af955-625">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="af955-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="af955-626">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="af955-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="af955-627">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="af955-628">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="af955-629">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="af955-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="af955-630">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="af955-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="af955-631">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="af955-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="af955-632">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="af955-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="af955-633">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="af955-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="af955-634">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="af955-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="af955-635">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="af955-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="af955-636">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="af955-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="af955-637">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af955-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="af955-638">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="af955-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="af955-639">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="af955-639">Configuration with web.config</span></span>

<span data-ttu-id="af955-640">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="af955-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="af955-641">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="af955-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="af955-642">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="af955-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="af955-643">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-644">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="af955-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="af955-645">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="af955-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="af955-646">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="af955-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="af955-647">Атрибут</span><span class="sxs-lookup"><span data-stu-id="af955-647">Attribute</span></span> | <span data-ttu-id="af955-648">Описание</span><span class="sxs-lookup"><span data-stu-id="af955-648">Description</span></span> | <span data-ttu-id="af955-649">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="af955-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="af955-650">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-650">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-651">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="af955-652">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-653">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="af955-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="af955-654">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-655">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="af955-656">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af955-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="af955-657">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-658">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="af955-659">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="af955-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="af955-660">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="af955-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="af955-661">По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-661">Default: `1`</span></span><br><span data-ttu-id="af955-662">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="af955-662">Min: `1`</span></span><br><span data-ttu-id="af955-663">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="af955-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="af955-664">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-664">Required string attribute.</span></span></p><p><span data-ttu-id="af955-665">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="af955-666">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-666">Relative paths are supported.</span></span> <span data-ttu-id="af955-667">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="af955-668">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-669">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="af955-670">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="af955-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="af955-671">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-671">Default: `10`</span></span><br><span data-ttu-id="af955-672">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-672">Min: `0`</span></span><br><span data-ttu-id="af955-673">Максимум: `100`</span><span class="sxs-lookup"><span data-stu-id="af955-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="af955-674">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="af955-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="af955-675">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="af955-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="af955-676">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="af955-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="af955-677">По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="af955-677">Default: `00:02:00`</span></span><br><span data-ttu-id="af955-678">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-678">Min: `00:00:00`</span></span><br><span data-ttu-id="af955-679">Максимум: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="af955-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="af955-680">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-681">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="af955-682">По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="af955-682">Default: `10`</span></span><br><span data-ttu-id="af955-683">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-683">Min: `0`</span></span><br><span data-ttu-id="af955-684">Максимум: `600`</span><span class="sxs-lookup"><span data-stu-id="af955-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="af955-685">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="af955-686">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="af955-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="af955-687">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="af955-688">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="af955-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="af955-689">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="af955-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="af955-690">По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="af955-690">Default: `120`</span></span><br><span data-ttu-id="af955-691">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="af955-691">Min: `0`</span></span><br><span data-ttu-id="af955-692">Максимум: `3600`</span><span class="sxs-lookup"><span data-stu-id="af955-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="af955-693">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="af955-694">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="af955-695">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="af955-695">Optional string attribute.</span></span></p><p><span data-ttu-id="af955-696">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="af955-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="af955-697">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="af955-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="af955-698">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="af955-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="af955-699">Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="af955-700">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="af955-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="af955-701">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="af955-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="af955-702">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="af955-702">Setting environment variables</span></span>

<span data-ttu-id="af955-703">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="af955-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="af955-704">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="af955-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="af955-705">Переменные среды, заданные в этом разделе, конфликтуют с переменными системной среды с такими же именами.</span><span class="sxs-lookup"><span data-stu-id="af955-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="af955-706">Если переменная среды задана и в файле *web.config*, и на уровне системы в Windows, значение из файла *web.config* добавляется к значению переменной системной среды (например, `ASPNETCORE_ENVIRONMENT: Development;Development`), которая препятствует запуску приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="af955-707">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="af955-707">The following example sets two environment variables.</span></span> <span data-ttu-id="af955-708">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="af955-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="af955-709">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="af955-710">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="af955-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="af955-711">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="af955-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="af955-712">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="af955-712">app_offline.htm</span></span>

<span data-ttu-id="af955-713">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="af955-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="af955-714">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="af955-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="af955-715">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="af955-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="af955-716">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="af955-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="af955-717">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="af955-717">Start-up error page</span></span>

<span data-ttu-id="af955-718">Если модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="af955-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="af955-719">Чтобы подавить эту страницу и вернуться к странице IIS кода состояния 502 по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="af955-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="af955-720">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="af955-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Страница кода состояния "502.5 — ошибка процесса"](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="af955-722">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="af955-722">Log creation and redirection</span></span>

<span data-ttu-id="af955-723">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="af955-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="af955-724">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="af955-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="af955-725">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="af955-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="af955-726">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="af955-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="af955-727">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="af955-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="af955-728">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="af955-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="af955-729">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="af955-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="af955-730">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="af955-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="af955-731">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="af955-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="af955-732">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="af955-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="af955-733">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="af955-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="af955-734">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="af955-735">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="af955-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="af955-736">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="af955-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="af955-737">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="af955-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="af955-738">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="af955-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="af955-739">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="af955-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="af955-740">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="af955-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="af955-741">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="af955-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="af955-742">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="af955-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="af955-743">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="af955-744">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="af955-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="af955-745">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="af955-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="af955-746">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="af955-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="af955-747">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="af955-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="af955-748">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="af955-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="af955-749">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="af955-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="af955-750">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="af955-751">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="af955-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="af955-752">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="af955-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="af955-753">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="af955-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="af955-754">При использовании общей конфигурации IIS выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="af955-755">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="af955-756">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="af955-756">Run the installer.</span></span>
1. <span data-ttu-id="af955-757">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="af955-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="af955-758">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="af955-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="af955-759">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="af955-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="af955-760">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="af955-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="af955-761">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="af955-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="af955-762">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="af955-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="af955-763">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="af955-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="af955-764">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="af955-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="af955-765">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="af955-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="af955-766">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="af955-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="af955-767">Module</span><span class="sxs-lookup"><span data-stu-id="af955-767">Module</span></span>

<span data-ttu-id="af955-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="af955-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="af955-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="af955-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="af955-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="af955-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="af955-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="af955-774">Схема</span><span class="sxs-lookup"><span data-stu-id="af955-774">Schema</span></span>

<span data-ttu-id="af955-775">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-775">**IIS**</span></span>

* <span data-ttu-id="af955-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="af955-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-777">**IIS Express**</span></span>

* <span data-ttu-id="af955-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="af955-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="af955-779">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="af955-779">Configuration</span></span>

<span data-ttu-id="af955-780">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="af955-780">**IIS**</span></span>

* <span data-ttu-id="af955-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="af955-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="af955-782">**IIS Express**</span></span>

* <span data-ttu-id="af955-783">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="af955-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="af955-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="af955-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="af955-785">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="af955-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="af955-786">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="af955-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="af955-787">[Справочные материалы по модулю ASP.NET Core (главная ветвь)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2). Используйте раскрывающийся список **Branch** (Ветвь), чтобы выбрать конкретный выпуск (например, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="af955-787">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
