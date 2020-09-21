---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c6b77f20792c32a697fff8d1d78ef3c2536c5f8f
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722901"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="73bf5-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="73bf5-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="73bf5-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="73bf5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73bf5-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="73bf5-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="73bf5-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="73bf5-107">файлы параметров, например *appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="73bf5-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="73bf5-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-108">Environment variables</span></span>
* <span data-ttu-id="73bf5-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-109">Azure Key Vault</span></span>
* <span data-ttu-id="73bf5-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-110">Azure App Configuration</span></span>
* <span data-ttu-id="73bf5-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="73bf5-111">Command-line arguments</span></span>
* <span data-ttu-id="73bf5-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="73bf5-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="73bf5-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="73bf5-113">Directory files</span></span>
* <span data-ttu-id="73bf5-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="73bf5-114">In-memory .NET objects</span></span>

<span data-ttu-id="73bf5-115">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73bf5-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="73bf5-116">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="73bf5-116">Default configuration</span></span>

<span data-ttu-id="73bf5-117">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="73bf5-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="73bf5-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="73bf5-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="73bf5-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="73bf5-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="73bf5-120">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="73bf5-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="73bf5-121">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="73bf5-122">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="73bf5-123">Например, *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="73bf5-124">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="73bf5-125">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="73bf5-126">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="73bf5-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="73bf5-127">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="73bf5-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="73bf5-128">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="73bf5-129">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="73bf5-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="73bf5-130">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="73bf5-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="73bf5-131">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="73bf5-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="73bf5-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="73bf5-132">appsettings.json</span></span>

<span data-ttu-id="73bf5-133">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="73bf5-134">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-135"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="73bf5-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="73bf5-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="73bf5-136">*appsettings.json*</span></span>
1. <span data-ttu-id="73bf5-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="73bf5-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="73bf5-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="73bf5-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="73bf5-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="73bf5-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="73bf5-141">For example, by default:</span></span>

* <span data-ttu-id="73bf5-142">В среде разработки конфигурация *appsettings*.***Development***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="73bf5-143">В рабочей среде конфигурация *appsettings*.***Production***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="73bf5-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="73bf5-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="73bf5-145">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="73bf5-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="73bf5-146">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="73bf5-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="73bf5-147">Изменения, внесенные в файл *appsettings.json* и *appsettings.* `Environment` *.json*, ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="73bf5-148">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="73bf5-149">Объединение коллекций служб</span><span class="sxs-lookup"><span data-stu-id="73bf5-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="73bf5-150">Безопасность и диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="73bf5-150">Security and secret manager</span></span>

<span data-ttu-id="73bf5-151">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="73bf5-152">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="73bf5-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="73bf5-153">Для хранения секретов во время разработки можно использовать [диспетчер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="73bf5-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="73bf5-154">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="73bf5-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="73bf5-155">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="73bf5-156">[По умолчанию](#default) [диспетчер секретов](xref:security/app-secrets) считывает параметры конфигурации после *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="73bf5-157">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="73bf5-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="73bf5-158"><xref:security/app-secrets>.  Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="73bf5-159">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="73bf5-160">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73bf5-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="73bf5-161">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="73bf5-162">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-162">Environment variables</span></span>

