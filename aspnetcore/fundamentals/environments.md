---
title: Использование нескольких сред в ASP.NET Core
author: rick-anderson
description: Сведения об управлении поведением приложений в разных средах в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330664"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="c2976-103">Использование нескольких сред в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c2976-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2976-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="c2976-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c2976-105">ASP.NET Core настраивает поведение приложения в зависимости от среды выполнения с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="c2976-106">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2976-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="c2976-107">Среды</span><span class="sxs-lookup"><span data-stu-id="c2976-107">Environments</span></span>

<span data-ttu-id="c2976-108">Чтобы определить среду выполнения, ASP.NET Core считывает данные из следующих переменных среды:</span><span class="sxs-lookup"><span data-stu-id="c2976-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. <span data-ttu-id="c2976-109">[DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration).</span><span class="sxs-lookup"><span data-stu-id="c2976-109">[DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)</span></span>
1. <span data-ttu-id="c2976-110">`ASPNETCORE_ENVIRONMENT` при вызове <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>.</span><span class="sxs-lookup"><span data-stu-id="c2976-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="c2976-111">Шаблоны по умолчанию для веб-приложений ASP.NET Core вызывают `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="c2976-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="c2976-112">Значение `ASPNETCORE_ENVIRONMENT` переопределяет `DOTNET_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c2976-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="c2976-113">Переменной `IHostEnvironment.EnvironmentName` можно присвоить любое значение, но платформа предоставляет следующие значения:</span><span class="sxs-lookup"><span data-stu-id="c2976-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="c2976-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: на локальном компьютере в файле [launchSettings.json](#lsj) для `ASPNETCORE_ENVIRONMENT` задается значение `Development`.</span><span class="sxs-lookup"><span data-stu-id="c2976-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="c2976-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: значение по умолчанию, если `DOTNET_ENVIRONMENT` и `ASPNETCORE_ENVIRONMENT` не заданы.</span><span class="sxs-lookup"><span data-stu-id="c2976-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="c2976-116">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="c2976-116">The following code:</span></span>

* <span data-ttu-id="c2976-117">Вызывает [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage), если `ASPNETCORE_ENVIRONMENT` имеет значение `Development`.</span><span class="sxs-lookup"><span data-stu-id="c2976-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="c2976-118">Позволяет вызвать [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler), если переменная `ASPNETCORE_ENVIRONMENT` имеет значение `Staging`, `Production` или `Staging_2`.</span><span class="sxs-lookup"><span data-stu-id="c2976-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="c2976-119">Позволяет внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c2976-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="c2976-120">Этот подход удобен, когда для приложения требуется просто скорректировать `Startup.Configure` для нескольких сред с минимальными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="c2976-121">Это аналогично коду, созданному на основе шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c2976-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="c2976-122">[Вспомогательная функция тега среды](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) использует значение [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) для включения или исключения разметки в элементе:</span><span class="sxs-lookup"><span data-stu-id="c2976-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="c2976-123">[Страница со сведениями](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) в [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) включает предыдущую разметку и отображает значение `IWebHostEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="c2976-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="c2976-124">В ОС Windows и macOS регистр символов в переменных среды и их значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="c2976-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="c2976-125">В ОС Linux в переменных среды и их значениях **регистр символов по умолчанию учитывается**.</span><span class="sxs-lookup"><span data-stu-id="c2976-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="c2976-126">Создание EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="c2976-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="c2976-127">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample), используемый в этом документе, основан на проекте Razor Pages с именем *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="c2976-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="c2976-128">Следующий код позволяет создать и запустить веб-приложение с именем *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="c2976-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="c2976-129">При запуске приложения отображаются некоторые из следующих выходных данных:</span><span class="sxs-lookup"><span data-stu-id="c2976-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="c2976-130">Разработка и launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="c2976-130">Development and launchSettings.json</span></span>

