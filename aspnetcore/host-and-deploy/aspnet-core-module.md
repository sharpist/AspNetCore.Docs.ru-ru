---
<span data-ttu-id="f304b-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-102">'Blazor'</span></span>
- <span data-ttu-id="f304b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-103">'Identity'</span></span>
- <span data-ttu-id="f304b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-105">'Razor'</span></span>
- <span data-ttu-id="f304b-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="f304b-107">Модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f304b-107">ASP.NET Core Module</span></span>

<span data-ttu-id="f304b-108">Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Рик Штраль](https://github.com/RickStrahl) (Rick Strahl), [Крис Росс](https://github.com/Tratcher) (Chris Ross), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Сураб Ширхатти](https://twitter.com/sshirhatti) (Sourabh Shirhatti) и [Джастин Коталик](https://github.com/jkotalik) (Justin Kotalik)</span><span class="sxs-lookup"><span data-stu-id="f304b-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f304b-109">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="f304b-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f304b-110">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f304b-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f304b-111">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f304b-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f304b-112">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="f304b-112">Supported Windows versions:</span></span>

* <span data-ttu-id="f304b-113">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-113">Windows 7 or later</span></span>
* <span data-ttu-id="f304b-114">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f304b-115">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f304b-116">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f304b-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f304b-117">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f304b-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f304b-118">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f304b-119">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-119">In-process hosting model</span></span>

<span data-ttu-id="f304b-120">По умолчанию приложения ASP.NET Core используют модель внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="f304b-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="f304b-121">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="f304b-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f304b-122">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f304b-123">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="f304b-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f304b-124">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f304b-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f304b-125">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f304b-126">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="f304b-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f304b-127">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="f304b-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f304b-128">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="f304b-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f304b-129">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-129">Use one app pool per app.</span></span>

* <span data-ttu-id="f304b-130">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="f304b-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f304b-131">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f304b-132">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f304b-133">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="f304b-133">Client disconnects are detected.</span></span> <span data-ttu-id="f304b-134">При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).</span><span class="sxs-lookup"><span data-stu-id="f304b-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f304b-135">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="f304b-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f304b-136">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="f304b-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f304b-137">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="f304b-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f304b-138">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f304b-139">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="f304b-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f304b-140">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f304b-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f304b-141">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="f304b-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="f304b-142">[Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.</span><span class="sxs-lookup"><span data-stu-id="f304b-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f304b-143">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="f304b-143">Out-of-process hosting model</span></span>

<span data-ttu-id="f304b-144">Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (*CSPROJ*):</span><span class="sxs-lookup"><span data-stu-id="f304b-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f304b-145">Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f304b-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="f304b-146">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="f304b-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f304b-147">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f304b-148">Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="f304b-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f304b-149">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f304b-150">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="f304b-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f304b-151">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-151">Hosting model changes</span></span>

<span data-ttu-id="f304b-152">Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f304b-153">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f304b-154">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f304b-155">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="f304b-155">Process name</span></span>

<span data-ttu-id="f304b-156">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="f304b-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f304b-157">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="f304b-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f304b-158">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f304b-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f304b-159">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f304b-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f304b-160">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="f304b-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f304b-161">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f304b-162">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="f304b-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f304b-163">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f304b-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f304b-164">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f304b-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f304b-165">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="f304b-165">Configuration with web.config</span></span>