<span data-ttu-id="73bf5-163">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json*, *appsettings.* `Environment` *.json* и [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="73bf5-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="73bf5-164">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json*, *appsettings.* `Environment` *.json* и диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="73bf5-165">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-165">The following `set` commands:</span></span>

* <span data-ttu-id="73bf5-166">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="73bf5-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="73bf5-167">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="73bf5-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="73bf5-168">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="73bf5-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="73bf5-169">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="73bf5-169">The preceding environment settings:</span></span>

* <span data-ttu-id="73bf5-170">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="73bf5-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="73bf5-171">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73bf5-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="73bf5-172">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="73bf5-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="73bf5-173">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="73bf5-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="73bf5-174">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="73bf5-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="73bf5-175">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="73bf5-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="73bf5-176">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="73bf5-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="73bf5-177">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="73bf5-178">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="73bf5-179">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="73bf5-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="73bf5-180">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="73bf5-180">In the preceding code:</span></span>

* <span data-ttu-id="73bf5-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="73bf5-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="73bf5-182">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="73bf5-183">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="73bf5-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="73bf5-184">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="73bf5-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="73bf5-185">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="73bf5-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="73bf5-186">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="73bf5-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="73bf5-187">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="73bf5-188">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="73bf5-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="73bf5-189">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="73bf5-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="73bf5-190">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="73bf5-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="73bf5-191">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="73bf5-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="73bf5-192">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="73bf5-193">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-193">Exposed as environment variables.</span></span>

<span data-ttu-id="73bf5-194">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="73bf5-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="73bf5-195">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="73bf5-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="73bf5-196">Переменные среды, заданные в launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="73bf5-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="73bf5-197">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="73bf5-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="73bf5-198">Командная строка</span><span class="sxs-lookup"><span data-stu-id="73bf5-198">Command-line</span></span>

<span data-ttu-id="73bf5-199">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="73bf5-200">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-200">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="73bf5-201">[Секреты приложения (диспетчер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73bf5-202">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-202">Environment variables.</span></span>

<span data-ttu-id="73bf5-203">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="73bf5-204">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="73bf5-204">Command-line arguments</span></span>

<span data-ttu-id="73bf5-205">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="73bf5-206">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="73bf5-207">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="73bf5-208">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="73bf5-208">The key value:</span></span>

* <span data-ttu-id="73bf5-209">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="73bf5-210">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="73bf5-211">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="73bf5-212">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="73bf5-213">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="73bf5-213">Switch mappings</span></span>

<span data-ttu-id="73bf5-214">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="73bf5-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="73bf5-215">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="73bf5-216">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="73bf5-217">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="73bf5-218">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="73bf5-219">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="73bf5-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="73bf5-220">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="73bf5-221">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="73bf5-222">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="73bf5-223">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="73bf5-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-224">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="73bf5-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="73bf5-225">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="73bf5-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="73bf5-226">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73bf5-227">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="73bf5-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="73bf5-228">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-228">Hierarchical configuration data</span></span>

<span data-ttu-id="73bf5-229">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="73bf5-230">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="73bf5-231">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-232">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="73bf5-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="73bf5-233">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="73bf5-234">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="73bf5-235">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="73bf5-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="73bf5-236">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-236">Configuration keys and values</span></span>

<span data-ttu-id="73bf5-237">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-237">Configuration keys:</span></span>

* <span data-ttu-id="73bf5-238">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="73bf5-238">Are case-insensitive.</span></span> <span data-ttu-id="73bf5-239">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="73bf5-240">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="73bf5-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="73bf5-241">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="73bf5-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="73bf5-242">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="73bf5-242">Hierarchical keys</span></span>
  * <span data-ttu-id="73bf5-243">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="73bf5-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="73bf5-244">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="73bf5-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="73bf5-245">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="73bf5-246">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="73bf5-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="73bf5-247">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="73bf5-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73bf5-249">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="73bf5-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="73bf5-250">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-250">Configuration values:</span></span>

* <span data-ttu-id="73bf5-251">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-251">Are strings.</span></span>
* <span data-ttu-id="73bf5-252">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="73bf5-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="73bf5-253">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-253">Configuration providers</span></span>

<span data-ttu-id="73bf5-254">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73bf5-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="73bf5-255">Поставщик</span><span class="sxs-lookup"><span data-stu-id="73bf5-255">Provider</span></span> | <span data-ttu-id="73bf5-256">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="73bf5-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="73bf5-257">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="73bf5-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="73bf5-258">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="73bf5-259">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="73bf5-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="73bf5-260">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="73bf5-261">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="73bf5-262">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-262">Command-line parameters</span></span> |
| [<span data-ttu-id="73bf5-263">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="73bf5-264">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-264">Custom source</span></span> |
| [<span data-ttu-id="73bf5-265">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="73bf5-266">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-266">Environment variables</span></span> |
| [<span data-ttu-id="73bf5-267">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="73bf5-268">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="73bf5-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="73bf5-269">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="73bf5-270">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="73bf5-270">Directory files</span></span> |
| [<span data-ttu-id="73bf5-271">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="73bf5-272">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-272">In-memory collections</span></span> |
| [<span data-ttu-id="73bf5-273">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="73bf5-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="73bf5-274">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-274">File in the user profile directory</span></span> |

<span data-ttu-id="73bf5-275">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="73bf5-276">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="73bf5-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="73bf5-277">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="73bf5-278">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="73bf5-278">*appsettings.json*</span></span>
1. <span data-ttu-id="73bf5-279">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="73bf5-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="73bf5-280">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="73bf5-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="73bf5-281">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="73bf5-282">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="73bf5-283">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="73bf5-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="73bf5-284">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="73bf5-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="73bf5-285">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="73bf5-285">Connection string prefixes</span></span>

<span data-ttu-id="73bf5-286">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="73bf5-287">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="73bf5-288">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="73bf5-289">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="73bf5-289">Connection string prefix</span></span> | <span data-ttu-id="73bf5-290">Поставщик</span><span class="sxs-lookup"><span data-stu-id="73bf5-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="73bf5-291">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="73bf5-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="73bf5-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="73bf5-293">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="73bf5-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="73bf5-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="73bf5-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="73bf5-295">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="73bf5-296">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="73bf5-297">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="73bf5-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="73bf5-298">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-298">Environment variable key</span></span> | <span data-ttu-id="73bf5-299">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-299">Converted configuration key</span></span> | <span data-ttu-id="73bf5-300">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="73bf5-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-301">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="73bf5-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-302">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-303">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-304">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-305">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-306">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-307">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="73bf5-308">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-308">File configuration provider</span></span>

<span data-ttu-id="73bf5-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="73bf5-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="73bf5-310">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-310">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="73bf5-311">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="73bf5-311">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="73bf5-312">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="73bf5-312">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="73bf5-313">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="73bf5-313">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="73bf5-314">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="73bf5-314">INI configuration provider</span></span>

<span data-ttu-id="73bf5-315"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-315">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-316">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-316">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="73bf5-317">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-317">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="73bf5-318">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-318">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="73bf5-319">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-319">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="73bf5-320">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-320">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="73bf5-321">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-321">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="73bf5-322">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="73bf5-322">JSON configuration provider</span></span>

<span data-ttu-id="73bf5-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="73bf5-324">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="73bf5-324">Overloads can specify:</span></span>

* <span data-ttu-id="73bf5-325">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="73bf5-325">Whether the file is optional.</span></span>
* <span data-ttu-id="73bf5-326">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="73bf5-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="73bf5-327">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="73bf5-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="73bf5-328">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="73bf5-328">The preceding code:</span></span>

* <span data-ttu-id="73bf5-329">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="73bf5-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="73bf5-330">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="73bf5-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="73bf5-331">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="73bf5-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="73bf5-332">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="73bf5-333">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="73bf5-334">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="73bf5-335">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="73bf5-336">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="73bf5-337">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="73bf5-338">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="73bf5-339">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="73bf5-340">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="73bf5-341">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="73bf5-341">XML configuration provider</span></span>

<span data-ttu-id="73bf5-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-343">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="73bf5-344">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="73bf5-345">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-345">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="73bf5-346">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="73bf5-347">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="73bf5-348">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-349">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="73bf5-350">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="73bf5-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-351">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="73bf5-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="73bf5-352">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73bf5-353">key:attribute</span><span class="sxs-lookup"><span data-stu-id="73bf5-353">key:attribute</span></span>
* <span data-ttu-id="73bf5-354">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="73bf5-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="73bf5-355">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="73bf5-356"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="73bf5-357">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-357">The key is the file name.</span></span> <span data-ttu-id="73bf5-358">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-358">The value contains the file's contents.</span></span> <span data-ttu-id="73bf5-359">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="73bf5-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="73bf5-360">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="73bf5-361">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="73bf5-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="73bf5-362">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="73bf5-363">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="73bf5-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="73bf5-364">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="73bf5-365">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="73bf5-366">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="73bf5-367">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="73bf5-368">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-368">Memory configuration provider</span></span>

<span data-ttu-id="73bf5-369"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="73bf5-370">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="73bf5-371">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-372">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="73bf5-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="73bf5-373">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="73bf5-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="73bf5-374">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="73bf5-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="73bf5-375">GetValue</span></span>

<span data-ttu-id="73bf5-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="73bf5-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-377">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="73bf5-378">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="73bf5-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="73bf5-379">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="73bf5-380">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="73bf5-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="73bf5-381">GetSection</span></span>

<span data-ttu-id="73bf5-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="73bf5-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="73bf5-383">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-384">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-385">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="73bf5-386">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="73bf5-387">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="73bf5-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="73bf5-388"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="73bf5-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="73bf5-389">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="73bf5-389">GetChildren and Exists</span></span>

<span data-ttu-id="73bf5-390">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-391">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="73bf5-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="73bf5-392">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="73bf5-392">Bind an array</span></span>

<span data-ttu-id="73bf5-393">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73bf5-394">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="73bf5-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="73bf5-395">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="73bf5-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="73bf5-396">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="73bf5-397">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="73bf5-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-398">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="73bf5-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="73bf5-399">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="73bf5-400">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="73bf5-401">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="73bf5-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="73bf5-402">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="73bf5-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="73bf5-403">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="73bf5-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-404">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="73bf5-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="73bf5-405">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="73bf5-406">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="73bf5-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="73bf5-407">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="73bf5-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="73bf5-408">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="73bf5-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="73bf5-409">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="73bf5-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="73bf5-410">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="73bf5-411">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="73bf5-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-412">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="73bf5-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="73bf5-413">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="73bf5-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="73bf5-414">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-414">Custom configuration provider</span></span>

<span data-ttu-id="73bf5-415">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="73bf5-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="73bf5-416">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="73bf5-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="73bf5-417">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="73bf5-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="73bf5-418">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="73bf5-419">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="73bf5-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="73bf5-420">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="73bf5-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="73bf5-421">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="73bf5-422">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="73bf5-423">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="73bf5-424">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="73bf5-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="73bf5-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="73bf5-426">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="73bf5-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="73bf5-428">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="73bf5-429">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="73bf5-430">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="73bf5-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="73bf5-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="73bf5-432">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="73bf5-433">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="73bf5-434">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="73bf5-435">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="73bf5-435">Access configuration in Startup</span></span>

<span data-ttu-id="73bf5-436">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="73bf5-437">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="73bf5-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="73bf5-438">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="73bf5-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="73bf5-439">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="73bf5-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="73bf5-440">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="73bf5-441">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="73bf5-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="73bf5-442">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="73bf5-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="73bf5-443">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="73bf5-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="73bf5-444">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="73bf5-444">Configure options with a delegate</span></span>

<span data-ttu-id="73bf5-445">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="73bf5-446">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="73bf5-447">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="73bf5-448">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="73bf5-449">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="73bf5-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="73bf5-450">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json*, а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="73bf5-451">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="73bf5-451">Host versus app configuration</span></span>

<span data-ttu-id="73bf5-452">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="73bf5-453">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="73bf5-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="73bf5-454">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="73bf5-455">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="73bf5-456">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="73bf5-457">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="73bf5-457">Default host configuration</span></span>

<span data-ttu-id="73bf5-458">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="73bf5-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="73bf5-459">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="73bf5-460">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="73bf5-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="73bf5-461">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="73bf5-462">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="73bf5-463">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="73bf5-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="73bf5-464">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="73bf5-465">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="73bf5-466">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="73bf5-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="73bf5-467">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="73bf5-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="73bf5-468">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="73bf5-468">Other configuration</span></span>

<span data-ttu-id="73bf5-469">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="73bf5-470">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="73bf5-471">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="73bf5-472">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="73bf5-473">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="73bf5-474">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="73bf5-475">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="73bf5-475">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="73bf5-476">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-476">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="73bf5-477">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="73bf5-477">Add configuration from an external assembly</span></span>

<span data-ttu-id="73bf5-478">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-478">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="73bf5-479">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-479">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73bf5-480">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="73bf5-480">Additional resources</span></span>

* [<span data-ttu-id="73bf5-481">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-481">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73bf5-482">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-482">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="73bf5-483">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="73bf5-483">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="73bf5-484">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-484">Azure Key Vault</span></span>
* <span data-ttu-id="73bf5-485">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-485">Azure App Configuration</span></span>
* <span data-ttu-id="73bf5-486">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="73bf5-486">Command-line arguments</span></span>
* <span data-ttu-id="73bf5-487">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="73bf5-487">Custom providers (installed or created)</span></span>
* <span data-ttu-id="73bf5-488">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="73bf5-488">Directory files</span></span>
* <span data-ttu-id="73bf5-489">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-489">Environment variables</span></span>
* <span data-ttu-id="73bf5-490">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="73bf5-490">In-memory .NET objects</span></span>
* <span data-ttu-id="73bf5-491">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="73bf5-491">Settings files</span></span>

<span data-ttu-id="73bf5-492">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="73bf5-492">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="73bf5-493">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="73bf5-493">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="73bf5-494">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-494">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="73bf5-495">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="73bf5-495">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="73bf5-496">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-496">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="73bf5-497">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73bf5-497">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="73bf5-498">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="73bf5-498">Host versus app configuration</span></span>

<span data-ttu-id="73bf5-499">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-499">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="73bf5-500">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="73bf5-500">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="73bf5-501">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-501">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="73bf5-502">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-502">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="73bf5-503">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-503">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="73bf5-504">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="73bf5-504">Other configuration</span></span>

<span data-ttu-id="73bf5-505">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-505">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="73bf5-506">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-506">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="73bf5-507">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-507">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="73bf5-508">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-508">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="73bf5-509">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-509">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="73bf5-510">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="73bf5-510">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="73bf5-511">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-511">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="73bf5-512">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="73bf5-512">Default configuration</span></span>

<span data-ttu-id="73bf5-513">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-513">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="73bf5-514">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="73bf5-514">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="73bf5-515">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="73bf5-515">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="73bf5-516">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="73bf5-516">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="73bf5-517">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-517">Host configuration is provided from:</span></span>
  * <span data-ttu-id="73bf5-518">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-518">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="73bf5-519">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-519">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="73bf5-520">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-520">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="73bf5-521">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="73bf5-521">App configuration is provided from:</span></span>
  * <span data-ttu-id="73bf5-522">Файл *appsettings.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-522">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73bf5-523">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-523">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73bf5-524">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="73bf5-524">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="73bf5-525">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-525">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="73bf5-526">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73bf5-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="73bf5-527">Безопасность</span><span class="sxs-lookup"><span data-stu-id="73bf5-527">Security</span></span>

<span data-ttu-id="73bf5-528">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-528">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="73bf5-529">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="73bf5-529">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="73bf5-530">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="73bf5-530">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="73bf5-531">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-531">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="73bf5-532">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="73bf5-532">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="73bf5-533"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-533"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="73bf5-534">Менеджер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-534">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="73bf5-535">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="73bf5-535">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="73bf5-536">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73bf5-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="73bf5-537">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-537">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="73bf5-538">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-538">Hierarchical configuration data</span></span>

<span data-ttu-id="73bf5-539">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-539">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="73bf5-540">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-540">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="73bf5-541">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-541">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="73bf5-542">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="73bf5-542">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="73bf5-543">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-543">section0:key0</span></span>
* <span data-ttu-id="73bf5-544">section0:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-544">section0:key1</span></span>
* <span data-ttu-id="73bf5-545">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-545">section1:key0</span></span>
* <span data-ttu-id="73bf5-546">section1:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-546">section1:key1</span></span>

<span data-ttu-id="73bf5-547">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="73bf5-548">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="73bf5-548">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="73bf5-549">Соглашения</span><span class="sxs-lookup"><span data-stu-id="73bf5-549">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="73bf5-550">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="73bf5-550">Sources and providers</span></span>

<span data-ttu-id="73bf5-551">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-551">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="73bf5-552">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="73bf5-552">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="73bf5-553">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="73bf5-553">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="73bf5-554">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="73bf5-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="73bf5-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="73bf5-556">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-556">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="73bf5-557">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-557">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="73bf5-558">Клавиши</span><span class="sxs-lookup"><span data-stu-id="73bf5-558">Keys</span></span>

<span data-ttu-id="73bf5-559">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-559">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="73bf5-560">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-560">Keys are case-insensitive.</span></span> <span data-ttu-id="73bf5-561">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-561">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="73bf5-562">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="73bf5-562">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="73bf5-563">Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="73bf5-563">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="73bf5-564">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="73bf5-564">Hierarchical keys</span></span>
  * <span data-ttu-id="73bf5-565">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="73bf5-565">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="73bf5-566">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="73bf5-566">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="73bf5-567">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="73bf5-567">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="73bf5-568">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="73bf5-568">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="73bf5-569">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="73bf5-569">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="73bf5-570"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-570">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73bf5-571">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="73bf5-571">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="73bf5-572">Значения</span><span class="sxs-lookup"><span data-stu-id="73bf5-572">Values</span></span>

<span data-ttu-id="73bf5-573">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-573">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="73bf5-574">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="73bf5-574">Values are strings.</span></span>
* <span data-ttu-id="73bf5-575">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="73bf5-575">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="73bf5-576">Поставщики</span><span class="sxs-lookup"><span data-stu-id="73bf5-576">Providers</span></span>

<span data-ttu-id="73bf5-577">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="73bf5-577">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="73bf5-578">Поставщик</span><span class="sxs-lookup"><span data-stu-id="73bf5-578">Provider</span></span> | <span data-ttu-id="73bf5-579">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="73bf5-579">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="73bf5-580">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="73bf5-580">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="73bf5-581">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-581">Azure Key Vault</span></span> |
| <span data-ttu-id="73bf5-582">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="73bf5-582">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="73bf5-583">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="73bf5-583">Azure App Configuration</span></span> |
| [<span data-ttu-id="73bf5-584">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-584">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="73bf5-585">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-585">Command-line parameters</span></span> |
| [<span data-ttu-id="73bf5-586">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-586">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="73bf5-587">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-587">Custom source</span></span> |
| [<span data-ttu-id="73bf5-588">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-588">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="73bf5-589">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-589">Environment variables</span></span> |
| [<span data-ttu-id="73bf5-590">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-590">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="73bf5-591">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="73bf5-591">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="73bf5-592">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-592">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="73bf5-593">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="73bf5-593">Directory files</span></span> |
| [<span data-ttu-id="73bf5-594">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-594">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="73bf5-595">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-595">In-memory collections</span></span> |
| <span data-ttu-id="73bf5-596">[Секреты пользователей (Менеджер секретов)](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="73bf5-596">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="73bf5-597">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-597">File in the user profile directory</span></span> |

<span data-ttu-id="73bf5-598">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-598">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="73bf5-599">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="73bf5-599">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="73bf5-600">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="73bf5-600">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="73bf5-601">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-601">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="73bf5-602">Файлы (*appsettings.json*, *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="73bf5-602">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="73bf5-603">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="73bf5-603">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="73bf5-604">[Секреты пользователя (Менеджер секретов)](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="73bf5-604">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="73bf5-605">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-605">Environment variables</span></span>
1. <span data-ttu-id="73bf5-606">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="73bf5-606">Command-line arguments</span></span>

<span data-ttu-id="73bf5-607">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="73bf5-607">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="73bf5-608">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-608">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73bf5-609">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="73bf5-609">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="73bf5-610">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="73bf5-610">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="73bf5-611">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="73bf5-611">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="73bf5-612">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="73bf5-612">ConfigureAppConfiguration</span></span>

<span data-ttu-id="73bf5-613">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-613">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="73bf5-614">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-614">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="73bf5-615">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="73bf5-615">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="73bf5-616">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="73bf5-616">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="73bf5-617">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="73bf5-617">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="73bf5-618">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="73bf5-618">Consume configuration during app startup</span></span>

<span data-ttu-id="73bf5-619">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-619">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="73bf5-620">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="73bf5-620">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="73bf5-621">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="73bf5-621">Command-line Configuration Provider</span></span>

<span data-ttu-id="73bf5-622"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-622">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-623">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-623">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73bf5-624">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-624">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="73bf5-625">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="73bf5-625">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73bf5-626">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="73bf5-626">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73bf5-627">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="73bf5-627">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="73bf5-628">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-628">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73bf5-629">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-629">Environment variables.</span></span>

<span data-ttu-id="73bf5-630">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-630">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="73bf5-631">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="73bf5-631">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="73bf5-632">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="73bf5-632">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="73bf5-633">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-633">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="73bf5-634">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-634">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73bf5-635">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="73bf5-635">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="73bf5-636">**Пример**</span><span class="sxs-lookup"><span data-stu-id="73bf5-636">**Example**</span></span>

<span data-ttu-id="73bf5-637">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-637">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="73bf5-638">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="73bf5-638">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="73bf5-639">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-639">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="73bf5-640">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-640">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73bf5-641">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-641">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="73bf5-642">Аргументы</span><span class="sxs-lookup"><span data-stu-id="73bf5-642">Arguments</span></span>

<span data-ttu-id="73bf5-643">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-643">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="73bf5-644">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-644">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="73bf5-645">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="73bf5-645">Key prefix</span></span>               | <span data-ttu-id="73bf5-646">Пример</span><span class="sxs-lookup"><span data-stu-id="73bf5-646">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="73bf5-647">Без префикса</span><span class="sxs-lookup"><span data-stu-id="73bf5-647">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="73bf5-648">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="73bf5-648">Two dashes (`--`)</span></span>        | <span data-ttu-id="73bf5-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="73bf5-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="73bf5-650">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="73bf5-650">Forward slash (`/`)</span></span>      | <span data-ttu-id="73bf5-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="73bf5-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="73bf5-652">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="73bf5-652">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="73bf5-653">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="73bf5-653">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="73bf5-654">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="73bf5-654">Switch mappings</span></span>

<span data-ttu-id="73bf5-655">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="73bf5-655">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="73bf5-656">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="73bf5-656">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="73bf5-657">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-657">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="73bf5-658">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-658">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="73bf5-659">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-659">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="73bf5-660">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="73bf5-660">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="73bf5-661">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-661">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="73bf5-662">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-662">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="73bf5-663">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="73bf5-663">Create a switch mappings dictionary.</span></span> <span data-ttu-id="73bf5-664">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="73bf5-664">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="73bf5-665">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="73bf5-665">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="73bf5-666">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="73bf5-666">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="73bf5-667">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-667">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73bf5-668">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="73bf5-668">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="73bf5-669">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="73bf5-669">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="73bf5-670">Ключ</span><span class="sxs-lookup"><span data-stu-id="73bf5-670">Key</span></span>       | <span data-ttu-id="73bf5-671">Значение</span><span class="sxs-lookup"><span data-stu-id="73bf5-671">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="73bf5-672">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="73bf5-672">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="73bf5-673">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="73bf5-673">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="73bf5-674">Ключ</span><span class="sxs-lookup"><span data-stu-id="73bf5-674">Key</span></span>               | <span data-ttu-id="73bf5-675">Значение</span><span class="sxs-lookup"><span data-stu-id="73bf5-675">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="73bf5-676">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-676">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="73bf5-677"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-677">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-678">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-678">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="73bf5-679">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="73bf5-680">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="73bf5-680">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="73bf5-681">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-681">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="73bf5-682">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="73bf5-682">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73bf5-683">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="73bf5-683">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73bf5-684">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="73bf5-684">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="73bf5-685">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="73bf5-685">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="73bf5-686">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-686">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73bf5-687">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-687">Command-line arguments.</span></span>

<span data-ttu-id="73bf5-688">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-688">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="73bf5-689">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-689">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="73bf5-690">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="73bf5-690">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="73bf5-691">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="73bf5-691">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="73bf5-692">**Пример**</span><span class="sxs-lookup"><span data-stu-id="73bf5-692">**Example**</span></span>

<span data-ttu-id="73bf5-693">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-693">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="73bf5-694">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-694">Run the sample app.</span></span> <span data-ttu-id="73bf5-695">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-695">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73bf5-696">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-696">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="73bf5-697">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-697">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="73bf5-698">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-698">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="73bf5-699">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-699">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="73bf5-700">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="73bf5-700">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="73bf5-701">Префиксы</span><span class="sxs-lookup"><span data-stu-id="73bf5-701">Prefixes</span></span>

<span data-ttu-id="73bf5-702">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-702">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="73bf5-703">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-703">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="73bf5-704">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="73bf5-704">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="73bf5-705">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-705">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="73bf5-706">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="73bf5-706">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73bf5-707">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="73bf5-707">**Connection string prefixes**</span></span>

<span data-ttu-id="73bf5-708">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-708">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="73bf5-709">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="73bf5-709">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="73bf5-710">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="73bf5-710">Connection string prefix</span></span> | <span data-ttu-id="73bf5-711">Поставщик</span><span class="sxs-lookup"><span data-stu-id="73bf5-711">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="73bf5-712">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="73bf5-712">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="73bf5-713">MySQL</span><span class="sxs-lookup"><span data-stu-id="73bf5-713">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="73bf5-714">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="73bf5-714">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="73bf5-715">SQL Server</span><span class="sxs-lookup"><span data-stu-id="73bf5-715">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="73bf5-716">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-716">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="73bf5-717">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="73bf5-717">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="73bf5-718">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="73bf5-718">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="73bf5-719">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="73bf5-719">Environment variable key</span></span> | <span data-ttu-id="73bf5-720">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-720">Converted configuration key</span></span> | <span data-ttu-id="73bf5-721">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="73bf5-721">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-722">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="73bf5-722">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-723">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-723">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-724">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-724">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-725">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-726">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-726">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="73bf5-727">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="73bf5-728">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73bf5-728">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="73bf5-729">**Пример**</span><span class="sxs-lookup"><span data-stu-id="73bf5-729">**Example**</span></span>

<span data-ttu-id="73bf5-730">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="73bf5-730">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="73bf5-731">Имя: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="73bf5-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="73bf5-732">Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="73bf5-732">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="73bf5-733">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="73bf5-733">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="73bf5-734">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-734">File Configuration Provider</span></span>

<span data-ttu-id="73bf5-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="73bf5-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="73bf5-736">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="73bf5-736">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="73bf5-737">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="73bf5-737">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="73bf5-738">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="73bf5-738">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="73bf5-739">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="73bf5-739">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="73bf5-740">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="73bf5-740">INI Configuration Provider</span></span>

<span data-ttu-id="73bf5-741"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-741">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-742">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-742">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73bf5-743">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="73bf5-743">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="73bf5-744">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-744">Overloads permit specifying:</span></span>

* <span data-ttu-id="73bf5-745">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="73bf5-745">Whether the file is optional.</span></span>
* <span data-ttu-id="73bf5-746">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="73bf5-746">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73bf5-747"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-747">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73bf5-748">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-748">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73bf5-749">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-749">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="73bf5-750">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-750">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73bf5-751">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-751">section0:key0</span></span>
* <span data-ttu-id="73bf5-752">section0:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-752">section0:key1</span></span>
* <span data-ttu-id="73bf5-753">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="73bf5-753">section1:subsection:key</span></span>
* <span data-ttu-id="73bf5-754">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="73bf5-754">section2:subsection0:key</span></span>
* <span data-ttu-id="73bf5-755">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="73bf5-755">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="73bf5-756">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="73bf5-756">JSON Configuration Provider</span></span>

<span data-ttu-id="73bf5-757"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-757">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="73bf5-758">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-758">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73bf5-759">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-759">Overloads permit specifying:</span></span>

* <span data-ttu-id="73bf5-760">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="73bf5-760">Whether the file is optional.</span></span>
* <span data-ttu-id="73bf5-761">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="73bf5-761">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73bf5-762"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-762">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73bf5-763">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-763">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73bf5-764">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="73bf5-764">The method is called to load configuration from:</span></span>

* <span data-ttu-id="73bf5-765">*appsettings.json*: Этот файл считывается первым.</span><span class="sxs-lookup"><span data-stu-id="73bf5-765">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="73bf5-766">Версия файла среды может переопределить значения, предоставленные *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-766">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="73bf5-767">*appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="73bf5-767">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="73bf5-768">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="73bf5-768">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73bf5-769">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="73bf5-769">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73bf5-770">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="73bf5-770">Environment variables.</span></span>
* <span data-ttu-id="73bf5-771">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-771">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73bf5-772">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-772">Command-line arguments.</span></span>

<span data-ttu-id="73bf5-773">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-773">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="73bf5-774">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-774">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="73bf5-775">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-775">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73bf5-776">**Пример**</span><span class="sxs-lookup"><span data-stu-id="73bf5-776">**Example**</span></span>

<span data-ttu-id="73bf5-777">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-777">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="73bf5-778">Первый вызов `AddJsonFile` загружает конфигурацию из *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-778">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="73bf5-779">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-779">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="73bf5-780">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="73bf5-780">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="73bf5-781">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-781">Run the sample app.</span></span> <span data-ttu-id="73bf5-782">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-782">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73bf5-783">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-783">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="73bf5-784">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="73bf5-784">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="73bf5-785">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="73bf5-785">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="73bf5-786">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-786">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="73bf5-787">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-787">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="73bf5-788">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="73bf5-788">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="73bf5-789">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-789">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="73bf5-790">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-790">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="73bf5-791">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="73bf5-791">XML Configuration Provider</span></span>

<span data-ttu-id="73bf5-792"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-792">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="73bf5-793">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-793">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73bf5-794">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-794">Overloads permit specifying:</span></span>

* <span data-ttu-id="73bf5-795">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="73bf5-795">Whether the file is optional.</span></span>
* <span data-ttu-id="73bf5-796">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="73bf5-796">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73bf5-797"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-797">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73bf5-798">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-798">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="73bf5-799">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="73bf5-799">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="73bf5-800">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-800">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73bf5-801">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-801">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="73bf5-802">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-802">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73bf5-803">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-803">section0:key0</span></span>
* <span data-ttu-id="73bf5-804">section0:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-804">section0:key1</span></span>
* <span data-ttu-id="73bf5-805">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-805">section1:key0</span></span>
* <span data-ttu-id="73bf5-806">section1:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-806">section1:key1</span></span>

<span data-ttu-id="73bf5-807">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-807">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="73bf5-808">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73bf5-809">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-809">section:section0:key:key0</span></span>
* <span data-ttu-id="73bf5-810">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-810">section:section0:key:key1</span></span>
* <span data-ttu-id="73bf5-811">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-811">section:section1:key:key0</span></span>
* <span data-ttu-id="73bf5-812">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-812">section:section1:key:key1</span></span>

<span data-ttu-id="73bf5-813">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="73bf5-813">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="73bf5-814">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73bf5-815">key:attribute</span><span class="sxs-lookup"><span data-stu-id="73bf5-815">key:attribute</span></span>
* <span data-ttu-id="73bf5-816">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="73bf5-816">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="73bf5-817">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="73bf5-817">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="73bf5-818"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-818">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="73bf5-819">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-819">The key is the file name.</span></span> <span data-ttu-id="73bf5-820">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="73bf5-820">The value contains the file's contents.</span></span> <span data-ttu-id="73bf5-821">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="73bf5-821">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="73bf5-822">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-822">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="73bf5-823">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="73bf5-823">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="73bf5-824">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="73bf5-824">Overloads permit specifying:</span></span>

* <span data-ttu-id="73bf5-825">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="73bf5-825">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="73bf5-826">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-826">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="73bf5-827">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-827">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="73bf5-828">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-828">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="73bf5-829">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-829">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="73bf5-830">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="73bf5-830">Memory Configuration Provider</span></span>

<span data-ttu-id="73bf5-831"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-831">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="73bf5-832">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-832">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73bf5-833">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-833">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="73bf5-834">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="73bf5-835">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-835">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="73bf5-836">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="73bf5-836">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="73bf5-837">GetValue</span><span class="sxs-lookup"><span data-stu-id="73bf5-837">GetValue</span></span>

<span data-ttu-id="73bf5-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="73bf5-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="73bf5-839">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="73bf5-839">An overload accepts a default value.</span></span>

<span data-ttu-id="73bf5-840">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="73bf5-840">The following example:</span></span>

* <span data-ttu-id="73bf5-841">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-841">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="73bf5-842">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-842">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="73bf5-843">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-843">Types the value as an `int`.</span></span>
* <span data-ttu-id="73bf5-844">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="73bf5-844">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="73bf5-845">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="73bf5-845">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="73bf5-846">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="73bf5-846">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="73bf5-847">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="73bf5-847">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="73bf5-848">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-848">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="73bf5-849">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-849">section0:key0</span></span>
* <span data-ttu-id="73bf5-850">section0:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-850">section0:key1</span></span>
* <span data-ttu-id="73bf5-851">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-851">section1:key0</span></span>
* <span data-ttu-id="73bf5-852">section1:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-852">section1:key1</span></span>
* <span data-ttu-id="73bf5-853">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-853">section2:subsection0:key0</span></span>
* <span data-ttu-id="73bf5-854">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-854">section2:subsection0:key1</span></span>
* <span data-ttu-id="73bf5-855">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="73bf5-855">section2:subsection1:key0</span></span>
* <span data-ttu-id="73bf5-856">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="73bf5-856">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="73bf5-857">GetSection</span><span class="sxs-lookup"><span data-stu-id="73bf5-857">GetSection</span></span>

<span data-ttu-id="73bf5-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="73bf5-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="73bf5-859">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="73bf5-859">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="73bf5-860">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="73bf5-860">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="73bf5-861">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="73bf5-861">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="73bf5-862">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-862">`GetSection` never returns `null`.</span></span> <span data-ttu-id="73bf5-863">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-863">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="73bf5-864">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="73bf5-864">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="73bf5-865"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="73bf5-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="73bf5-866">GetChildren</span><span class="sxs-lookup"><span data-stu-id="73bf5-866">GetChildren</span></span>

<span data-ttu-id="73bf5-867">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="73bf5-867">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="73bf5-868">Exists</span><span class="sxs-lookup"><span data-stu-id="73bf5-868">Exists</span></span>

<span data-ttu-id="73bf5-869">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-869">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="73bf5-870">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-870">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="73bf5-871">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="73bf5-871">Bind to an object graph</span></span>

<span data-ttu-id="73bf5-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="73bf5-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="73bf5-873">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="73bf5-873">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="73bf5-874">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="73bf5-874">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="73bf5-875">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-875">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="73bf5-876">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-876">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="73bf5-877">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="73bf5-877">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="73bf5-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="73bf5-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="73bf5-879">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-879">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="73bf5-880">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="73bf5-880">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="73bf5-881">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="73bf5-881">Bind an array to a class</span></span>

<span data-ttu-id="73bf5-882">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="73bf5-882">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="73bf5-883"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-883">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73bf5-884">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="73bf5-884">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="73bf5-885">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="73bf5-885">Binding is provided by convention.</span></span> <span data-ttu-id="73bf5-886">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="73bf5-886">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="73bf5-887">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="73bf5-887">**In-memory array processing**</span></span>

<span data-ttu-id="73bf5-888">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="73bf5-888">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="73bf5-889">Ключ</span><span class="sxs-lookup"><span data-stu-id="73bf5-889">Key</span></span>             | <span data-ttu-id="73bf5-890">Значение</span><span class="sxs-lookup"><span data-stu-id="73bf5-890">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="73bf5-891">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="73bf5-891">array:entries:0</span></span> | <span data-ttu-id="73bf5-892">value0</span><span class="sxs-lookup"><span data-stu-id="73bf5-892">value0</span></span> |
| <span data-ttu-id="73bf5-893">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="73bf5-893">array:entries:1</span></span> | <span data-ttu-id="73bf5-894">value1</span><span class="sxs-lookup"><span data-stu-id="73bf5-894">value1</span></span> |
| <span data-ttu-id="73bf5-895">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="73bf5-895">array:entries:2</span></span> | <span data-ttu-id="73bf5-896">value2</span><span class="sxs-lookup"><span data-stu-id="73bf5-896">value2</span></span> |
| <span data-ttu-id="73bf5-897">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="73bf5-897">array:entries:4</span></span> | <span data-ttu-id="73bf5-898">value4</span><span class="sxs-lookup"><span data-stu-id="73bf5-898">value4</span></span> |
| <span data-ttu-id="73bf5-899">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="73bf5-899">array:entries:5</span></span> | <span data-ttu-id="73bf5-900">value5</span><span class="sxs-lookup"><span data-stu-id="73bf5-900">value5</span></span> |

<span data-ttu-id="73bf5-901">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="73bf5-901">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="73bf5-902">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="73bf5-902">The array skips a value for index &num;3.</span></span> <span data-ttu-id="73bf5-903">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="73bf5-903">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="73bf5-904">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-904">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="73bf5-905">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="73bf5-905">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="73bf5-906">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="73bf5-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="73bf5-907">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-907">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="73bf5-908">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="73bf5-908">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="73bf5-909">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="73bf5-909">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="73bf5-910">0</span><span class="sxs-lookup"><span data-stu-id="73bf5-910">0</span></span>                            | <span data-ttu-id="73bf5-911">value0</span><span class="sxs-lookup"><span data-stu-id="73bf5-911">value0</span></span>                       |
| <span data-ttu-id="73bf5-912">1</span><span class="sxs-lookup"><span data-stu-id="73bf5-912">1</span></span>                            | <span data-ttu-id="73bf5-913">value1</span><span class="sxs-lookup"><span data-stu-id="73bf5-913">value1</span></span>                       |
| <span data-ttu-id="73bf5-914">2</span><span class="sxs-lookup"><span data-stu-id="73bf5-914">2</span></span>                            | <span data-ttu-id="73bf5-915">value2</span><span class="sxs-lookup"><span data-stu-id="73bf5-915">value2</span></span>                       |
| <span data-ttu-id="73bf5-916">3</span><span class="sxs-lookup"><span data-stu-id="73bf5-916">3</span></span>                            | <span data-ttu-id="73bf5-917">value4</span><span class="sxs-lookup"><span data-stu-id="73bf5-917">value4</span></span>                       |
| <span data-ttu-id="73bf5-918">4</span><span class="sxs-lookup"><span data-stu-id="73bf5-918">4</span></span>                            | <span data-ttu-id="73bf5-919">value5</span><span class="sxs-lookup"><span data-stu-id="73bf5-919">value5</span></span>                       |

<span data-ttu-id="73bf5-920">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-920">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="73bf5-921">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="73bf5-921">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="73bf5-922">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="73bf5-922">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="73bf5-923">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-923">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="73bf5-924">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-924">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="73bf5-925">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-925">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="73bf5-926">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="73bf5-926">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="73bf5-927">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-927">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="73bf5-928">Ключ</span><span class="sxs-lookup"><span data-stu-id="73bf5-928">Key</span></span>             | <span data-ttu-id="73bf5-929">Значение</span><span class="sxs-lookup"><span data-stu-id="73bf5-929">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="73bf5-930">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="73bf5-930">array:entries:3</span></span> | <span data-ttu-id="73bf5-931">value3</span><span class="sxs-lookup"><span data-stu-id="73bf5-931">value3</span></span> |

<span data-ttu-id="73bf5-932">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="73bf5-932">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="73bf5-933">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="73bf5-933">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="73bf5-934">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="73bf5-934">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="73bf5-935">0</span><span class="sxs-lookup"><span data-stu-id="73bf5-935">0</span></span>                            | <span data-ttu-id="73bf5-936">value0</span><span class="sxs-lookup"><span data-stu-id="73bf5-936">value0</span></span>                       |
| <span data-ttu-id="73bf5-937">1</span><span class="sxs-lookup"><span data-stu-id="73bf5-937">1</span></span>                            | <span data-ttu-id="73bf5-938">value1</span><span class="sxs-lookup"><span data-stu-id="73bf5-938">value1</span></span>                       |
| <span data-ttu-id="73bf5-939">2</span><span class="sxs-lookup"><span data-stu-id="73bf5-939">2</span></span>                            | <span data-ttu-id="73bf5-940">value2</span><span class="sxs-lookup"><span data-stu-id="73bf5-940">value2</span></span>                       |
| <span data-ttu-id="73bf5-941">3</span><span class="sxs-lookup"><span data-stu-id="73bf5-941">3</span></span>                            | <span data-ttu-id="73bf5-942">value3</span><span class="sxs-lookup"><span data-stu-id="73bf5-942">value3</span></span>                       |
| <span data-ttu-id="73bf5-943">4</span><span class="sxs-lookup"><span data-stu-id="73bf5-943">4</span></span>                            | <span data-ttu-id="73bf5-944">value4</span><span class="sxs-lookup"><span data-stu-id="73bf5-944">value4</span></span>                       |
| <span data-ttu-id="73bf5-945">5</span><span class="sxs-lookup"><span data-stu-id="73bf5-945">5</span></span>                            | <span data-ttu-id="73bf5-946">value5</span><span class="sxs-lookup"><span data-stu-id="73bf5-946">value5</span></span>                       |

<span data-ttu-id="73bf5-947">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="73bf5-947">**JSON array processing**</span></span>

<span data-ttu-id="73bf5-948">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="73bf5-948">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="73bf5-949">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="73bf5-949">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="73bf5-950">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="73bf5-950">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="73bf5-951">Ключ</span><span class="sxs-lookup"><span data-stu-id="73bf5-951">Key</span></span>                     | <span data-ttu-id="73bf5-952">Значение</span><span class="sxs-lookup"><span data-stu-id="73bf5-952">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="73bf5-953">json_array:key</span><span class="sxs-lookup"><span data-stu-id="73bf5-953">json_array:key</span></span>          | <span data-ttu-id="73bf5-954">valueA</span><span class="sxs-lookup"><span data-stu-id="73bf5-954">valueA</span></span> |
| <span data-ttu-id="73bf5-955">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="73bf5-955">json_array:subsection:0</span></span> | <span data-ttu-id="73bf5-956">valueB</span><span class="sxs-lookup"><span data-stu-id="73bf5-956">valueB</span></span> |
| <span data-ttu-id="73bf5-957">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="73bf5-957">json_array:subsection:1</span></span> | <span data-ttu-id="73bf5-958">valueC</span><span class="sxs-lookup"><span data-stu-id="73bf5-958">valueC</span></span> |
| <span data-ttu-id="73bf5-959">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="73bf5-959">json_array:subsection:2</span></span> | <span data-ttu-id="73bf5-960">valueD</span><span class="sxs-lookup"><span data-stu-id="73bf5-960">valueD</span></span> |

<span data-ttu-id="73bf5-961">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="73bf5-961">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="73bf5-962">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-962">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="73bf5-963">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-963">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="73bf5-964">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="73bf5-964">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="73bf5-965">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="73bf5-965">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="73bf5-966">0</span><span class="sxs-lookup"><span data-stu-id="73bf5-966">0</span></span>                                   | <span data-ttu-id="73bf5-967">valueB</span><span class="sxs-lookup"><span data-stu-id="73bf5-967">valueB</span></span>                              |
| <span data-ttu-id="73bf5-968">1</span><span class="sxs-lookup"><span data-stu-id="73bf5-968">1</span></span>                                   | <span data-ttu-id="73bf5-969">valueC</span><span class="sxs-lookup"><span data-stu-id="73bf5-969">valueC</span></span>                              |
| <span data-ttu-id="73bf5-970">2</span><span class="sxs-lookup"><span data-stu-id="73bf5-970">2</span></span>                                   | <span data-ttu-id="73bf5-971">valueD</span><span class="sxs-lookup"><span data-stu-id="73bf5-971">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="73bf5-972">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="73bf5-972">Custom configuration provider</span></span>

<span data-ttu-id="73bf5-973">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="73bf5-973">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="73bf5-974">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="73bf5-974">The provider has the following characteristics:</span></span>

* <span data-ttu-id="73bf5-975">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="73bf5-975">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="73bf5-976">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="73bf5-976">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="73bf5-977">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="73bf5-977">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="73bf5-978">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="73bf5-978">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="73bf5-979">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="73bf5-979">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="73bf5-980">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-980">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="73bf5-981">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-981">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="73bf5-982">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="73bf5-982">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="73bf5-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="73bf5-984">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-984">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="73bf5-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="73bf5-986">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-986">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="73bf5-987">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="73bf5-987">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="73bf5-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="73bf5-989">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-989">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="73bf5-990">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="73bf5-990">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="73bf5-991">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="73bf5-991">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="73bf5-992">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="73bf5-992">Access configuration during startup</span></span>

<span data-ttu-id="73bf5-993">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-993">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="73bf5-994">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="73bf5-994">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="73bf5-995">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="73bf5-995">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="73bf5-996">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="73bf5-996">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="73bf5-997">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="73bf5-997">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="73bf5-998">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="73bf5-998">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="73bf5-999">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="73bf5-999">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="73bf5-1000">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="73bf5-1000">Add configuration from an external assembly</span></span>

<span data-ttu-id="73bf5-1001">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="73bf5-1001">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="73bf5-1002">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73bf5-1002">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73bf5-1003">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="73bf5-1003">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end