<span data-ttu-id="c2976-131">В среде разработки могут быть включены функции, которые не должны быть доступны в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="c2976-132">Например, шаблоны ASP.NET Core включают в среде разработки [страницу со сведениями об исключении для разработчика](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="c2976-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="c2976-133">Среду для локального компьютера разработки можно задать в файле *Properties\launchSettings.json* проекта.</span><span class="sxs-lookup"><span data-stu-id="c2976-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="c2976-134">Значения среды, заданные в файле *launchSettings.json*, переопределяют значения, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c2976-135">Файл *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c2976-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="c2976-136">используется только на локальном компьютере разработки;</span><span class="sxs-lookup"><span data-stu-id="c2976-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="c2976-137">не развернут;</span><span class="sxs-lookup"><span data-stu-id="c2976-137">Is not deployed.</span></span>
* <span data-ttu-id="c2976-138">содержит параметры профиля.</span><span class="sxs-lookup"><span data-stu-id="c2976-138">contains profile settings.</span></span>

<span data-ttu-id="c2976-139">В следующем фрагменте JSON показан файл *launchSettings.json* для веб-проекта ASP.NET Core с именем *EnvironmentsSample*, созданный с помощью Visual Studio или `dotnet new`:</span><span class="sxs-lookup"><span data-stu-id="c2976-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="c2976-140">Предыдущая разметка содержит два профиля:</span><span class="sxs-lookup"><span data-stu-id="c2976-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="c2976-141">`IIS Express`. Профиль по умолчанию, используемый при запуске приложения из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c2976-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="c2976-142">Ключ `"commandName"` имеет значение `"IISExpress"`, поэтому [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) является веб-сервером.</span><span class="sxs-lookup"><span data-stu-id="c2976-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="c2976-143">Профиль запуска можно задать для проекта или любого другого профиля.</span><span class="sxs-lookup"><span data-stu-id="c2976-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="c2976-144">Например, на приведенном ниже изображении при выборе имени проекта запускается [веб-сервер Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c2976-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![Запуск IIS Express из меню](environments/_static/iisx2.png)
* <span data-ttu-id="c2976-146">`EnvironmentsSample`. Имя профиля — это имя проекта.</span><span class="sxs-lookup"><span data-stu-id="c2976-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="c2976-147">Этот профиль используется по умолчанию при запуске приложения с помощью `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c2976-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="c2976-148">Ключ `"commandName"` имеет значение `"Project"`, поэтому запускается [веб-сервер Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c2976-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="c2976-149">Значение `commandName` может определять запускаемый веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="c2976-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="c2976-150">`commandName` может иметь одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="c2976-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="c2976-151">`IISExpress`: запуск IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c2976-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="c2976-152">`IIS`: веб-сервер не запущен.</span><span class="sxs-lookup"><span data-stu-id="c2976-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="c2976-153">Службы IIS должны быть доступны.</span><span class="sxs-lookup"><span data-stu-id="c2976-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="c2976-154">`Project`: запуск Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c2976-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="c2976-155">Вкладка **Отладка** в свойствах проекта Visual Studio предоставляет графический пользовательский интерфейс для изменения файла *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c2976-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="c2976-156">Для вступления в силу изменений, внесенных в профили проекта, может потребоваться перезапуск веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c2976-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="c2976-157">Чтобы сервер Kestrel обнаружил изменения, внесенные в среду, его необходимо перезапустить.</span><span class="sxs-lookup"><span data-stu-id="c2976-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Задание переменных среды в свойствах проекта](environments/_static/project-properties-debug.png)

<span data-ttu-id="c2976-159">Следующий файл *launchSettings.json* содержит несколько профилей:</span><span class="sxs-lookup"><span data-stu-id="c2976-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="c2976-160">Профили можно выбирать:</span><span class="sxs-lookup"><span data-stu-id="c2976-160">Profiles can be selected:</span></span>

* <span data-ttu-id="c2976-161">В пользовательском интерфейсе Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c2976-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="c2976-162">С помощью команды [`dotnet run`](/dotnet/core/tools/dotnet-run) в командной оболочке. При этом для параметра `--launch-profile` нужно задать имя профиля.</span><span class="sxs-lookup"><span data-stu-id="c2976-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="c2976-163">*Этот подход поддерживает только профили Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="c2976-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="c2976-164">В файле *launchSettings.json* не должны храниться секреты.</span><span class="sxs-lookup"><span data-stu-id="c2976-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="c2976-165">Для хранения секретов во время разработки в локальной среде можно использовать [средство Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c2976-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="c2976-166">При использовании [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="c2976-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="c2976-167">В следующем примере задается несколько [переменных среды для значений конфигурации узла](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="c2976-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="c2976-168">Файл *.vscode/launch.json* используется только Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c2976-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="c2976-169">Рабочие</span><span class="sxs-lookup"><span data-stu-id="c2976-169">Production</span></span>

<span data-ttu-id="c2976-170">Конфигурация рабочей среды должна обеспечивать максимальный уровень безопасности, [производительности](xref:performance/performance-best-practices) и надежности приложений.</span><span class="sxs-lookup"><span data-stu-id="c2976-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="c2976-171">Некоторые общие параметры, отличные от разработки:</span><span class="sxs-lookup"><span data-stu-id="c2976-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="c2976-172">[Кэширование](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="c2976-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="c2976-173">ресурсы на стороне клиента объединяются в пакеты, уплотняются и могут предоставляться из сети CDN;</span><span class="sxs-lookup"><span data-stu-id="c2976-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="c2976-174">страницы с сообщениями об ошибках диагностики отключены;</span><span class="sxs-lookup"><span data-stu-id="c2976-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="c2976-175">включены страницы с понятными пользователям сообщениями об ошибках;</span><span class="sxs-lookup"><span data-stu-id="c2976-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="c2976-176">включены средства [ведения журналов](xref:fundamentals/logging/index) и мониторинга в рабочей среде</span><span class="sxs-lookup"><span data-stu-id="c2976-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="c2976-177">(например, с использованием [Application Insights](/azure/application-insights/app-insights-asp-net-core)).</span><span class="sxs-lookup"><span data-stu-id="c2976-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c2976-178">Указание среды</span><span class="sxs-lookup"><span data-stu-id="c2976-178">Set the environment</span></span>

<span data-ttu-id="c2976-179">Часто бывает полезным указать определенную среду для тестирования с переменной среды или параметром платформы.</span><span class="sxs-lookup"><span data-stu-id="c2976-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="c2976-180">Если среда не указана, по умолчанию используется среда `Production`, в которой большинство функций отладки отключено.</span><span class="sxs-lookup"><span data-stu-id="c2976-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="c2976-181">Способ указания среды зависит от операционной системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="c2976-182">При создании узла среду приложения определяет последний параметр среды, считанный приложением.</span><span class="sxs-lookup"><span data-stu-id="c2976-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="c2976-183">Среду приложения невозможно изменить во время его выполнения.</span><span class="sxs-lookup"><span data-stu-id="c2976-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="c2976-184">На [странице со сведениями](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) в [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) отображается значение `IWebHostEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="c2976-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c2976-185">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="c2976-185">Azure App Service</span></span>

<span data-ttu-id="c2976-186">Чтобы установить среду в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="c2976-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="c2976-187">Выберите приложение из колонки **Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="c2976-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="c2976-188">В группе **Параметры** выберите колонку **Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="c2976-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="c2976-189">На вкладке **Параметры приложения** выберите **Новый параметр приложения**.</span><span class="sxs-lookup"><span data-stu-id="c2976-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="c2976-190">В окне **Добавить или изменить параметр приложения** в поле **Имя** введите `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c2976-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="c2976-191">В поле **Значение** укажите среду (например, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="c2976-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="c2976-192">Установите флажок **Параметр слота развертывания**, если нужно, чтобы параметр среды оставался в текущем слоте при замене слота развертывания.</span><span class="sxs-lookup"><span data-stu-id="c2976-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="c2976-193">Дополнительные сведения см. в статье [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) (Настройка промежуточных сред в Службе приложений Azure) в документации по Azure.</span><span class="sxs-lookup"><span data-stu-id="c2976-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="c2976-194">Нажмите кнопку **ОК**, чтобы закрыть окно **Добавить или изменить параметр приложения**.</span><span class="sxs-lookup"><span data-stu-id="c2976-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="c2976-195">Нажмите кнопку **Сохранить** в верхней части колонки **Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="c2976-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="c2976-196">Служба приложений Azure автоматически перезапускает приложение после добавления, изменения или удаления параметра приложения на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="c2976-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="c2976-197">Windows</span><span class="sxs-lookup"><span data-stu-id="c2976-197">Windows</span></span>

<span data-ttu-id="c2976-198">Значения среды в файле *launchSettings.json* переопределяют значения, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c2976-199">Если приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), то, чтобы задать переменную `ASPNETCORE_ENVIRONMENT` для текущего сеанса, используйте следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c2976-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="c2976-200">**Командная строка**</span><span class="sxs-lookup"><span data-stu-id="c2976-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="c2976-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c2976-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="c2976-202">Предыдущая команда позволяет задать `ASPNETCORE_ENVIRONMENT` только для процессов, запускаемых из этого командного окна.</span><span class="sxs-lookup"><span data-stu-id="c2976-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="c2976-203">Чтобы задать это значение в Windows на глобальном уровне, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="c2976-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="c2976-204">Последовательно откройте **Панель управления** > **Система** > **Дополнительные параметры системы** и добавьте или измените значение `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="c2976-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Дополнительные параметры системы](environments/_static/systemsetting_environment.png)

  ![Переменная среды ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="c2976-207">Откройте командную строку администратора и выполните команду `setx` либо откройте командную строку администратора PowerShell и используйте `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="c2976-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="c2976-208">**Командная строка**</span><span class="sxs-lookup"><span data-stu-id="c2976-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="c2976-209">Параметр `/M` указывает на установку переменной среды на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c2976-210">Если параметр `/M` не используется, переменная среды задается для учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c2976-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="c2976-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c2976-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="c2976-212">Значение параметра `Machine` указывает на установку переменной среды на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c2976-213">При изменении значения параметра на `User` переменная среды задается для учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c2976-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="c2976-214">Если переменная среды `ASPNETCORE_ENVIRONMENT` задана глобально, она действует для `dotnet run` в любом окне командной строки, открываемом после установки значения.</span><span class="sxs-lookup"><span data-stu-id="c2976-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="c2976-215">Значения среды в файле *launchSettings.json* переопределяют значения, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c2976-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="c2976-216">**web.config**</span></span>

<span data-ttu-id="c2976-217">Сведения об установке переменной среды `ASPNETCORE_ENVIRONMENT` в файле *web.config* см. в разделе *Настройка переменной среды* в <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c2976-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="c2976-218">**Файл проекта или профиль публикации**</span><span class="sxs-lookup"><span data-stu-id="c2976-218">**Project file or publish profile**</span></span>

<span data-ttu-id="c2976-219">**Для развертываний Windows IIS:** Включите свойство `<EnvironmentName>` в [профиле публикации (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) или файле проекта.</span><span class="sxs-lookup"><span data-stu-id="c2976-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c2976-220">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="c2976-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c2976-221">**Пул приложений IIS**</span><span class="sxs-lookup"><span data-stu-id="c2976-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="c2976-222">Чтобы задать переменную среды `ASPNETCORE_ENVIRONMENT` для приложения, выполняющегося в изолированном пуле приложений (такая возможность поддерживается в службах IIS 10.0 и более поздних версий), см. подраздел, посвященный команде *AppCmd.exe*, в разделе [Переменные среды &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="c2976-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="c2976-223">Если переменная среды `ASPNETCORE_ENVIRONMENT` задана для пула приложений, ее значение переопределяет значение на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="c2976-224">При размещении приложения в службах IIS и добавлении или изменении переменной среды `ASPNETCORE_ENVIRONMENT` используйте один из следующих подходов по применению нового значения в приложении.</span><span class="sxs-lookup"><span data-stu-id="c2976-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="c2976-225">Из командной строки выполните команду `net stop was /y`, за которой следует `net start w3svc`.</span><span class="sxs-lookup"><span data-stu-id="c2976-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="c2976-226">Перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="c2976-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="c2976-227">macOS</span><span class="sxs-lookup"><span data-stu-id="c2976-227">macOS</span></span>

<span data-ttu-id="c2976-228">Задать текущую среду в macOS можно в командной строке при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="c2976-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="c2976-229">Также можно задать среду с помощью команды `export` до запуска приложения:</span><span class="sxs-lookup"><span data-stu-id="c2976-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="c2976-230">Переменные среды на уровне компьютера задаются в файле *BASHRC* или *BASH_PROFILE*.</span><span class="sxs-lookup"><span data-stu-id="c2976-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="c2976-231">Измените файл в любом текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="c2976-231">Edit the file using any text editor.</span></span> <span data-ttu-id="c2976-232">Добавьте следующий оператор:</span><span class="sxs-lookup"><span data-stu-id="c2976-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="c2976-233">Linux</span><span class="sxs-lookup"><span data-stu-id="c2976-233">Linux</span></span>

<span data-ttu-id="c2976-234">В дистрибутивах Linux используйте команду `export` в командной строке для значений переменных на уровне сеанса или в файле *BASH_PROFILE* для значений среды на уровне компьютера.</span><span class="sxs-lookup"><span data-stu-id="c2976-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="c2976-235">Указание среды в коде</span><span class="sxs-lookup"><span data-stu-id="c2976-235">Set the environment in code</span></span>

<span data-ttu-id="c2976-236">Вызовите <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="c2976-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="c2976-237">См. раздел <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="c2976-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="c2976-238">Конфигурация для разных сред</span><span class="sxs-lookup"><span data-stu-id="c2976-238">Configuration by environment</span></span>

<span data-ttu-id="c2976-239">Для загрузки конфигурации среды см. <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="c2976-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="c2976-240">Класс Startup и его методы для разных сред</span><span class="sxs-lookup"><span data-stu-id="c2976-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="c2976-241">Внедрение IWebHostEnvironment в класс Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="c2976-242">Внедрите <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в конструктор `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c2976-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="c2976-243">Этот подход удобен, когда для приложения требуется настроить `Startup` всего для нескольких сред с минимальными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="c2976-244">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="c2976-244">In the following example:</span></span>

* <span data-ttu-id="c2976-245">Среда хранится в поле `_env`.</span><span class="sxs-lookup"><span data-stu-id="c2976-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="c2976-246">`_env` используется в `ConfigureServices` и `Configure` для применения конфигурации запуска на основе среды приложения.</span><span class="sxs-lookup"><span data-stu-id="c2976-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="c2976-247">Соглашения о классе Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-247">Startup class conventions</span></span>

<span data-ttu-id="c2976-248">При запуске приложения ASP.NET Core [класс Startup](xref:fundamentals/startup) выполняет его начальную загрузку.</span><span class="sxs-lookup"><span data-stu-id="c2976-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="c2976-249">Приложение может определять несколько классов `Startup` для различных сред.</span><span class="sxs-lookup"><span data-stu-id="c2976-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="c2976-250">Подходящий класс `Startup` выбирается во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c2976-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c2976-251">Класс, у которого суффикс имени соответствует текущей среде, получает приоритет.</span><span class="sxs-lookup"><span data-stu-id="c2976-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c2976-252">Если соответствующий класс `Startup{EnvironmentName}` не найден, используется класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c2976-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="c2976-253">Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="c2976-254">Для обычных приложений такой подход не нужно использовать.</span><span class="sxs-lookup"><span data-stu-id="c2976-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="c2976-255">Чтобы реализовать классы `Startup` на основе среды, создайте классы `Startup{EnvironmentName}` и резервный класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c2976-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="c2976-256">Используйте перегрузку [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), которая принимает имя сборки:</span><span class="sxs-lookup"><span data-stu-id="c2976-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="c2976-257">Соглашения о методах Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-257">Startup method conventions</span></span>

<span data-ttu-id="c2976-258">Методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) поддерживают версии для конкретных сред в формате `Configure<EnvironmentName>` и `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="c2976-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="c2976-259">Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды:</span><span class="sxs-lookup"><span data-stu-id="c2976-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="c2976-260">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c2976-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2976-261">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="c2976-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c2976-262">ASP.NET Core настраивает поведение приложения в зависимости от среды выполнения с помощью переменной среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="c2976-263">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2976-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="c2976-264">Среды</span><span class="sxs-lookup"><span data-stu-id="c2976-264">Environments</span></span>

<span data-ttu-id="c2976-265">ASP.NET Core считывает переменную среды `ASPNETCORE_ENVIRONMENT` при запуске приложения и сохраняет ее значение в свойстве [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="c2976-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="c2976-266">Переменной `ASPNETCORE_ENVIRONMENT` можно присвоить любое значение, но платформа предоставляет три значения:</span><span class="sxs-lookup"><span data-stu-id="c2976-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="c2976-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="c2976-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="c2976-268">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="c2976-268">The preceding code:</span></span>

* <span data-ttu-id="c2976-269">Вызывает [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage), если `ASPNETCORE_ENVIRONMENT` имеет значение `Development`.</span><span class="sxs-lookup"><span data-stu-id="c2976-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="c2976-270">Вызывает [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler), если переменная `ASPNETCORE_ENVIRONMENT` имеет одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="c2976-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="c2976-271">[Вспомогательная функция тега среды](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) использует значение `IHostingEnvironment.EnvironmentName` для включения или исключения разметки в элементе:</span><span class="sxs-lookup"><span data-stu-id="c2976-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="c2976-272">В ОС Windows и macOS регистр символов в переменных среды и их значениях не учитывается.</span><span class="sxs-lookup"><span data-stu-id="c2976-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="c2976-273">В ОС Linux в переменных среды и их значениях регистр символов по умолчанию учитывается.</span><span class="sxs-lookup"><span data-stu-id="c2976-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="c2976-274">Разработка</span><span class="sxs-lookup"><span data-stu-id="c2976-274">Development</span></span>

<span data-ttu-id="c2976-275">В среде разработки могут быть включены функции, которые не должны быть доступны в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="c2976-276">Например, шаблоны ASP.NET Core включают в среде разработки [страницу со сведениями об исключении для разработчика](xref:fundamentals/error-handling#developer-exception-page).</span><span class="sxs-lookup"><span data-stu-id="c2976-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="c2976-277">Среду для локального компьютера разработки можно задать в файле *Properties\launchSettings.json* проекта.</span><span class="sxs-lookup"><span data-stu-id="c2976-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="c2976-278">Значения среды, заданные в файле *launchSettings.json*, переопределяют значения, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="c2976-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="c2976-279">В следующем коде JSON показаны три профиля из файла *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c2976-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="c2976-280">Свойство `applicationUrl` в *launchSettings.json* может задать список URL-адресов сервера.</span><span class="sxs-lookup"><span data-stu-id="c2976-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="c2976-281">Для разделения URL-адресов в списке используется точка с запятой:</span><span class="sxs-lookup"><span data-stu-id="c2976-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="c2976-282">Когда приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), используется первый профиль с атрибутом `"commandName": "Project"`.</span><span class="sxs-lookup"><span data-stu-id="c2976-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="c2976-283">Значение `commandName` определяет запускаемый веб-сервер.</span><span class="sxs-lookup"><span data-stu-id="c2976-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="c2976-284">`commandName` может иметь одно из следующих значений:</span><span class="sxs-lookup"><span data-stu-id="c2976-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="c2976-285">`Project` (запускается Kestrel)</span><span class="sxs-lookup"><span data-stu-id="c2976-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="c2976-286">Когда приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), выполняются указанные ниже действия:</span><span class="sxs-lookup"><span data-stu-id="c2976-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="c2976-287">Считывается файл *launchSettings.json*, если он доступен.</span><span class="sxs-lookup"><span data-stu-id="c2976-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="c2976-288">Параметры `environmentVariables` в файле *launchSettings.json* переопределяют переменные среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="c2976-289">Отображается среда размещения.</span><span class="sxs-lookup"><span data-stu-id="c2976-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="c2976-290">Ниже представлены выходные данные приложения, запущенного с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run):</span><span class="sxs-lookup"><span data-stu-id="c2976-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="c2976-291">Вкладка **Отладка** в свойствах проекта Visual Studio предоставляет графический пользовательский интерфейс для изменения файла *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c2976-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Задание переменных среды в свойствах проекта](environments/_static/project-properties-debug.png)

<span data-ttu-id="c2976-293">Для вступления в силу изменений, внесенных в профили проекта, может потребоваться перезапуск веб-сервера.</span><span class="sxs-lookup"><span data-stu-id="c2976-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="c2976-294">Чтобы сервер Kestrel обнаружил изменения, внесенные в среду, его необходимо перезапустить.</span><span class="sxs-lookup"><span data-stu-id="c2976-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="c2976-295">В файле *launchSettings.json* не должны храниться секреты.</span><span class="sxs-lookup"><span data-stu-id="c2976-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="c2976-296">Для хранения секретов во время разработки в локальной среде можно использовать [средство Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c2976-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="c2976-297">При использовании [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="c2976-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="c2976-298">В следующем примере показано, как присвоить среде значение `Development`:</span><span class="sxs-lookup"><span data-stu-id="c2976-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="c2976-299">Файл *.vscode/launch.json* в проекте не читается при запуске приложения с помощью команды `dotnet run` так же, как файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c2976-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="c2976-300">При запуске приложения в среде разработки без файла *launchSettings.json* укажите среду с помощью переменной среды или задайте аргумент командной строки как команду `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c2976-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="c2976-301">Рабочие</span><span class="sxs-lookup"><span data-stu-id="c2976-301">Production</span></span>

<span data-ttu-id="c2976-302">Конфигурация рабочей среды должна обеспечивать максимальный уровень безопасности, производительности и надежности приложений.</span><span class="sxs-lookup"><span data-stu-id="c2976-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="c2976-303">Некоторые общие параметры, отличные от разработки:</span><span class="sxs-lookup"><span data-stu-id="c2976-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="c2976-304">кэширование;</span><span class="sxs-lookup"><span data-stu-id="c2976-304">Caching.</span></span>
* <span data-ttu-id="c2976-305">ресурсы на стороне клиента объединяются в пакеты, уплотняются и могут предоставляться из сети CDN;</span><span class="sxs-lookup"><span data-stu-id="c2976-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="c2976-306">страницы с сообщениями об ошибках диагностики отключены;</span><span class="sxs-lookup"><span data-stu-id="c2976-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="c2976-307">включены страницы с понятными пользователям сообщениями об ошибках;</span><span class="sxs-lookup"><span data-stu-id="c2976-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="c2976-308">включены средства ведения журналов и мониторинга в рабочей среде,</span><span class="sxs-lookup"><span data-stu-id="c2976-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="c2976-309">например [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="c2976-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c2976-310">Указание среды</span><span class="sxs-lookup"><span data-stu-id="c2976-310">Set the environment</span></span>

<span data-ttu-id="c2976-311">Часто бывает полезным указать определенную среду для тестирования с переменной среды или параметром платформы.</span><span class="sxs-lookup"><span data-stu-id="c2976-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="c2976-312">Если среда не указана, по умолчанию используется среда `Production`, в которой большинство функций отладки отключено.</span><span class="sxs-lookup"><span data-stu-id="c2976-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="c2976-313">Способ указания среды зависит от операционной системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="c2976-314">При создании узла среду приложения определяет последний параметр среды, считанный приложением.</span><span class="sxs-lookup"><span data-stu-id="c2976-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="c2976-315">Среду приложения невозможно изменить во время его выполнения.</span><span class="sxs-lookup"><span data-stu-id="c2976-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="c2976-316">Переменная среды или параметр платформы</span><span class="sxs-lookup"><span data-stu-id="c2976-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="c2976-317">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="c2976-317">Azure App Service</span></span>

<span data-ttu-id="c2976-318">Чтобы установить среду в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="c2976-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="c2976-319">Выберите приложение из колонки **Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="c2976-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="c2976-320">В группе **Параметры** выберите колонку **Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="c2976-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="c2976-321">На вкладке **Параметры приложения** выберите **Новый параметр приложения**.</span><span class="sxs-lookup"><span data-stu-id="c2976-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="c2976-322">В окне **Добавить или изменить параметр приложения** в поле **Имя** введите `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c2976-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="c2976-323">В поле **Значение** укажите среду (например, `Staging`).</span><span class="sxs-lookup"><span data-stu-id="c2976-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="c2976-324">Установите флажок **Параметр слота развертывания**, если нужно, чтобы параметр среды оставался в текущем слоте при замене слота развертывания.</span><span class="sxs-lookup"><span data-stu-id="c2976-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="c2976-325">Дополнительные сведения см. в статье [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) (Настройка промежуточных сред в Службе приложений Azure) в документации по Azure.</span><span class="sxs-lookup"><span data-stu-id="c2976-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="c2976-326">Нажмите кнопку **ОК**, чтобы закрыть окно **Добавить или изменить параметр приложения**.</span><span class="sxs-lookup"><span data-stu-id="c2976-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="c2976-327">Нажмите кнопку **Сохранить** в верхней части колонки **Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="c2976-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="c2976-328">Служба приложений Azure автоматически перезапускает приложение после добавления, изменения или удаления параметра приложения (переменной среды) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="c2976-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="c2976-329">Windows</span><span class="sxs-lookup"><span data-stu-id="c2976-329">Windows</span></span>

<span data-ttu-id="c2976-330">Если приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), то, чтобы задать переменную `ASPNETCORE_ENVIRONMENT` для текущего сеанса, используйте следующие команды:</span><span class="sxs-lookup"><span data-stu-id="c2976-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="c2976-331">**Командная строка**</span><span class="sxs-lookup"><span data-stu-id="c2976-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="c2976-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c2976-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="c2976-333">Эти команды действуют только для текущего окна.</span><span class="sxs-lookup"><span data-stu-id="c2976-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="c2976-334">Когда окно закрывается, для параметра `ASPNETCORE_ENVIRONMENT` восстанавливается значение по умолчанию или значение, заданное на компьютере.</span><span class="sxs-lookup"><span data-stu-id="c2976-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="c2976-335">Чтобы задать это значение в Windows на глобальном уровне, используйте один из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="c2976-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="c2976-336">Последовательно откройте **Панель управления** > **Система** > **Дополнительные параметры системы** и добавьте или измените значение `ASPNETCORE_ENVIRONMENT`:</span><span class="sxs-lookup"><span data-stu-id="c2976-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Дополнительные параметры системы](environments/_static/systemsetting_environment.png)

  ![Переменная среды ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="c2976-339">Откройте командную строку администратора и выполните команду `setx` либо откройте командную строку администратора PowerShell и используйте `[Environment]::SetEnvironmentVariable`:</span><span class="sxs-lookup"><span data-stu-id="c2976-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="c2976-340">**Командная строка**</span><span class="sxs-lookup"><span data-stu-id="c2976-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="c2976-341">Параметр `/M` указывает на установку переменной среды на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c2976-342">Если параметр `/M` не используется, переменная среды задается для учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c2976-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="c2976-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="c2976-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="c2976-344">Значение параметра `Machine` указывает на установку переменной среды на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="c2976-345">При изменении значения параметра на `User` переменная среды задается для учетной записи пользователя.</span><span class="sxs-lookup"><span data-stu-id="c2976-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="c2976-346">Если переменная среды `ASPNETCORE_ENVIRONMENT` задана глобально, она действует для `dotnet run` в любом окне командной строки, открываемом после установки значения.</span><span class="sxs-lookup"><span data-stu-id="c2976-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="c2976-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="c2976-347">**web.config**</span></span>

<span data-ttu-id="c2976-348">Сведения об установке переменной среды `ASPNETCORE_ENVIRONMENT` в файле *web.config* см. в разделе *Настройка переменной среды* в <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c2976-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="c2976-349">**Файл проекта или профиль публикации**</span><span class="sxs-lookup"><span data-stu-id="c2976-349">**Project file or publish profile**</span></span>

<span data-ttu-id="c2976-350">**Для развертываний Windows IIS:** Включите свойство `<EnvironmentName>` в [профиле публикации (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) или файле проекта.</span><span class="sxs-lookup"><span data-stu-id="c2976-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="c2976-351">При этом подходе во время публикации проекта среда задается в файле *web.config*:</span><span class="sxs-lookup"><span data-stu-id="c2976-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c2976-352">**Пул приложений IIS**</span><span class="sxs-lookup"><span data-stu-id="c2976-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="c2976-353">Чтобы задать переменную среды `ASPNETCORE_ENVIRONMENT` для приложения, выполняющегося в изолированном пуле приложений (такая возможность поддерживается в службах IIS 10.0 и более поздних версий), см. подраздел, посвященный команде *AppCmd.exe*, в разделе [Переменные среды &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="c2976-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="c2976-354">Если переменная среды `ASPNETCORE_ENVIRONMENT` задана для пула приложений, ее значение переопределяет значение на уровне системы.</span><span class="sxs-lookup"><span data-stu-id="c2976-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c2976-355">При размещении приложения в службах IIS и добавлении или изменении переменной среды `ASPNETCORE_ENVIRONMENT` используйте один из следующих подходов по применению нового значения в приложении.</span><span class="sxs-lookup"><span data-stu-id="c2976-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="c2976-356">Из командной строки выполните команду `net stop was /y`, за которой следует `net start w3svc`.</span><span class="sxs-lookup"><span data-stu-id="c2976-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="c2976-357">Перезапустите сервер.</span><span class="sxs-lookup"><span data-stu-id="c2976-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="c2976-358">macOS</span><span class="sxs-lookup"><span data-stu-id="c2976-358">macOS</span></span>

<span data-ttu-id="c2976-359">Задать текущую среду в macOS можно в командной строке при запуске приложения:</span><span class="sxs-lookup"><span data-stu-id="c2976-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="c2976-360">Также можно задать среду с помощью команды `export` до запуска приложения:</span><span class="sxs-lookup"><span data-stu-id="c2976-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="c2976-361">Переменные среды на уровне компьютера задаются в файле *BASHRC* или *BASH_PROFILE*.</span><span class="sxs-lookup"><span data-stu-id="c2976-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="c2976-362">Измените файл в любом текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="c2976-362">Edit the file using any text editor.</span></span> <span data-ttu-id="c2976-363">Добавьте следующий оператор:</span><span class="sxs-lookup"><span data-stu-id="c2976-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="c2976-364">Linux</span><span class="sxs-lookup"><span data-stu-id="c2976-364">Linux</span></span>

<span data-ttu-id="c2976-365">В дистрибутивах Linux используйте команду `export` в командной строке для значений переменных на уровне сеанса или в файле *BASH_PROFILE* для значений среды на уровне компьютера.</span><span class="sxs-lookup"><span data-stu-id="c2976-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="c2976-366">Указание среды в коде</span><span class="sxs-lookup"><span data-stu-id="c2976-366">Set the environment in code</span></span>

<span data-ttu-id="c2976-367">Вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="c2976-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="c2976-368">См. раздел <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="c2976-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="c2976-369">Конфигурация для разных сред</span><span class="sxs-lookup"><span data-stu-id="c2976-369">Configuration by environment</span></span>

<span data-ttu-id="c2976-370">Для загрузки конфигурации среды мы рекомендуем:</span><span class="sxs-lookup"><span data-stu-id="c2976-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="c2976-371">Файлы *appsettings* (*appsettings.{Environment}.json*).</span><span class="sxs-lookup"><span data-stu-id="c2976-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="c2976-372">См. раздел <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="c2976-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="c2976-373">Переменные среды (заданные в каждой системе, где размещено приложение).</span><span class="sxs-lookup"><span data-stu-id="c2976-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="c2976-374">См. разделы <xref:fundamentals/host/web-host#environment> и <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="c2976-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="c2976-375">Менеджер секретов (только в среде разработки).</span><span class="sxs-lookup"><span data-stu-id="c2976-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="c2976-376">См. раздел <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="c2976-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="c2976-377">Класс Startup и его методы для разных сред</span><span class="sxs-lookup"><span data-stu-id="c2976-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="c2976-378">Внедрение IHostingEnvironment в Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="c2976-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="c2976-379">Внедрите <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> в `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c2976-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="c2976-380">Этот подход удобен, когда для приложения требуется просто настроить `Startup.Configure` всего для нескольких сред с минимальными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="c2976-381">Внедрение IHostingEnvironment в класс Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="c2976-382">Внедрите <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> в конструктор `Startup` и назначьте службу полю для использования в рамках всего класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c2976-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="c2976-383">Этот подход удобен, когда для приложения требуется настроить запуск всего для нескольких сред с минимальными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="c2976-384">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="c2976-384">In the following example:</span></span>

* <span data-ttu-id="c2976-385">Среда хранится в поле `_env`.</span><span class="sxs-lookup"><span data-stu-id="c2976-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="c2976-386">`_env` используется в `ConfigureServices` и `Configure` для применения конфигурации запуска на основе среды приложения.</span><span class="sxs-lookup"><span data-stu-id="c2976-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="c2976-387">Соглашения о классе Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-387">Startup class conventions</span></span>

<span data-ttu-id="c2976-388">При запуске приложения ASP.NET Core [класс Startup](xref:fundamentals/startup) выполняет его начальную загрузку.</span><span class="sxs-lookup"><span data-stu-id="c2976-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="c2976-389">Приложение может определять отдельные классы `Startup` для различных сред (например, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="c2976-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="c2976-390">Подходящий класс `Startup` выбирается во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="c2976-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c2976-391">Класс, у которого суффикс имени соответствует текущей среде, получает приоритет.</span><span class="sxs-lookup"><span data-stu-id="c2976-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c2976-392">Если соответствующий класс `Startup{EnvironmentName}` не найден, используется класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c2976-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="c2976-393">Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="c2976-394">Чтобы реализовать классы `Startup` на основе среды, создайте класс `Startup{EnvironmentName}` для каждой используемой среды и резервный класс `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c2976-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="c2976-395">Используйте перегрузку [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), которая принимает имя сборки:</span><span class="sxs-lookup"><span data-stu-id="c2976-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="c2976-396">Соглашения о методах Startup</span><span class="sxs-lookup"><span data-stu-id="c2976-396">Startup method conventions</span></span>

<span data-ttu-id="c2976-397">Методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) поддерживают версии для конкретных сред в формате `Configure<EnvironmentName>` и `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="c2976-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="c2976-398">Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды.</span><span class="sxs-lookup"><span data-stu-id="c2976-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="c2976-399">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c2976-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