<span data-ttu-id="f304b-166">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="f304b-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f304b-167">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="f304b-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f304b-168">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f304b-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f304b-169">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f304b-170">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-171">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="f304b-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f304b-172">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f304b-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f304b-173">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f304b-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f304b-174">Атрибут</span><span class="sxs-lookup"><span data-stu-id="f304b-174">Attribute</span></span> | <span data-ttu-id="f304b-175">Описание</span><span class="sxs-lookup"><span data-stu-id="f304b-175">Description</span></span> | <span data-ttu-id="f304b-176">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f304b-176">Default</span></span> |
| ---
<span data-ttu-id="f304b-177">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-178">'Blazor'</span></span>
- <span data-ttu-id="f304b-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-179">'Identity'</span></span>
- <span data-ttu-id="f304b-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-181">'Razor'</span></span>
- <span data-ttu-id="f304b-182">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-183">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-184">'Blazor'</span></span>
- <span data-ttu-id="f304b-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-185">'Identity'</span></span>
- <span data-ttu-id="f304b-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-187">'Razor'</span></span>
- <span data-ttu-id="f304b-188">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-188">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-189">----- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-190">'Blazor'</span></span>
- <span data-ttu-id="f304b-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-191">'Identity'</span></span>
- <span data-ttu-id="f304b-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-193">'Razor'</span></span>
- <span data-ttu-id="f304b-194">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-195">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-196">'Blazor'</span></span>
- <span data-ttu-id="f304b-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-197">'Identity'</span></span>
- <span data-ttu-id="f304b-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-199">'Razor'</span></span>
- <span data-ttu-id="f304b-200">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-201">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-202">'Blazor'</span></span>
- <span data-ttu-id="f304b-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-203">'Identity'</span></span>
- <span data-ttu-id="f304b-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-205">'Razor'</span></span>
- <span data-ttu-id="f304b-206">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-206">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="f304b-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="f304b-208">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-208">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-209">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="f304b-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="f304b-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="f304b-211">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-212">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="f304b-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="f304b-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="f304b-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="f304b-214">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-215">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f304b-216">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="f304b-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="f304b-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="f304b-218">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-218">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-219">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="f304b-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="f304b-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="f304b-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="f304b-221">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-222">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f304b-223">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="f304b-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f304b-224">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="f304b-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f304b-225">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="f304b-226">| По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-226">| Default: `1`</span></span><br><span data-ttu-id="f304b-227">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-227">Min: `1`</span></span><br><span data-ttu-id="f304b-228">Максимум: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="f304b-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="f304b-229">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-229">Required string attribute.</span></span></p><p><span data-ttu-id="f304b-230">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f304b-231">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-231">Relative paths are supported.</span></span> <span data-ttu-id="f304b-232">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="f304b-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="f304b-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="f304b-234">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-235">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f304b-236">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="f304b-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f304b-237">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="f304b-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="f304b-238">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-238">| Default: `10`</span></span><br><span data-ttu-id="f304b-239">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-239">Min: `0`</span></span><br><span data-ttu-id="f304b-240">Максимум: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="f304b-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="f304b-241">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="f304b-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f304b-242">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f304b-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f304b-243">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="f304b-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f304b-244">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="f304b-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f304b-245">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="f304b-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f304b-246">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="f304b-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f304b-247">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="f304b-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="f304b-248">| По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="f304b-249">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-249">Min: `00:00:00`</span></span><br><span data-ttu-id="f304b-250">Максимум: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="f304b-251">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-252">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="f304b-253">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-253">| Default: `10`</span></span><br><span data-ttu-id="f304b-254">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-254">Min: `0`</span></span><br><span data-ttu-id="f304b-255">Максимум: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="f304b-256">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-257">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f304b-258">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f304b-259">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="f304b-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f304b-260">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="f304b-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="f304b-261">| По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="f304b-261">| Default: `120`</span></span><br><span data-ttu-id="f304b-262">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-262">Min: `0`</span></span><br><span data-ttu-id="f304b-263">Максимум: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="f304b-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="f304b-264">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-265">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="f304b-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="f304b-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="f304b-267">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-267">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-268">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f304b-269">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f304b-270">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f304b-271">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-272">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f304b-273">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="f304b-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="f304b-274">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="f304b-274">Set environment variables</span></span>

<span data-ttu-id="f304b-275">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f304b-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f304b-276">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f304b-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f304b-277">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f304b-278">В следующем примере устанавливаются две переменные среды в *web.config*. `ASPNETCORE_ENVIRONMENT` настраивает в качестве среды приложения `Development`.</span><span class="sxs-lookup"><span data-stu-id="f304b-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f304b-279">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f304b-280">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="f304b-281">Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="f304b-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="f304b-282">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="f304b-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f304b-283">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="f304b-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f304b-284">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f304b-284">app_offline.htm</span></span>

<span data-ttu-id="f304b-285">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f304b-286">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f304b-287">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f304b-288">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="f304b-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f304b-289">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="f304b-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f304b-290">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f304b-291">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="f304b-291">Start-up error page</span></span>

<span data-ttu-id="f304b-292">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="f304b-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f304b-293">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="f304b-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-294">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="f304b-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-295">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="f304b-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-296">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f304b-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f304b-297">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f304b-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f304b-298">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="f304b-298">Log creation and redirection</span></span>

<span data-ttu-id="f304b-299">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f304b-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f304b-300">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-301">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="f304b-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f304b-302">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="f304b-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f304b-303">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="f304b-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f304b-304">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f304b-305">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f304b-306">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="f304b-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f304b-307">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f304b-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f304b-308">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f304b-309">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="f304b-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f304b-310">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f304b-311">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="f304b-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f304b-312">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="f304b-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f304b-313">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="f304b-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f304b-314">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f304b-315">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-316">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f304b-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f304b-317">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f304b-318">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="f304b-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f304b-319">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="f304b-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f304b-320">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="f304b-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f304b-321">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="f304b-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f304b-322">Все папки, указанные в пути (*logs* в приведенном выше примере), создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-323">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="f304b-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f304b-324">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="f304b-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f304b-325">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="f304b-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f304b-326">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="f304b-326">ERROR</span></span>
* <span data-ttu-id="f304b-327">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="f304b-327">WARNING</span></span>
* <span data-ttu-id="f304b-328">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="f304b-328">INFO</span></span>
* <span data-ttu-id="f304b-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="f304b-329">TRACE</span></span>

<span data-ttu-id="f304b-330">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="f304b-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f304b-331">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f304b-331">CONSOLE</span></span>
* <span data-ttu-id="f304b-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f304b-332">EVENTLOG</span></span>
* <span data-ttu-id="f304b-333">FILE</span><span class="sxs-lookup"><span data-stu-id="f304b-333">FILE</span></span>

<span data-ttu-id="f304b-334">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="f304b-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f304b-335">`ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="f304b-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f304b-336">(По умолчанию: *aspnetcore debug.log*)</span><span class="sxs-lookup"><span data-stu-id="f304b-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f304b-337">`ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="f304b-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f304b-338">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="f304b-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f304b-339">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="f304b-339">The size of the log isn't limited.</span></span> <span data-ttu-id="f304b-340">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f304b-341">См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="f304b-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="f304b-342">Изменение размера стека</span><span class="sxs-lookup"><span data-stu-id="f304b-342">Modify the stack size</span></span>

<span data-ttu-id="f304b-343">*Применяется только при использовании модели внутрипроцессного размещения.*</span><span class="sxs-lookup"><span data-stu-id="f304b-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="f304b-344">Настройте размер управляемого стека, задав для параметра `stackSize` значение в байтах в файле *web.config*. Размер по умолчанию — `1048576` байт (1 МБ).</span><span class="sxs-lookup"><span data-stu-id="f304b-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f304b-345">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="f304b-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f304b-346">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="f304b-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f304b-347">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="f304b-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f304b-348">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f304b-349">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="f304b-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f304b-350">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="f304b-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f304b-351">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="f304b-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f304b-352">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f304b-353">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="f304b-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f304b-354">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f304b-355">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f304b-356">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="f304b-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f304b-357">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="f304b-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f304b-358">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="f304b-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f304b-359">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="f304b-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f304b-360">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f304b-361">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f304b-362">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="f304b-362">Run the installer.</span></span>
1. <span data-ttu-id="f304b-363">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="f304b-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f304b-364">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f304b-365">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="f304b-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f304b-366">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f304b-367">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f304b-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f304b-368">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="f304b-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f304b-369">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="f304b-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f304b-370">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="f304b-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f304b-371">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f304b-372">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="f304b-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f304b-373">Module</span><span class="sxs-lookup"><span data-stu-id="f304b-373">Module</span></span>

<span data-ttu-id="f304b-374">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f304b-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f304b-379">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f304b-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f304b-384">Схема</span><span class="sxs-lookup"><span data-stu-id="f304b-384">Schema</span></span>

<span data-ttu-id="f304b-385">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-385">**IIS**</span></span>

* <span data-ttu-id="f304b-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f304b-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f304b-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-388">**IIS Express**</span></span>

* <span data-ttu-id="f304b-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f304b-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f304b-391">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="f304b-391">Configuration</span></span>

<span data-ttu-id="f304b-392">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-392">**IIS**</span></span>

* <span data-ttu-id="f304b-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f304b-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-394">**IIS Express**</span></span>

* <span data-ttu-id="f304b-395">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f304b-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f304b-397">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="f304b-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f304b-398">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:</span><span class="sxs-lookup"><span data-stu-id="f304b-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f304b-399">Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f304b-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f304b-400">Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f304b-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f304b-401">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="f304b-401">Supported Windows versions:</span></span>

* <span data-ttu-id="f304b-402">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-402">Windows 7 or later</span></span>
* <span data-ttu-id="f304b-403">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f304b-404">При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f304b-405">При размещении вне процесса модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f304b-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f304b-406">Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f304b-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f304b-407">Модели размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f304b-408">Модель внутрипроцессного размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-408">In-process hosting model</span></span>

<span data-ttu-id="f304b-409">Чтобы настроить приложение для внутрипроцессного размещения, добавьте свойство `<AspNetCoreHostingModel>` к файлу проекта приложения со значением `InProcess` (размещение вне процесса имеет значение `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="f304b-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f304b-410">Модель внутрипроцессного размещения не поддерживается для приложений ASP.NET Core, предназначенных для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f304b-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f304b-411">Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="f304b-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f304b-412">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f304b-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="f304b-413">При внутрипроцессном размещении применимы следующие характеристики:</span><span class="sxs-lookup"><span data-stu-id="f304b-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f304b-414">Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f304b-415">Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="f304b-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f304b-416">Регистрация `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f304b-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f304b-417">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f304b-418">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="f304b-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f304b-419">[Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="f304b-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f304b-420">Совместное использование пула приложений среди приложений не поддерживается.</span><span class="sxs-lookup"><span data-stu-id="f304b-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f304b-421">Используйте один пул приложений для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-421">Use one app pool per app.</span></span>

* <span data-ttu-id="f304b-422">При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="f304b-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f304b-423">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f304b-424">Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f304b-425">Обнаружены отключения клиентов.</span><span class="sxs-lookup"><span data-stu-id="f304b-425">Client disconnects are detected.</span></span> <span data-ttu-id="f304b-426">При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).</span><span class="sxs-lookup"><span data-stu-id="f304b-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f304b-427">В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="f304b-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f304b-428">Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="f304b-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f304b-429">Вызовите метод `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="f304b-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f304b-430">Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="f304b-431">При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя.</span><span class="sxs-lookup"><span data-stu-id="f304b-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="f304b-432">Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f304b-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="f304b-433">При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="f304b-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f304b-434">Модель размещения вне процесса</span><span class="sxs-lookup"><span data-stu-id="f304b-434">Out-of-process hosting model</span></span>

<span data-ttu-id="f304b-435">Чтобы настроить приложение для размещения вне процесса, используйте один из следующих подходов в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="f304b-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="f304b-436">Не указывайте свойство `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="f304b-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="f304b-437">Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f304b-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="f304b-438">Установите для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` (внутрипроцессное размещение имеет значение `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="f304b-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f304b-439">Это значение не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.</span><span class="sxs-lookup"><span data-stu-id="f304b-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="f304b-440">Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f304b-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f304b-441">Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:</span><span class="sxs-lookup"><span data-stu-id="f304b-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f304b-442">Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f304b-443">Настройка перехвата ошибок запуска на узле.</span><span class="sxs-lookup"><span data-stu-id="f304b-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f304b-444">Изменения модели размещения</span><span class="sxs-lookup"><span data-stu-id="f304b-444">Hosting model changes</span></span>

<span data-ttu-id="f304b-445">Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f304b-446">Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f304b-447">Следующий запрос для приложения порождает новый процесс IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f304b-448">Имя процесса</span><span class="sxs-lookup"><span data-stu-id="f304b-448">Process name</span></span>

<span data-ttu-id="f304b-449">`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).</span><span class="sxs-lookup"><span data-stu-id="f304b-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="f304b-450">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="f304b-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f304b-451">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f304b-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f304b-452">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f304b-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f304b-453">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="f304b-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f304b-454">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f304b-455">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="f304b-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f304b-456">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f304b-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f304b-457">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f304b-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f304b-458">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="f304b-458">Configuration with web.config</span></span>

<span data-ttu-id="f304b-459">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="f304b-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f304b-460">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="f304b-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f304b-461">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f304b-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f304b-462">Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="f304b-463">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-464">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="f304b-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f304b-465">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f304b-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f304b-466">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f304b-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f304b-467">Атрибут</span><span class="sxs-lookup"><span data-stu-id="f304b-467">Attribute</span></span> | <span data-ttu-id="f304b-468">Описание</span><span class="sxs-lookup"><span data-stu-id="f304b-468">Description</span></span> | <span data-ttu-id="f304b-469">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f304b-469">Default</span></span> |
| ---
<span data-ttu-id="f304b-470">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-471">'Blazor'</span></span>
- <span data-ttu-id="f304b-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-472">'Identity'</span></span>
- <span data-ttu-id="f304b-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-474">'Razor'</span></span>
- <span data-ttu-id="f304b-475">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-476">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-477">'Blazor'</span></span>
- <span data-ttu-id="f304b-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-478">'Identity'</span></span>
- <span data-ttu-id="f304b-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-480">'Razor'</span></span>
- <span data-ttu-id="f304b-481">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-481">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-482">----- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-483">'Blazor'</span></span>
- <span data-ttu-id="f304b-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-484">'Identity'</span></span>
- <span data-ttu-id="f304b-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-486">'Razor'</span></span>
- <span data-ttu-id="f304b-487">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-488">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-489">'Blazor'</span></span>
- <span data-ttu-id="f304b-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-490">'Identity'</span></span>
- <span data-ttu-id="f304b-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-492">'Razor'</span></span>
- <span data-ttu-id="f304b-493">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-494">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-495">'Blazor'</span></span>
- <span data-ttu-id="f304b-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-496">'Identity'</span></span>
- <span data-ttu-id="f304b-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-498">'Razor'</span></span>
- <span data-ttu-id="f304b-499">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-499">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="f304b-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="f304b-501">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-501">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-502">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="f304b-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="f304b-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="f304b-504">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-505">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="f304b-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="f304b-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="f304b-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="f304b-507">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-508">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f304b-509">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="f304b-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="f304b-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="f304b-511">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-511">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-512">Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="f304b-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="f304b-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="f304b-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="f304b-514">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-515">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f304b-516">&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</span><span class="sxs-lookup"><span data-stu-id="f304b-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="f304b-517">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="f304b-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f304b-518">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="f304b-519">| По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-519">| Default: `1`</span></span><br><span data-ttu-id="f304b-520">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-520">Min: `1`</span></span><br><span data-ttu-id="f304b-521">Максимум: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="f304b-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="f304b-522">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-522">Required string attribute.</span></span></p><p><span data-ttu-id="f304b-523">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f304b-524">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-524">Relative paths are supported.</span></span> <span data-ttu-id="f304b-525">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="f304b-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="f304b-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="f304b-527">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-528">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f304b-529">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="f304b-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f304b-530">Не поддерживается для внутрипроцессного размещения.</span><span class="sxs-lookup"><span data-stu-id="f304b-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="f304b-531">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-531">| Default: `10`</span></span><br><span data-ttu-id="f304b-532">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-532">Min: `0`</span></span><br><span data-ttu-id="f304b-533">Максимум: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="f304b-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="f304b-534">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="f304b-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f304b-535">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f304b-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f304b-536">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="f304b-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f304b-537">Не применяется к внутрипроцессному размещению.</span><span class="sxs-lookup"><span data-stu-id="f304b-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f304b-538">Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</span><span class="sxs-lookup"><span data-stu-id="f304b-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="f304b-539">Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59.</span><span class="sxs-lookup"><span data-stu-id="f304b-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="f304b-540">Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</span><span class="sxs-lookup"><span data-stu-id="f304b-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="f304b-541">| По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="f304b-542">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-542">Min: `00:00:00`</span></span><br><span data-ttu-id="f304b-543">Максимум: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="f304b-544">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-545">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="f304b-546">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-546">| Default: `10`</span></span><br><span data-ttu-id="f304b-547">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-547">Min: `0`</span></span><br><span data-ttu-id="f304b-548">Максимум: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="f304b-549">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-550">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f304b-551">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f304b-552">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="f304b-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f304b-553">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="f304b-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="f304b-554">| По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="f304b-554">| Default: `120`</span></span><br><span data-ttu-id="f304b-555">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-555">Min: `0`</span></span><br><span data-ttu-id="f304b-556">Максимум: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="f304b-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="f304b-557">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-558">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="f304b-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="f304b-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="f304b-560">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-560">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-561">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f304b-562">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f304b-563">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f304b-564">Все папки, указанные в пути, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-565">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f304b-566">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="f304b-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f304b-567">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="f304b-567">Setting environment variables</span></span>

<span data-ttu-id="f304b-568">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f304b-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f304b-569">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f304b-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f304b-570">Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="f304b-571">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-571">The following example sets two environment variables.</span></span> <span data-ttu-id="f304b-572">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="f304b-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f304b-573">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f304b-574">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="f304b-575">Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта.</span><span class="sxs-lookup"><span data-stu-id="f304b-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="f304b-576">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="f304b-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="f304b-577">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="f304b-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f304b-578">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f304b-578">app_offline.htm</span></span>

<span data-ttu-id="f304b-579">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f304b-580">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f304b-581">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f304b-582">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="f304b-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="f304b-583">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="f304b-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f304b-584">Например, подключение websocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f304b-585">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="f304b-585">Start-up error page</span></span>

<span data-ttu-id="f304b-586">Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.</span><span class="sxs-lookup"><span data-stu-id="f304b-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f304b-587">Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.</span><span class="sxs-lookup"><span data-stu-id="f304b-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-588">Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.</span><span class="sxs-lookup"><span data-stu-id="f304b-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-589">Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="f304b-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f304b-590">Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f304b-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f304b-591">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f304b-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f304b-592">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="f304b-592">Log creation and redirection</span></span>

<span data-ttu-id="f304b-593">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f304b-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f304b-594">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-595">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="f304b-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f304b-596">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="f304b-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f304b-597">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="f304b-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f304b-598">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f304b-599">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f304b-600">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="f304b-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f304b-601">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f304b-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f304b-602">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f304b-603">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="f304b-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f304b-604">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f304b-605">Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ).</span><span class="sxs-lookup"><span data-stu-id="f304b-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f304b-606">После запуска все дополнительные журналы удаляются.</span><span class="sxs-lookup"><span data-stu-id="f304b-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="f304b-607">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="f304b-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f304b-608">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="f304b-609">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-610">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f304b-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f304b-611">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="f304b-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f304b-612">Расширенные журналы диагностики</span><span class="sxs-lookup"><span data-stu-id="f304b-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f304b-613">Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики.</span><span class="sxs-lookup"><span data-stu-id="f304b-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f304b-614">Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:</span><span class="sxs-lookup"><span data-stu-id="f304b-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f304b-615">Папки, указанные в пути к значению `<handlerSetting>` (*logs* в приведенном выше примере), не создаются модулем автоматически и должны заранее существовать в развертывании.</span><span class="sxs-lookup"><span data-stu-id="f304b-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="f304b-616">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="f304b-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f304b-617">Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.</span><span class="sxs-lookup"><span data-stu-id="f304b-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f304b-618">Уровни (в порядке возрастания степени детализации):</span><span class="sxs-lookup"><span data-stu-id="f304b-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f304b-619">ОШИБКА</span><span class="sxs-lookup"><span data-stu-id="f304b-619">ERROR</span></span>
* <span data-ttu-id="f304b-620">ПРЕДУПРЕЖДЕНИЕ</span><span class="sxs-lookup"><span data-stu-id="f304b-620">WARNING</span></span>
* <span data-ttu-id="f304b-621">ИНФОРМАЦИЯ</span><span class="sxs-lookup"><span data-stu-id="f304b-621">INFO</span></span>
* <span data-ttu-id="f304b-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="f304b-622">TRACE</span></span>

<span data-ttu-id="f304b-623">Расположения (допускаются несколько расположений):</span><span class="sxs-lookup"><span data-stu-id="f304b-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f304b-624">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="f304b-624">CONSOLE</span></span>
* <span data-ttu-id="f304b-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="f304b-625">EVENTLOG</span></span>
* <span data-ttu-id="f304b-626">FILE</span><span class="sxs-lookup"><span data-stu-id="f304b-626">FILE</span></span>

<span data-ttu-id="f304b-627">Параметры обработчика могут быть указаны с помощью переменных среды:</span><span class="sxs-lookup"><span data-stu-id="f304b-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f304b-628">`ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки.</span><span class="sxs-lookup"><span data-stu-id="f304b-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="f304b-629">(По умолчанию: *aspnetcore debug.log*)</span><span class="sxs-lookup"><span data-stu-id="f304b-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f304b-630">`ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.</span><span class="sxs-lookup"><span data-stu-id="f304b-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f304b-631">**Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы.</span><span class="sxs-lookup"><span data-stu-id="f304b-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f304b-632">Размер журнала не ограничен.</span><span class="sxs-lookup"><span data-stu-id="f304b-632">The size of the log isn't limited.</span></span> <span data-ttu-id="f304b-633">Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="f304b-634">См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).</span><span class="sxs-lookup"><span data-stu-id="f304b-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f304b-635">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="f304b-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f304b-636">*Применяется только к размещению вне процесса.*</span><span class="sxs-lookup"><span data-stu-id="f304b-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="f304b-637">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="f304b-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f304b-638">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f304b-639">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="f304b-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f304b-640">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="f304b-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f304b-641">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="f304b-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f304b-642">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f304b-643">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="f304b-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f304b-644">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f304b-645">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f304b-646">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="f304b-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f304b-647">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="f304b-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f304b-648">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="f304b-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f304b-649">При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:</span><span class="sxs-lookup"><span data-stu-id="f304b-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f304b-650">Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f304b-651">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f304b-652">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="f304b-652">Run the installer.</span></span>
1. <span data-ttu-id="f304b-653">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="f304b-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f304b-654">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f304b-655">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="f304b-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f304b-656">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f304b-657">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f304b-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f304b-658">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="f304b-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f304b-659">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="f304b-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f304b-660">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="f304b-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f304b-661">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f304b-662">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="f304b-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f304b-663">Module</span><span class="sxs-lookup"><span data-stu-id="f304b-663">Module</span></span>

<span data-ttu-id="f304b-664">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f304b-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="f304b-669">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="f304b-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f304b-674">Схема</span><span class="sxs-lookup"><span data-stu-id="f304b-674">Schema</span></span>

<span data-ttu-id="f304b-675">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-675">**IIS**</span></span>

* <span data-ttu-id="f304b-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f304b-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="f304b-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-678">**IIS Express**</span></span>

* <span data-ttu-id="f304b-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="f304b-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f304b-681">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="f304b-681">Configuration</span></span>

<span data-ttu-id="f304b-682">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-682">**IIS**</span></span>

* <span data-ttu-id="f304b-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f304b-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-684">**IIS Express**</span></span>

* <span data-ttu-id="f304b-685">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f304b-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f304b-687">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="f304b-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f304b-688">Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для переадресации веб-запросов в серверные приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f304b-689">Поддерживаемые версии Windows:</span><span class="sxs-lookup"><span data-stu-id="f304b-689">Supported Windows versions:</span></span>

* <span data-ttu-id="f304b-690">Windows 7 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-690">Windows 7 or later</span></span>
* <span data-ttu-id="f304b-691">Windows Server 2008 R2 и более поздние версии</span><span class="sxs-lookup"><span data-stu-id="f304b-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f304b-692">Модуль работает только с Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f304b-692">The module only works with Kestrel.</span></span> <span data-ttu-id="f304b-693">Модуль несовместим с [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f304b-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="f304b-694">Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль также обрабатывает управление процессами.</span><span class="sxs-lookup"><span data-stu-id="f304b-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f304b-695">Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое.</span><span class="sxs-lookup"><span data-stu-id="f304b-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f304b-696">Это, по сути, совпадает с поведением приложений ASP.NET 4.x, выполняемых внутрипроцессно в IIS и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f304b-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f304b-697">На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением:</span><span class="sxs-lookup"><span data-stu-id="f304b-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Модуль ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f304b-699">Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра.</span><span class="sxs-lookup"><span data-stu-id="f304b-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f304b-700">Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f304b-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f304b-701">Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.</span><span class="sxs-lookup"><span data-stu-id="f304b-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f304b-702">Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="f304b-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f304b-703">Выполняются дополнительные проверки, и запросы не из модуля отклоняются.</span><span class="sxs-lookup"><span data-stu-id="f304b-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f304b-704">Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f304b-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f304b-705">После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f304b-706">Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f304b-707">ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f304b-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f304b-708">Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.</span><span class="sxs-lookup"><span data-stu-id="f304b-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f304b-709">Многие собственные модули, такие как проверка подлинности Windows, остаются активными.</span><span class="sxs-lookup"><span data-stu-id="f304b-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f304b-710">Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f304b-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f304b-711">Дополнительные возможности модуля ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f304b-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f304b-712">Задание переменных среды для рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="f304b-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f304b-713">Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f304b-714">Переадресация токенов проверки подлинности Windows.</span><span class="sxs-lookup"><span data-stu-id="f304b-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f304b-715">Как установить и использовать модуль ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f304b-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f304b-716">Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="f304b-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f304b-717">Конфигурация с помощью файла web.config</span><span class="sxs-lookup"><span data-stu-id="f304b-717">Configuration with web.config</span></span>

<span data-ttu-id="f304b-718">Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.</span><span class="sxs-lookup"><span data-stu-id="f304b-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f304b-719">Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.</span><span class="sxs-lookup"><span data-stu-id="f304b-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f304b-720">Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f304b-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f304b-721">Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-722">Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.</span><span class="sxs-lookup"><span data-stu-id="f304b-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f304b-723">Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f304b-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f304b-724">Атрибуты элемента aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f304b-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="f304b-725">Атрибут</span><span class="sxs-lookup"><span data-stu-id="f304b-725">Attribute</span></span> | <span data-ttu-id="f304b-726">Описание</span><span class="sxs-lookup"><span data-stu-id="f304b-726">Description</span></span> | <span data-ttu-id="f304b-727">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f304b-727">Default</span></span> |
| ---
<span data-ttu-id="f304b-728">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-729">'Blazor'</span></span>
- <span data-ttu-id="f304b-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-730">'Identity'</span></span>
- <span data-ttu-id="f304b-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-732">'Razor'</span></span>
- <span data-ttu-id="f304b-733">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-734">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-735">'Blazor'</span></span>
- <span data-ttu-id="f304b-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-736">'Identity'</span></span>
- <span data-ttu-id="f304b-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-738">'Razor'</span></span>
- <span data-ttu-id="f304b-739">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-739">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-740">----- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-741">'Blazor'</span></span>
- <span data-ttu-id="f304b-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-742">'Identity'</span></span>
- <span data-ttu-id="f304b-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-744">'Razor'</span></span>
- <span data-ttu-id="f304b-745">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-746">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-747">'Blazor'</span></span>
- <span data-ttu-id="f304b-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-748">'Identity'</span></span>
- <span data-ttu-id="f304b-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-750">'Razor'</span></span>
- <span data-ttu-id="f304b-751">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="f304b-752">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f304b-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f304b-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f304b-753">'Blazor'</span></span>
- <span data-ttu-id="f304b-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f304b-754">'Identity'</span></span>
- <span data-ttu-id="f304b-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f304b-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="f304b-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f304b-756">'Razor'</span></span>
- <span data-ttu-id="f304b-757">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f304b-757">'SignalR' uid:</span></span> 

<span data-ttu-id="f304b-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="f304b-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="f304b-759">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-759">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-760">Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="f304b-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="f304b-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="f304b-762">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-763">Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</span><span class="sxs-lookup"><span data-stu-id="f304b-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="f304b-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="f304b-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="f304b-765">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-766">Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f304b-767">Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="f304b-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="f304b-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="f304b-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="f304b-769">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-770">Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f304b-771">Параметр `processesPerApplication` не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="f304b-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="f304b-772">Этот атрибут будет удален в будущем выпуске.</span><span class="sxs-lookup"><span data-stu-id="f304b-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="f304b-773">| По умолчанию: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-773">| Default: `1`</span></span><br><span data-ttu-id="f304b-774">Минимум: `1`</span><span class="sxs-lookup"><span data-stu-id="f304b-774">Min: `1`</span></span><br><span data-ttu-id="f304b-775">Максимум: `100` | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="f304b-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="f304b-776">Обязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-776">Required string attribute.</span></span></p><p><span data-ttu-id="f304b-777">Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f304b-778">Поддерживаются относительные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-778">Relative paths are supported.</span></span> <span data-ttu-id="f304b-779">Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="f304b-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="f304b-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="f304b-781">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-782">Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f304b-783">Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</span><span class="sxs-lookup"><span data-stu-id="f304b-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="f304b-784">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-784">| Default: `10`</span></span><br><span data-ttu-id="f304b-785">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-785">Min: `0`</span></span><br><span data-ttu-id="f304b-786">Максимум: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="f304b-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="f304b-787">Необязательный атрибут timespan.</span><span class="sxs-lookup"><span data-stu-id="f304b-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f304b-788">Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="f304b-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f304b-789">В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</span><span class="sxs-lookup"><span data-stu-id="f304b-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="f304b-790">| По умолчанию: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="f304b-791">Минимум: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f304b-791">Min: `00:00:00`</span></span><br><span data-ttu-id="f304b-792">Максимум: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="f304b-793">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-794">Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="f304b-795">| По умолчанию: `10`</span><span class="sxs-lookup"><span data-stu-id="f304b-795">| Default: `10`</span></span><br><span data-ttu-id="f304b-796">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-796">Min: `0`</span></span><br><span data-ttu-id="f304b-797">Максимум: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="f304b-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="f304b-798">Необязательный целочисленный атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="f304b-799">Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f304b-800">Если этот предел превышен, модуль завершает процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f304b-801">Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</span><span class="sxs-lookup"><span data-stu-id="f304b-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f304b-802">Значение 0 (ноль) **не** считается бесконечным временем ожидания.</span><span class="sxs-lookup"><span data-stu-id="f304b-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="f304b-803">| По умолчанию: `120`</span><span class="sxs-lookup"><span data-stu-id="f304b-803">| Default: `120`</span></span><br><span data-ttu-id="f304b-804">Минимум: `0`</span><span class="sxs-lookup"><span data-stu-id="f304b-804">Min: `0`</span></span><br><span data-ttu-id="f304b-805">Максимум: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="f304b-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="f304b-806">Дополнительный логический атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f304b-807">Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="f304b-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="f304b-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="f304b-809">Необязательный строковый атрибут.</span><span class="sxs-lookup"><span data-stu-id="f304b-809">Optional string attribute.</span></span></p><p><span data-ttu-id="f304b-810">Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f304b-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f304b-811">Относительные пути задаются относительно корневого каталога веб-сайта.</span><span class="sxs-lookup"><span data-stu-id="f304b-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f304b-812">Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f304b-813">Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f304b-814">С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f304b-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f304b-815">Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</span><span class="sxs-lookup"><span data-stu-id="f304b-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="f304b-816">Настройка переменных среды</span><span class="sxs-lookup"><span data-stu-id="f304b-816">Setting environment variables</span></span>

<span data-ttu-id="f304b-817">Переменные среды для процесса можно указать в атрибуте `processPath`.</span><span class="sxs-lookup"><span data-stu-id="f304b-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f304b-818">Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="f304b-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="f304b-819">Переменные среды, заданные в этом разделе, конфликтуют с переменными системной среды с такими же именами.</span><span class="sxs-lookup"><span data-stu-id="f304b-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="f304b-820">Если переменная среды задана и в файле *web.config*, и на уровне системы в Windows, значение из файла *web.config* добавляется к значению переменной системной среды (например, `ASPNETCORE_ENVIRONMENT: Development;Development`), которая препятствует запуску приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="f304b-821">В следующем примере устанавливаются две переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-821">The following example sets two environment variables.</span></span> <span data-ttu-id="f304b-822">`ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`.</span><span class="sxs-lookup"><span data-stu-id="f304b-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f304b-823">Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f304b-824">`CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="f304b-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="f304b-825">Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.</span><span class="sxs-lookup"><span data-stu-id="f304b-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="f304b-826">App_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f304b-826">app_offline.htm</span></span>

<span data-ttu-id="f304b-827">Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="f304b-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f304b-828">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="f304b-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f304b-829">Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="f304b-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f304b-830">Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="f304b-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="f304b-831">Страница ошибок запуска</span><span class="sxs-lookup"><span data-stu-id="f304b-831">Start-up error page</span></span>

<span data-ttu-id="f304b-832">Если модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.</span><span class="sxs-lookup"><span data-stu-id="f304b-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="f304b-833">Чтобы подавить эту страницу и вернуться к странице IIS кода состояния 502 по умолчанию, используйте атрибут `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="f304b-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f304b-834">Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f304b-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Страница кода состояния "502.5 — ошибка процесса"](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f304b-836">Создание и перенаправление журнала</span><span class="sxs-lookup"><span data-stu-id="f304b-836">Log creation and redirection</span></span>

<span data-ttu-id="f304b-837">Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="f304b-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f304b-838">Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала.</span><span class="sxs-lookup"><span data-stu-id="f304b-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f304b-839">Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).</span><span class="sxs-lookup"><span data-stu-id="f304b-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f304b-840">Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки.</span><span class="sxs-lookup"><span data-stu-id="f304b-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f304b-841">Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.</span><span class="sxs-lookup"><span data-stu-id="f304b-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f304b-842">Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f304b-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="f304b-843">Не используйте журнал stdout для ведения общего журнала приложений.</span><span class="sxs-lookup"><span data-stu-id="f304b-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f304b-844">Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов.</span><span class="sxs-lookup"><span data-stu-id="f304b-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f304b-845">Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f304b-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f304b-846">При создании файла журнала автоматически добавляются отметка времени и расширение файла.</span><span class="sxs-lookup"><span data-stu-id="f304b-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f304b-847">Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей.</span><span class="sxs-lookup"><span data-stu-id="f304b-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f304b-848">Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="f304b-849">В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="f304b-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="f304b-850">Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="f304b-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="f304b-851">При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f304b-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f304b-852">Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="f304b-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="f304b-853">Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f304b-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="f304b-854">Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="f304b-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f304b-855">Конфигурация прокси-сервера использует протокол HTTP и токен связывания</span><span class="sxs-lookup"><span data-stu-id="f304b-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="f304b-856">Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP.</span><span class="sxs-lookup"><span data-stu-id="f304b-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f304b-857">Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f304b-858">Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника.</span><span class="sxs-lookup"><span data-stu-id="f304b-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f304b-859">Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`).</span><span class="sxs-lookup"><span data-stu-id="f304b-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f304b-860">Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер.</span><span class="sxs-lookup"><span data-stu-id="f304b-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f304b-861">ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды.</span><span class="sxs-lookup"><span data-stu-id="f304b-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f304b-862">Если значения токена не совпадают, запрос заносится в журнал и отклоняется.</span><span class="sxs-lookup"><span data-stu-id="f304b-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f304b-863">Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере.</span><span class="sxs-lookup"><span data-stu-id="f304b-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f304b-864">Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f304b-865">Модуль ASP.NET Core с общей конфигурацией IIS</span><span class="sxs-lookup"><span data-stu-id="f304b-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f304b-866">Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="f304b-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f304b-867">Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.</span><span class="sxs-lookup"><span data-stu-id="f304b-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="f304b-868">При использовании общей конфигурации IIS выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="f304b-869">Отключите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f304b-870">Запустите установщик.</span><span class="sxs-lookup"><span data-stu-id="f304b-870">Run the installer.</span></span>
1. <span data-ttu-id="f304b-871">Экспортируйте обновленный файл *applicationHost.config* в общую папку.</span><span class="sxs-lookup"><span data-stu-id="f304b-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f304b-872">Повторно включите общую конфигурацию IIS.</span><span class="sxs-lookup"><span data-stu-id="f304b-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f304b-873">Версия модуля и журналы установщика хостинга Bundle</span><span class="sxs-lookup"><span data-stu-id="f304b-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f304b-874">Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="f304b-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f304b-875">В системе размещения перейдите к папке *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f304b-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f304b-876">Найдите файл *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="f304b-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f304b-877">Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="f304b-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f304b-878">Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.</span><span class="sxs-lookup"><span data-stu-id="f304b-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f304b-879">Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="f304b-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f304b-880">Модуль, схемы и расположение файлов конфигурации</span><span class="sxs-lookup"><span data-stu-id="f304b-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f304b-881">Module</span><span class="sxs-lookup"><span data-stu-id="f304b-881">Module</span></span>

<span data-ttu-id="f304b-882">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="f304b-885">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f304b-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="f304b-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="f304b-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f304b-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="f304b-888">Схема</span><span class="sxs-lookup"><span data-stu-id="f304b-888">Schema</span></span>

<span data-ttu-id="f304b-889">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-889">**IIS**</span></span>

* <span data-ttu-id="f304b-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="f304b-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-891">**IIS Express**</span></span>

* <span data-ttu-id="f304b-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f304b-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="f304b-893">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="f304b-893">Configuration</span></span>

<span data-ttu-id="f304b-894">**Службы IIS**</span><span class="sxs-lookup"><span data-stu-id="f304b-894">**IIS**</span></span>

* <span data-ttu-id="f304b-895">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f304b-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f304b-896">**IIS Express**</span></span>

* <span data-ttu-id="f304b-897">Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="f304b-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f304b-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f304b-899">Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="f304b-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f304b-900">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f304b-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="f304b-901">[Справочные материалы по модулю ASP.NET Core (главная ветвь)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Используйте раскрывающийся список **Branch** (Ветвь), чтобы выбрать нужный выпуск (например, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="f304b-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
