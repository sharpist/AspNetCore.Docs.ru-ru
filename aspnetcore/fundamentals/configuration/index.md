---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: 5a9ed8d6737352f56be78039a895a85f22dec361
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944648"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="2db1e-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="2db1e-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="2db1e-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="2db1e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2db1e-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="2db1e-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="2db1e-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="2db1e-107">файлы параметров, например *appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="2db1e-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="2db1e-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-108">Environment variables</span></span>
* <span data-ttu-id="2db1e-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-109">Azure Key Vault</span></span>
* <span data-ttu-id="2db1e-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-110">Azure App Configuration</span></span>
* <span data-ttu-id="2db1e-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="2db1e-111">Command-line arguments</span></span>
* <span data-ttu-id="2db1e-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="2db1e-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="2db1e-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="2db1e-113">Directory files</span></span>
* <span data-ttu-id="2db1e-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="2db1e-114">In-memory .NET objects</span></span>

<span data-ttu-id="2db1e-115">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2db1e-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="2db1e-116">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2db1e-116">Default configuration</span></span>

<span data-ttu-id="2db1e-117">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="2db1e-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="2db1e-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="2db1e-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="2db1e-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="2db1e-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="2db1e-120">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="2db1e-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="2db1e-121">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="2db1e-122">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="2db1e-123">Например, *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="2db1e-124">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="2db1e-125">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2db1e-126">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="2db1e-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="2db1e-127">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="2db1e-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="2db1e-128">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="2db1e-129">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="2db1e-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="2db1e-130">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="2db1e-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="2db1e-131">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="2db1e-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="2db1e-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="2db1e-132">appsettings.json</span></span>

<span data-ttu-id="2db1e-133">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2db1e-134">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-135"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="2db1e-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="2db1e-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2db1e-136">*appsettings.json*</span></span>
1. <span data-ttu-id="2db1e-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="2db1e-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2db1e-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="2db1e-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2db1e-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="2db1e-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="2db1e-141">For example, by default:</span></span>

* <span data-ttu-id="2db1e-142">В среде разработки конфигурация *appsettings*.***Development***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="2db1e-143">В рабочей среде конфигурация *appsettings*.***Production***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="2db1e-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="2db1e-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="2db1e-145">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="2db1e-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="2db1e-146">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="2db1e-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="2db1e-147">Изменения, внесенные в файл *appsettings.json* и *appsettings.* `Environment` *.json*, ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2db1e-148">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="2db1e-149">Безопасность и диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="2db1e-149">Security and secret manager</span></span>

<span data-ttu-id="2db1e-150">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="2db1e-151">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="2db1e-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="2db1e-152">Для хранения секретов во время разработки можно использовать [диспетчер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2db1e-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="2db1e-153">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="2db1e-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2db1e-154">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2db1e-155">[По умолчанию](#default) [диспетчер секретов](xref:security/app-secrets) считывает параметры конфигурации после *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="2db1e-156">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="2db1e-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2db1e-157"><xref:security/app-secrets>.  Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2db1e-158">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="2db1e-159">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2db1e-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2db1e-160">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="2db1e-161">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-161">Environment variables</span></span>

<span data-ttu-id="2db1e-162">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json*, *appsettings.* `Environment` *.json* и [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2db1e-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2db1e-163">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json*, *appsettings.* `Environment` *.json* и диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2db1e-164">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-164">The following `set` commands:</span></span>

* <span data-ttu-id="2db1e-165">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="2db1e-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="2db1e-166">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="2db1e-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="2db1e-167">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="2db1e-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="2db1e-168">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="2db1e-168">The preceding environment settings:</span></span>

* <span data-ttu-id="2db1e-169">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="2db1e-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="2db1e-170">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2db1e-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="2db1e-171">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="2db1e-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="2db1e-172">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="2db1e-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="2db1e-173">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="2db1e-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="2db1e-174">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="2db1e-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="2db1e-175">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2db1e-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="2db1e-176">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="2db1e-177">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="2db1e-178">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="2db1e-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="2db1e-179">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="2db1e-179">In the preceding code:</span></span>

* <span data-ttu-id="2db1e-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="2db1e-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2db1e-181">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="2db1e-182">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="2db1e-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="2db1e-183">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="2db1e-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="2db1e-184">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="2db1e-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="2db1e-185">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="2db1e-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="2db1e-186">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="2db1e-187">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="2db1e-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="2db1e-188">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2db1e-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="2db1e-189">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="2db1e-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="2db1e-190">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="2db1e-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="2db1e-191">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="2db1e-192">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-192">Exposed as environment variables.</span></span>

<span data-ttu-id="2db1e-193">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2db1e-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2db1e-194">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="2db1e-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="2db1e-195">Переменные среды, заданные в launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="2db1e-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="2db1e-196">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="2db1e-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="2db1e-197">Командная строка</span><span class="sxs-lookup"><span data-stu-id="2db1e-197">Command-line</span></span>

<span data-ttu-id="2db1e-198">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="2db1e-199">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2db1e-200">[Секреты приложения (диспетчер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2db1e-201">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-201">Environment variables.</span></span>

<span data-ttu-id="2db1e-202">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="2db1e-203">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="2db1e-203">Command-line arguments</span></span>

<span data-ttu-id="2db1e-204">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="2db1e-205">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="2db1e-206">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="2db1e-207">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="2db1e-207">The key value:</span></span>

* <span data-ttu-id="2db1e-208">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="2db1e-209">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="2db1e-210">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="2db1e-211">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="2db1e-212">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="2db1e-212">Switch mappings</span></span>

<span data-ttu-id="2db1e-213">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="2db1e-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="2db1e-214">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2db1e-215">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2db1e-216">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2db1e-217">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2db1e-218">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="2db1e-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2db1e-219">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="2db1e-220">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2db1e-221">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="2db1e-222">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="2db1e-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-223">Выполните следующую команду, чтобы проверить замену ключа:</span><span class="sxs-lookup"><span data-stu-id="2db1e-223">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2db1e-224">Примечание. В настоящее время `=` нельзя использовать для задания значений замены ключа с одним тире `-`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-224">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="2db1e-225">Также см. [эту проблему в GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="2db1e-225">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="2db1e-226">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="2db1e-226">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2db1e-227">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="2db1e-227">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2db1e-228">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-228">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2db1e-229">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="2db1e-229">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2db1e-230">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-230">Hierarchical configuration data</span></span>

<span data-ttu-id="2db1e-231">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-231">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2db1e-232">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-232">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2db1e-233">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-233">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-234">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="2db1e-234">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="2db1e-235">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-235">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="2db1e-236">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-236"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2db1e-237">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="2db1e-237">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="2db1e-238">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-238">Configuration keys and values</span></span>

<span data-ttu-id="2db1e-239">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-239">Configuration keys:</span></span>

* <span data-ttu-id="2db1e-240">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="2db1e-240">Are case-insensitive.</span></span> <span data-ttu-id="2db1e-241">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-241">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2db1e-242">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="2db1e-242">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="2db1e-243">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="2db1e-243">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="2db1e-244">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="2db1e-244">Hierarchical keys</span></span>
  * <span data-ttu-id="2db1e-245">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="2db1e-245">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2db1e-246">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="2db1e-246">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2db1e-247">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-247">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="2db1e-248">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="2db1e-248">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="2db1e-249">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-249">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2db1e-250"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-250">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2db1e-251">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="2db1e-251">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="2db1e-252">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-252">Configuration values:</span></span>

* <span data-ttu-id="2db1e-253">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-253">Are strings.</span></span>
* <span data-ttu-id="2db1e-254">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="2db1e-254">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="2db1e-255">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-255">Configuration providers</span></span>

<span data-ttu-id="2db1e-256">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2db1e-256">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2db1e-257">Поставщик</span><span class="sxs-lookup"><span data-stu-id="2db1e-257">Provider</span></span> | <span data-ttu-id="2db1e-258">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="2db1e-258">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="2db1e-259">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2db1e-259">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="2db1e-260">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-260">Azure Key Vault</span></span> |
| [<span data-ttu-id="2db1e-261">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="2db1e-261">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="2db1e-262">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-262">Azure App Configuration</span></span> |
| [<span data-ttu-id="2db1e-263">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-263">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="2db1e-264">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-264">Command-line parameters</span></span> |
| [<span data-ttu-id="2db1e-265">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-265">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2db1e-266">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-266">Custom source</span></span> |
| [<span data-ttu-id="2db1e-267">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-267">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="2db1e-268">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-268">Environment variables</span></span> |
| [<span data-ttu-id="2db1e-269">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-269">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2db1e-270">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="2db1e-270">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="2db1e-271">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-271">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2db1e-272">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="2db1e-272">Directory files</span></span> |
| [<span data-ttu-id="2db1e-273">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-273">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2db1e-274">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-274">In-memory collections</span></span> |
| [<span data-ttu-id="2db1e-275">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="2db1e-275">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="2db1e-276">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-276">File in the user profile directory</span></span> |

<span data-ttu-id="2db1e-277">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-277">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="2db1e-278">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="2db1e-278">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2db1e-279">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-279">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2db1e-280">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2db1e-280">*appsettings.json*</span></span>
1. <span data-ttu-id="2db1e-281">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="2db1e-281">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="2db1e-282">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="2db1e-282">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="2db1e-283">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-283">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2db1e-284">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-284">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="2db1e-285">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="2db1e-285">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2db1e-286">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="2db1e-286">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="2db1e-287">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="2db1e-287">Connection string prefixes</span></span>

<span data-ttu-id="2db1e-288">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-288">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="2db1e-289">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-289">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2db1e-290">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-290">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2db1e-291">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="2db1e-291">Connection string prefix</span></span> | <span data-ttu-id="2db1e-292">Поставщик</span><span class="sxs-lookup"><span data-stu-id="2db1e-292">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2db1e-293">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-293">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2db1e-294">MySQL</span><span class="sxs-lookup"><span data-stu-id="2db1e-294">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2db1e-295">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="2db1e-295">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2db1e-296">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2db1e-296">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2db1e-297">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-297">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2db1e-298">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-298">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2db1e-299">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="2db1e-299">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2db1e-300">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-300">Environment variable key</span></span> | <span data-ttu-id="2db1e-301">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-301">Converted configuration key</span></span> | <span data-ttu-id="2db1e-302">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="2db1e-302">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-303">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="2db1e-303">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-304">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-305">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-305">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-306">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-307">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-307">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-308">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-308">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-309">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-309">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="2db1e-310">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="2db1e-310">JSON configuration provider</span></span>

<span data-ttu-id="2db1e-311"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-311">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="2db1e-312">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="2db1e-312">Overloads can specify:</span></span>

* <span data-ttu-id="2db1e-313">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="2db1e-313">Whether the file is optional.</span></span>
* <span data-ttu-id="2db1e-314">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="2db1e-314">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="2db1e-315">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="2db1e-315">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="2db1e-316">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="2db1e-316">The preceding code:</span></span>

* <span data-ttu-id="2db1e-317">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="2db1e-317">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="2db1e-318">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="2db1e-318">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="2db1e-319">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="2db1e-319">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="2db1e-320">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-320">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="2db1e-321">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-321">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2db1e-322">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-322">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2db1e-323">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-323">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="2db1e-324">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-324">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="2db1e-325">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-325">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2db1e-326">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-326">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2db1e-327">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-327">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="2db1e-328">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-328">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="2db1e-329">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-329">File configuration provider</span></span>

<span data-ttu-id="2db1e-330"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="2db1e-330"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2db1e-331">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-331">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="2db1e-332">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="2db1e-332">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2db1e-333">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="2db1e-333">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="2db1e-334">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="2db1e-334">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2db1e-335">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="2db1e-335">INI configuration provider</span></span>

<span data-ttu-id="2db1e-336"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-336">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-337">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-337">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="2db1e-338">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-338">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="2db1e-339">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-339">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="2db1e-340">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-340">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2db1e-341">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-341">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="2db1e-342">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-342">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="2db1e-343">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="2db1e-343">XML configuration provider</span></span>

<span data-ttu-id="2db1e-344"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-344">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-345">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-345">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="2db1e-346">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-346">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="2db1e-347">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-347">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="2db1e-348">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-348">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2db1e-349">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-349">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="2db1e-350">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-350">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-351">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-351">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="2db1e-352">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="2db1e-352">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-353">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="2db1e-353">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2db1e-354">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-354">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2db1e-355">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2db1e-355">key:attribute</span></span>
* <span data-ttu-id="2db1e-356">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2db1e-356">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2db1e-357">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-357">Key-per-file configuration provider</span></span>

<span data-ttu-id="2db1e-358"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-358">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2db1e-359">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-359">The key is the file name.</span></span> <span data-ttu-id="2db1e-360">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-360">The value contains the file's contents.</span></span> <span data-ttu-id="2db1e-361">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="2db1e-361">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2db1e-362">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-362">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2db1e-363">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="2db1e-363">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2db1e-364">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-364">Overloads permit specifying:</span></span>

* <span data-ttu-id="2db1e-365">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="2db1e-365">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2db1e-366">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-366">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2db1e-367">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-367">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2db1e-368">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-368">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2db1e-369">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-369">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="2db1e-370">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-370">Memory configuration provider</span></span>

<span data-ttu-id="2db1e-371"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-371">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2db1e-372">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-372">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="2db1e-373">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-373">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-374">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="2db1e-374">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2db1e-375">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2db1e-375">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2db1e-376">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-376">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="2db1e-377">GetValue</span><span class="sxs-lookup"><span data-stu-id="2db1e-377">GetValue</span></span>

<span data-ttu-id="2db1e-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="2db1e-378">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-379">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-379">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2db1e-380">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="2db1e-380">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2db1e-381">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-381">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="2db1e-382">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-382">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="2db1e-383">GetSection</span><span class="sxs-lookup"><span data-stu-id="2db1e-383">GetSection</span></span>

<span data-ttu-id="2db1e-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="2db1e-384">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2db1e-385">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-385">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-386">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-386">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-387">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-387">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2db1e-388">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-388">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2db1e-389">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="2db1e-389">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2db1e-390"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="2db1e-390">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="2db1e-391">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="2db1e-391">GetChildren and Exists</span></span>

<span data-ttu-id="2db1e-392">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-392">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-393">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="2db1e-393">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="2db1e-394">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="2db1e-394">Bind an array</span></span>

<span data-ttu-id="2db1e-395">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-395">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2db1e-396">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="2db1e-396">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="2db1e-397">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="2db1e-397">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="2db1e-398">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-398">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="2db1e-399">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="2db1e-399">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-400">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="2db1e-400">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="2db1e-401">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-401">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="2db1e-402">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-402">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="2db1e-403">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="2db1e-403">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="2db1e-404">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="2db1e-404">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="2db1e-405">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="2db1e-405">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-406">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="2db1e-406">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="2db1e-407">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-407">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2db1e-408">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="2db1e-408">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2db1e-409">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="2db1e-409">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2db1e-410">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="2db1e-410">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="2db1e-411">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="2db1e-411">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="2db1e-412">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-412">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="2db1e-413">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="2db1e-413">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-414">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="2db1e-414">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="2db1e-415">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="2db1e-415">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="2db1e-416">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-416">Custom configuration provider</span></span>

<span data-ttu-id="2db1e-417">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2db1e-417">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2db1e-418">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="2db1e-418">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2db1e-419">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="2db1e-419">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2db1e-420">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-420">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2db1e-421">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="2db1e-421">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2db1e-422">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="2db1e-422">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2db1e-423">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-423">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2db1e-424">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-424">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2db1e-425">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-425">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2db1e-426">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="2db1e-426">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2db1e-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-427">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2db1e-428">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-428">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2db1e-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-429">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2db1e-430">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-430">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2db1e-431">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-431">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="2db1e-432">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="2db1e-432">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="2db1e-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-433">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2db1e-434">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-434">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2db1e-435">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-435">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2db1e-436">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-436">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="2db1e-437">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="2db1e-437">Access configuration in Startup</span></span>

<span data-ttu-id="2db1e-438">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-438">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="2db1e-439">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2db1e-439">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="2db1e-440">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="2db1e-440">Access configuration in Razor Pages</span></span>

<span data-ttu-id="2db1e-441">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="2db1e-441">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="2db1e-442">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-442">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="2db1e-443">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="2db1e-443">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="2db1e-444">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="2db1e-444">Access configuration in a MVC view file</span></span>

<span data-ttu-id="2db1e-445">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="2db1e-445">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="2db1e-446">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="2db1e-446">Configure options with a delegate</span></span>

<span data-ttu-id="2db1e-447">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-447">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="2db1e-448">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-448">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="2db1e-449">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-449">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="2db1e-450">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-450">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="2db1e-451">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="2db1e-451">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2db1e-452">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json*, а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-452">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2db1e-453">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="2db1e-453">Host versus app configuration</span></span>

<span data-ttu-id="2db1e-454">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-454">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2db1e-455">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="2db1e-455">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2db1e-456">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-456">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2db1e-457">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-457">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2db1e-458">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-458">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="2db1e-459">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2db1e-459">Default host configuration</span></span>

<span data-ttu-id="2db1e-460">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="2db1e-460">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="2db1e-461">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-461">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2db1e-462">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="2db1e-462">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="2db1e-463">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-463">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2db1e-464">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-464">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2db1e-465">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="2db1e-465">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="2db1e-466">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-466">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="2db1e-467">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-467">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="2db1e-468">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="2db1e-468">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="2db1e-469">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="2db1e-469">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2db1e-470">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="2db1e-470">Other configuration</span></span>

<span data-ttu-id="2db1e-471">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-471">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2db1e-472">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-472">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2db1e-473">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-473">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2db1e-474">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-474">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2db1e-475">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-475">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2db1e-476">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-476">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2db1e-477">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="2db1e-477">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="2db1e-478">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-478">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2db1e-479">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="2db1e-479">Add configuration from an external assembly</span></span>

<span data-ttu-id="2db1e-480">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-480">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2db1e-481">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-481">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2db1e-482">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2db1e-482">Additional resources</span></span>

* [<span data-ttu-id="2db1e-483">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-483">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2db1e-484">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-484">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="2db1e-485">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="2db1e-485">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="2db1e-486">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-486">Azure Key Vault</span></span>
* <span data-ttu-id="2db1e-487">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-487">Azure App Configuration</span></span>
* <span data-ttu-id="2db1e-488">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="2db1e-488">Command-line arguments</span></span>
* <span data-ttu-id="2db1e-489">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="2db1e-489">Custom providers (installed or created)</span></span>
* <span data-ttu-id="2db1e-490">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="2db1e-490">Directory files</span></span>
* <span data-ttu-id="2db1e-491">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-491">Environment variables</span></span>
* <span data-ttu-id="2db1e-492">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="2db1e-492">In-memory .NET objects</span></span>
* <span data-ttu-id="2db1e-493">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="2db1e-493">Settings files</span></span>

<span data-ttu-id="2db1e-494">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2db1e-494">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2db1e-495">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="2db1e-495">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="2db1e-496">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-496">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="2db1e-497">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="2db1e-497">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="2db1e-498">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-498">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2db1e-499">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2db1e-499">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2db1e-500">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="2db1e-500">Host versus app configuration</span></span>

<span data-ttu-id="2db1e-501">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-501">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2db1e-502">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="2db1e-502">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2db1e-503">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-503">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2db1e-504">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-504">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2db1e-505">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-505">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2db1e-506">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="2db1e-506">Other configuration</span></span>

<span data-ttu-id="2db1e-507">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-507">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2db1e-508">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-508">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2db1e-509">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-509">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2db1e-510">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-510">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2db1e-511">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-511">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2db1e-512">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="2db1e-512">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2db1e-513">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-513">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="2db1e-514">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2db1e-514">Default configuration</span></span>

<span data-ttu-id="2db1e-515">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-515">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="2db1e-516">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="2db1e-516">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="2db1e-517">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2db1e-517">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2db1e-518">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2db1e-518">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="2db1e-519">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-519">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2db1e-520">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-520">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2db1e-521">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-521">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2db1e-522">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-522">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2db1e-523">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="2db1e-523">App configuration is provided from:</span></span>
  * <span data-ttu-id="2db1e-524">Файл *appsettings.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-524">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2db1e-525">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-525">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2db1e-526">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="2db1e-526">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="2db1e-527">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-527">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="2db1e-528">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2db1e-528">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="2db1e-529">Безопасность</span><span class="sxs-lookup"><span data-stu-id="2db1e-529">Security</span></span>

<span data-ttu-id="2db1e-530">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-530">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="2db1e-531">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="2db1e-531">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="2db1e-532">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="2db1e-532">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2db1e-533">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-533">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2db1e-534">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="2db1e-534">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2db1e-535"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-535"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2db1e-536">Менеджер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-536">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="2db1e-537">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="2db1e-537">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="2db1e-538">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2db1e-538">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2db1e-539">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-539">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2db1e-540">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-540">Hierarchical configuration data</span></span>

<span data-ttu-id="2db1e-541">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-541">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2db1e-542">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-542">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="2db1e-543">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-543">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="2db1e-544">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="2db1e-544">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="2db1e-545">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-545">section0:key0</span></span>
* <span data-ttu-id="2db1e-546">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-546">section0:key1</span></span>
* <span data-ttu-id="2db1e-547">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-547">section1:key0</span></span>
* <span data-ttu-id="2db1e-548">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-548">section1:key1</span></span>

<span data-ttu-id="2db1e-549">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-549"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2db1e-550">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="2db1e-550">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="2db1e-551">Соглашения</span><span class="sxs-lookup"><span data-stu-id="2db1e-551">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="2db1e-552">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="2db1e-552">Sources and providers</span></span>

<span data-ttu-id="2db1e-553">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-553">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="2db1e-554">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="2db1e-554">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="2db1e-555">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="2db1e-555">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="2db1e-556">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2db1e-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="2db1e-557"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="2db1e-558">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-558">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="2db1e-559">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-559">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="2db1e-560">Клавиши</span><span class="sxs-lookup"><span data-stu-id="2db1e-560">Keys</span></span>

<span data-ttu-id="2db1e-561">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-561">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="2db1e-562">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-562">Keys are case-insensitive.</span></span> <span data-ttu-id="2db1e-563">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-563">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2db1e-564">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="2db1e-564">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="2db1e-565">Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="2db1e-565">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="2db1e-566">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="2db1e-566">Hierarchical keys</span></span>
  * <span data-ttu-id="2db1e-567">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="2db1e-567">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2db1e-568">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="2db1e-568">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2db1e-569">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="2db1e-569">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="2db1e-570">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="2db1e-570">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2db1e-571">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="2db1e-571">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2db1e-572"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-572">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2db1e-573">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2db1e-573">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="2db1e-574">Значения</span><span class="sxs-lookup"><span data-stu-id="2db1e-574">Values</span></span>

<span data-ttu-id="2db1e-575">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-575">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="2db1e-576">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="2db1e-576">Values are strings.</span></span>
* <span data-ttu-id="2db1e-577">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="2db1e-577">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="2db1e-578">Поставщики</span><span class="sxs-lookup"><span data-stu-id="2db1e-578">Providers</span></span>

<span data-ttu-id="2db1e-579">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2db1e-579">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2db1e-580">Поставщик</span><span class="sxs-lookup"><span data-stu-id="2db1e-580">Provider</span></span> | <span data-ttu-id="2db1e-581">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="2db1e-581">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="2db1e-582">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="2db1e-582">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="2db1e-583">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-583">Azure Key Vault</span></span> |
| <span data-ttu-id="2db1e-584">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="2db1e-584">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="2db1e-585">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="2db1e-585">Azure App Configuration</span></span> |
| [<span data-ttu-id="2db1e-586">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-586">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="2db1e-587">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-587">Command-line parameters</span></span> |
| [<span data-ttu-id="2db1e-588">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-588">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2db1e-589">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-589">Custom source</span></span> |
| [<span data-ttu-id="2db1e-590">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-590">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="2db1e-591">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-591">Environment variables</span></span> |
| [<span data-ttu-id="2db1e-592">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-592">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2db1e-593">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="2db1e-593">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="2db1e-594">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-594">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2db1e-595">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="2db1e-595">Directory files</span></span> |
| [<span data-ttu-id="2db1e-596">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-596">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2db1e-597">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-597">In-memory collections</span></span> |
| <span data-ttu-id="2db1e-598">[Секреты пользователей (Менеджер секретов)](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="2db1e-598">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="2db1e-599">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-599">File in the user profile directory</span></span> |

<span data-ttu-id="2db1e-600">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-600">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="2db1e-601">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="2db1e-601">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="2db1e-602">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="2db1e-602">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2db1e-603">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-603">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2db1e-604">Файлы (*appsettings.json*, *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="2db1e-604">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="2db1e-605">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="2db1e-605">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="2db1e-606">[Секреты пользователя (Менеджер секретов)](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="2db1e-606">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="2db1e-607">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-607">Environment variables</span></span>
1. <span data-ttu-id="2db1e-608">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="2db1e-608">Command-line arguments</span></span>

<span data-ttu-id="2db1e-609">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="2db1e-609">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2db1e-610">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-610">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2db1e-611">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2db1e-611">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="2db1e-612">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="2db1e-612">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="2db1e-613">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="2db1e-613">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="2db1e-614">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2db1e-614">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2db1e-615">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-615">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="2db1e-616">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-616">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="2db1e-617">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="2db1e-617">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="2db1e-618">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="2db1e-618">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="2db1e-619">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="2db1e-619">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="2db1e-620">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="2db1e-620">Consume configuration during app startup</span></span>

<span data-ttu-id="2db1e-621">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-621">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2db1e-622">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="2db1e-622">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="2db1e-623">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="2db1e-623">Command-line Configuration Provider</span></span>

<span data-ttu-id="2db1e-624"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-624">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-625">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-625">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2db1e-626">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-626">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="2db1e-627">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2db1e-627">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2db1e-628">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="2db1e-628">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2db1e-629">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="2db1e-629">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2db1e-630">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-630">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2db1e-631">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-631">Environment variables.</span></span>

<span data-ttu-id="2db1e-632">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-632">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="2db1e-633">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="2db1e-633">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="2db1e-634">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="2db1e-634">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="2db1e-635">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-635">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="2db1e-636">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-636">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2db1e-637">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="2db1e-637">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="2db1e-638">**Пример**</span><span class="sxs-lookup"><span data-stu-id="2db1e-638">**Example**</span></span>

<span data-ttu-id="2db1e-639">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-639">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="2db1e-640">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="2db1e-640">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="2db1e-641">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-641">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="2db1e-642">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-642">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2db1e-643">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-643">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="2db1e-644">Аргументы</span><span class="sxs-lookup"><span data-stu-id="2db1e-644">Arguments</span></span>

<span data-ttu-id="2db1e-645">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-645">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="2db1e-646">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-646">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="2db1e-647">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="2db1e-647">Key prefix</span></span>               | <span data-ttu-id="2db1e-648">Пример</span><span class="sxs-lookup"><span data-stu-id="2db1e-648">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="2db1e-649">Без префикса</span><span class="sxs-lookup"><span data-stu-id="2db1e-649">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="2db1e-650">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="2db1e-650">Two dashes (`--`)</span></span>        | <span data-ttu-id="2db1e-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="2db1e-651">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="2db1e-652">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="2db1e-652">Forward slash (`/`)</span></span>      | <span data-ttu-id="2db1e-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="2db1e-653">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="2db1e-654">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="2db1e-654">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="2db1e-655">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="2db1e-655">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="2db1e-656">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="2db1e-656">Switch mappings</span></span>

<span data-ttu-id="2db1e-657">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="2db1e-657">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="2db1e-658">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="2db1e-658">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2db1e-659">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-659">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2db1e-660">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-660">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2db1e-661">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-661">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2db1e-662">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="2db1e-662">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2db1e-663">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-663">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="2db1e-664">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-664">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2db1e-665">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="2db1e-665">Create a switch mappings dictionary.</span></span> <span data-ttu-id="2db1e-666">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="2db1e-666">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="2db1e-667">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="2db1e-667">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="2db1e-668">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="2db1e-668">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2db1e-669">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-669">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2db1e-670">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="2db1e-670">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="2db1e-671">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="2db1e-671">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="2db1e-672">Ключ</span><span class="sxs-lookup"><span data-stu-id="2db1e-672">Key</span></span>       | <span data-ttu-id="2db1e-673">Значение</span><span class="sxs-lookup"><span data-stu-id="2db1e-673">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="2db1e-674">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="2db1e-674">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="2db1e-675">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="2db1e-675">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="2db1e-676">Ключ</span><span class="sxs-lookup"><span data-stu-id="2db1e-676">Key</span></span>               | <span data-ttu-id="2db1e-677">Значение</span><span class="sxs-lookup"><span data-stu-id="2db1e-677">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="2db1e-678">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-678">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="2db1e-679"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-679">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-680">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-680">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2db1e-681">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-681">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="2db1e-682">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2db1e-682">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2db1e-683">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-683">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="2db1e-684">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2db1e-684">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2db1e-685">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="2db1e-685">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2db1e-686">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="2db1e-686">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="2db1e-687">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="2db1e-687">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2db1e-688">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-688">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2db1e-689">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-689">Command-line arguments.</span></span>

<span data-ttu-id="2db1e-690">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-690">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="2db1e-691">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-691">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="2db1e-692">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="2db1e-692">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="2db1e-693">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="2db1e-693">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="2db1e-694">**Пример**</span><span class="sxs-lookup"><span data-stu-id="2db1e-694">**Example**</span></span>

<span data-ttu-id="2db1e-695">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-695">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="2db1e-696">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-696">Run the sample app.</span></span> <span data-ttu-id="2db1e-697">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-697">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2db1e-698">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-698">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="2db1e-699">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-699">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="2db1e-700">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-700">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="2db1e-701">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-701">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="2db1e-702">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="2db1e-702">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="2db1e-703">Префиксы</span><span class="sxs-lookup"><span data-stu-id="2db1e-703">Prefixes</span></span>

<span data-ttu-id="2db1e-704">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-704">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="2db1e-705">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-705">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="2db1e-706">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="2db1e-706">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="2db1e-707">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-707">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="2db1e-708">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2db1e-708">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2db1e-709">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="2db1e-709">**Connection string prefixes**</span></span>

<span data-ttu-id="2db1e-710">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-710">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2db1e-711">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="2db1e-711">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2db1e-712">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="2db1e-712">Connection string prefix</span></span> | <span data-ttu-id="2db1e-713">Поставщик</span><span class="sxs-lookup"><span data-stu-id="2db1e-713">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2db1e-714">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="2db1e-714">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2db1e-715">MySQL</span><span class="sxs-lookup"><span data-stu-id="2db1e-715">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2db1e-716">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="2db1e-716">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2db1e-717">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2db1e-717">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2db1e-718">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-718">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2db1e-719">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2db1e-719">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2db1e-720">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="2db1e-720">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2db1e-721">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="2db1e-721">Environment variable key</span></span> | <span data-ttu-id="2db1e-722">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-722">Converted configuration key</span></span> | <span data-ttu-id="2db1e-723">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="2db1e-723">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-724">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="2db1e-724">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-725">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-726">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-726">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-727">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-728">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-728">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2db1e-729">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-729">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2db1e-730">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2db1e-730">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="2db1e-731">**Пример**</span><span class="sxs-lookup"><span data-stu-id="2db1e-731">**Example**</span></span>

<span data-ttu-id="2db1e-732">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="2db1e-732">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="2db1e-733">Имя: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="2db1e-733">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="2db1e-734">Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="2db1e-734">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="2db1e-735">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="2db1e-735">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="2db1e-736">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-736">File Configuration Provider</span></span>

<span data-ttu-id="2db1e-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="2db1e-737"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2db1e-738">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="2db1e-738">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="2db1e-739">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="2db1e-739">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2db1e-740">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="2db1e-740">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="2db1e-741">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="2db1e-741">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2db1e-742">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="2db1e-742">INI Configuration Provider</span></span>

<span data-ttu-id="2db1e-743"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-743">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-744">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-744">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2db1e-745">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="2db1e-745">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="2db1e-746">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-746">Overloads permit specifying:</span></span>

* <span data-ttu-id="2db1e-747">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="2db1e-747">Whether the file is optional.</span></span>
* <span data-ttu-id="2db1e-748">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="2db1e-748">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2db1e-749"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-749">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2db1e-750">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-750">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2db1e-751">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-751">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="2db1e-752">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-752">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2db1e-753">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-753">section0:key0</span></span>
* <span data-ttu-id="2db1e-754">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-754">section0:key1</span></span>
* <span data-ttu-id="2db1e-755">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="2db1e-755">section1:subsection:key</span></span>
* <span data-ttu-id="2db1e-756">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="2db1e-756">section2:subsection0:key</span></span>
* <span data-ttu-id="2db1e-757">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="2db1e-757">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="2db1e-758">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="2db1e-758">JSON Configuration Provider</span></span>

<span data-ttu-id="2db1e-759"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-759">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="2db1e-760">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-760">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2db1e-761">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-761">Overloads permit specifying:</span></span>

* <span data-ttu-id="2db1e-762">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="2db1e-762">Whether the file is optional.</span></span>
* <span data-ttu-id="2db1e-763">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="2db1e-763">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2db1e-764"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-764">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2db1e-765">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-765">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2db1e-766">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="2db1e-766">The method is called to load configuration from:</span></span>

* <span data-ttu-id="2db1e-767">*appsettings.json*: Этот файл считывается первым.</span><span class="sxs-lookup"><span data-stu-id="2db1e-767">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="2db1e-768">Версия файла среды может переопределить значения, предоставленные *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-768">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="2db1e-769">*appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2db1e-769">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="2db1e-770">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2db1e-770">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2db1e-771">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="2db1e-771">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2db1e-772">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="2db1e-772">Environment variables.</span></span>
* <span data-ttu-id="2db1e-773">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-773">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2db1e-774">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-774">Command-line arguments.</span></span>

<span data-ttu-id="2db1e-775">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-775">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="2db1e-776">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-776">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="2db1e-777">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-777">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2db1e-778">**Пример**</span><span class="sxs-lookup"><span data-stu-id="2db1e-778">**Example**</span></span>

<span data-ttu-id="2db1e-779">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-779">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="2db1e-780">Первый вызов `AddJsonFile` загружает конфигурацию из *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-780">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="2db1e-781">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-781">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="2db1e-782">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="2db1e-782">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="2db1e-783">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-783">Run the sample app.</span></span> <span data-ttu-id="2db1e-784">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-784">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2db1e-785">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-785">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="2db1e-786">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="2db1e-786">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="2db1e-787">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="2db1e-787">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="2db1e-788">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-788">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="2db1e-789">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-789">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="2db1e-790">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="2db1e-790">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="2db1e-791">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-791">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="2db1e-792">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-792">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="2db1e-793">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="2db1e-793">XML Configuration Provider</span></span>

<span data-ttu-id="2db1e-794"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-794">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2db1e-795">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-795">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2db1e-796">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-796">Overloads permit specifying:</span></span>

* <span data-ttu-id="2db1e-797">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="2db1e-797">Whether the file is optional.</span></span>
* <span data-ttu-id="2db1e-798">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="2db1e-798">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2db1e-799"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-799">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2db1e-800">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-800">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="2db1e-801">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="2db1e-801">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="2db1e-802">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-802">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2db1e-803">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-803">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="2db1e-804">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-804">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2db1e-805">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-805">section0:key0</span></span>
* <span data-ttu-id="2db1e-806">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-806">section0:key1</span></span>
* <span data-ttu-id="2db1e-807">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-807">section1:key0</span></span>
* <span data-ttu-id="2db1e-808">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-808">section1:key1</span></span>

<span data-ttu-id="2db1e-809">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-809">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="2db1e-810">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-810">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2db1e-811">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-811">section:section0:key:key0</span></span>
* <span data-ttu-id="2db1e-812">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-812">section:section0:key:key1</span></span>
* <span data-ttu-id="2db1e-813">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-813">section:section1:key:key0</span></span>
* <span data-ttu-id="2db1e-814">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-814">section:section1:key:key1</span></span>

<span data-ttu-id="2db1e-815">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="2db1e-815">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2db1e-816">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-816">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2db1e-817">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2db1e-817">key:attribute</span></span>
* <span data-ttu-id="2db1e-818">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2db1e-818">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2db1e-819">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="2db1e-819">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="2db1e-820"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-820">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2db1e-821">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-821">The key is the file name.</span></span> <span data-ttu-id="2db1e-822">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="2db1e-822">The value contains the file's contents.</span></span> <span data-ttu-id="2db1e-823">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="2db1e-823">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2db1e-824">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-824">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2db1e-825">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="2db1e-825">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2db1e-826">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="2db1e-826">Overloads permit specifying:</span></span>

* <span data-ttu-id="2db1e-827">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="2db1e-827">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2db1e-828">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-828">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2db1e-829">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-829">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2db1e-830">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-830">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2db1e-831">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-831">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="2db1e-832">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="2db1e-832">Memory Configuration Provider</span></span>

<span data-ttu-id="2db1e-833"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-833">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2db1e-834">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-834">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2db1e-835">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-835">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="2db1e-836">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-836">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="2db1e-837">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-837">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="2db1e-838">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="2db1e-838">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="2db1e-839">GetValue</span><span class="sxs-lookup"><span data-stu-id="2db1e-839">GetValue</span></span>

<span data-ttu-id="2db1e-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="2db1e-840">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="2db1e-841">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2db1e-841">An overload accepts a default value.</span></span>

<span data-ttu-id="2db1e-842">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="2db1e-842">The following example:</span></span>

* <span data-ttu-id="2db1e-843">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-843">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="2db1e-844">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-844">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="2db1e-845">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-845">Types the value as an `int`.</span></span>
* <span data-ttu-id="2db1e-846">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="2db1e-846">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2db1e-847">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="2db1e-847">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2db1e-848">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="2db1e-848">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="2db1e-849">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="2db1e-849">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="2db1e-850">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-850">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="2db1e-851">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-851">section0:key0</span></span>
* <span data-ttu-id="2db1e-852">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-852">section0:key1</span></span>
* <span data-ttu-id="2db1e-853">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-853">section1:key0</span></span>
* <span data-ttu-id="2db1e-854">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-854">section1:key1</span></span>
* <span data-ttu-id="2db1e-855">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-855">section2:subsection0:key0</span></span>
* <span data-ttu-id="2db1e-856">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-856">section2:subsection0:key1</span></span>
* <span data-ttu-id="2db1e-857">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="2db1e-857">section2:subsection1:key0</span></span>
* <span data-ttu-id="2db1e-858">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="2db1e-858">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="2db1e-859">GetSection</span><span class="sxs-lookup"><span data-stu-id="2db1e-859">GetSection</span></span>

<span data-ttu-id="2db1e-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="2db1e-860">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2db1e-861">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="2db1e-861">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="2db1e-862">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="2db1e-862">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="2db1e-863">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="2db1e-863">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="2db1e-864">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-864">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2db1e-865">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-865">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2db1e-866">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="2db1e-866">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2db1e-867"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="2db1e-867">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="2db1e-868">GetChildren</span><span class="sxs-lookup"><span data-stu-id="2db1e-868">GetChildren</span></span>

<span data-ttu-id="2db1e-869">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="2db1e-869">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="2db1e-870">Exists</span><span class="sxs-lookup"><span data-stu-id="2db1e-870">Exists</span></span>

<span data-ttu-id="2db1e-871">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-871">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="2db1e-872">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-872">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="2db1e-873">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="2db1e-873">Bind to an object graph</span></span>

<span data-ttu-id="2db1e-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="2db1e-874"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="2db1e-875">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="2db1e-875">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="2db1e-876">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="2db1e-876">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="2db1e-877">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-877">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="2db1e-878">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-878">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="2db1e-879">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="2db1e-879">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="2db1e-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="2db1e-880">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2db1e-881">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-881">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="2db1e-882">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="2db1e-882">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2db1e-883">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="2db1e-883">Bind an array to a class</span></span>

<span data-ttu-id="2db1e-884">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="2db1e-884">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="2db1e-885"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-885">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2db1e-886">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="2db1e-886">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="2db1e-887">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="2db1e-887">Binding is provided by convention.</span></span> <span data-ttu-id="2db1e-888">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="2db1e-888">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="2db1e-889">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="2db1e-889">**In-memory array processing**</span></span>

<span data-ttu-id="2db1e-890">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="2db1e-890">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="2db1e-891">Ключ</span><span class="sxs-lookup"><span data-stu-id="2db1e-891">Key</span></span>             | <span data-ttu-id="2db1e-892">Значение</span><span class="sxs-lookup"><span data-stu-id="2db1e-892">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2db1e-893">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="2db1e-893">array:entries:0</span></span> | <span data-ttu-id="2db1e-894">value0</span><span class="sxs-lookup"><span data-stu-id="2db1e-894">value0</span></span> |
| <span data-ttu-id="2db1e-895">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="2db1e-895">array:entries:1</span></span> | <span data-ttu-id="2db1e-896">value1</span><span class="sxs-lookup"><span data-stu-id="2db1e-896">value1</span></span> |
| <span data-ttu-id="2db1e-897">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="2db1e-897">array:entries:2</span></span> | <span data-ttu-id="2db1e-898">value2</span><span class="sxs-lookup"><span data-stu-id="2db1e-898">value2</span></span> |
| <span data-ttu-id="2db1e-899">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="2db1e-899">array:entries:4</span></span> | <span data-ttu-id="2db1e-900">value4</span><span class="sxs-lookup"><span data-stu-id="2db1e-900">value4</span></span> |
| <span data-ttu-id="2db1e-901">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="2db1e-901">array:entries:5</span></span> | <span data-ttu-id="2db1e-902">value5</span><span class="sxs-lookup"><span data-stu-id="2db1e-902">value5</span></span> |

<span data-ttu-id="2db1e-903">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="2db1e-903">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="2db1e-904">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="2db1e-904">The array skips a value for index &num;3.</span></span> <span data-ttu-id="2db1e-905">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="2db1e-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="2db1e-906">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-906">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="2db1e-907">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="2db1e-907">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="2db1e-908">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="2db1e-908">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="2db1e-909">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-909">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="2db1e-910">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2db1e-910">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2db1e-911">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2db1e-911">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2db1e-912">0</span><span class="sxs-lookup"><span data-stu-id="2db1e-912">0</span></span>                            | <span data-ttu-id="2db1e-913">value0</span><span class="sxs-lookup"><span data-stu-id="2db1e-913">value0</span></span>                       |
| <span data-ttu-id="2db1e-914">1</span><span class="sxs-lookup"><span data-stu-id="2db1e-914">1</span></span>                            | <span data-ttu-id="2db1e-915">value1</span><span class="sxs-lookup"><span data-stu-id="2db1e-915">value1</span></span>                       |
| <span data-ttu-id="2db1e-916">2</span><span class="sxs-lookup"><span data-stu-id="2db1e-916">2</span></span>                            | <span data-ttu-id="2db1e-917">value2</span><span class="sxs-lookup"><span data-stu-id="2db1e-917">value2</span></span>                       |
| <span data-ttu-id="2db1e-918">3</span><span class="sxs-lookup"><span data-stu-id="2db1e-918">3</span></span>                            | <span data-ttu-id="2db1e-919">value4</span><span class="sxs-lookup"><span data-stu-id="2db1e-919">value4</span></span>                       |
| <span data-ttu-id="2db1e-920">4</span><span class="sxs-lookup"><span data-stu-id="2db1e-920">4</span></span>                            | <span data-ttu-id="2db1e-921">value5</span><span class="sxs-lookup"><span data-stu-id="2db1e-921">value5</span></span>                       |

<span data-ttu-id="2db1e-922">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-922">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2db1e-923">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="2db1e-923">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2db1e-924">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="2db1e-924">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2db1e-925">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-925">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="2db1e-926">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-926">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="2db1e-927">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-927">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="2db1e-928">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="2db1e-928">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="2db1e-929">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-929">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="2db1e-930">Ключ</span><span class="sxs-lookup"><span data-stu-id="2db1e-930">Key</span></span>             | <span data-ttu-id="2db1e-931">Значение</span><span class="sxs-lookup"><span data-stu-id="2db1e-931">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2db1e-932">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="2db1e-932">array:entries:3</span></span> | <span data-ttu-id="2db1e-933">value3</span><span class="sxs-lookup"><span data-stu-id="2db1e-933">value3</span></span> |

<span data-ttu-id="2db1e-934">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="2db1e-934">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="2db1e-935">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2db1e-935">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2db1e-936">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2db1e-936">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2db1e-937">0</span><span class="sxs-lookup"><span data-stu-id="2db1e-937">0</span></span>                            | <span data-ttu-id="2db1e-938">value0</span><span class="sxs-lookup"><span data-stu-id="2db1e-938">value0</span></span>                       |
| <span data-ttu-id="2db1e-939">1</span><span class="sxs-lookup"><span data-stu-id="2db1e-939">1</span></span>                            | <span data-ttu-id="2db1e-940">value1</span><span class="sxs-lookup"><span data-stu-id="2db1e-940">value1</span></span>                       |
| <span data-ttu-id="2db1e-941">2</span><span class="sxs-lookup"><span data-stu-id="2db1e-941">2</span></span>                            | <span data-ttu-id="2db1e-942">value2</span><span class="sxs-lookup"><span data-stu-id="2db1e-942">value2</span></span>                       |
| <span data-ttu-id="2db1e-943">3</span><span class="sxs-lookup"><span data-stu-id="2db1e-943">3</span></span>                            | <span data-ttu-id="2db1e-944">value3</span><span class="sxs-lookup"><span data-stu-id="2db1e-944">value3</span></span>                       |
| <span data-ttu-id="2db1e-945">4</span><span class="sxs-lookup"><span data-stu-id="2db1e-945">4</span></span>                            | <span data-ttu-id="2db1e-946">value4</span><span class="sxs-lookup"><span data-stu-id="2db1e-946">value4</span></span>                       |
| <span data-ttu-id="2db1e-947">5</span><span class="sxs-lookup"><span data-stu-id="2db1e-947">5</span></span>                            | <span data-ttu-id="2db1e-948">value5</span><span class="sxs-lookup"><span data-stu-id="2db1e-948">value5</span></span>                       |

<span data-ttu-id="2db1e-949">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="2db1e-949">**JSON array processing**</span></span>

<span data-ttu-id="2db1e-950">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="2db1e-950">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="2db1e-951">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="2db1e-951">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="2db1e-952">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="2db1e-952">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="2db1e-953">Ключ</span><span class="sxs-lookup"><span data-stu-id="2db1e-953">Key</span></span>                     | <span data-ttu-id="2db1e-954">Значение</span><span class="sxs-lookup"><span data-stu-id="2db1e-954">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="2db1e-955">json_array:key</span><span class="sxs-lookup"><span data-stu-id="2db1e-955">json_array:key</span></span>          | <span data-ttu-id="2db1e-956">valueA</span><span class="sxs-lookup"><span data-stu-id="2db1e-956">valueA</span></span> |
| <span data-ttu-id="2db1e-957">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="2db1e-957">json_array:subsection:0</span></span> | <span data-ttu-id="2db1e-958">valueB</span><span class="sxs-lookup"><span data-stu-id="2db1e-958">valueB</span></span> |
| <span data-ttu-id="2db1e-959">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="2db1e-959">json_array:subsection:1</span></span> | <span data-ttu-id="2db1e-960">valueC</span><span class="sxs-lookup"><span data-stu-id="2db1e-960">valueC</span></span> |
| <span data-ttu-id="2db1e-961">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="2db1e-961">json_array:subsection:2</span></span> | <span data-ttu-id="2db1e-962">valueD</span><span class="sxs-lookup"><span data-stu-id="2db1e-962">valueD</span></span> |

<span data-ttu-id="2db1e-963">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="2db1e-963">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="2db1e-964">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-964">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="2db1e-965">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-965">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="2db1e-966">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2db1e-966">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="2db1e-967">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2db1e-967">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="2db1e-968">0</span><span class="sxs-lookup"><span data-stu-id="2db1e-968">0</span></span>                                   | <span data-ttu-id="2db1e-969">valueB</span><span class="sxs-lookup"><span data-stu-id="2db1e-969">valueB</span></span>                              |
| <span data-ttu-id="2db1e-970">1</span><span class="sxs-lookup"><span data-stu-id="2db1e-970">1</span></span>                                   | <span data-ttu-id="2db1e-971">valueC</span><span class="sxs-lookup"><span data-stu-id="2db1e-971">valueC</span></span>                              |
| <span data-ttu-id="2db1e-972">2</span><span class="sxs-lookup"><span data-stu-id="2db1e-972">2</span></span>                                   | <span data-ttu-id="2db1e-973">valueD</span><span class="sxs-lookup"><span data-stu-id="2db1e-973">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="2db1e-974">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="2db1e-974">Custom configuration provider</span></span>

<span data-ttu-id="2db1e-975">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2db1e-975">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2db1e-976">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="2db1e-976">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2db1e-977">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="2db1e-977">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2db1e-978">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2db1e-978">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2db1e-979">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="2db1e-979">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2db1e-980">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="2db1e-980">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2db1e-981">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="2db1e-981">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2db1e-982">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-982">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2db1e-983">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-983">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2db1e-984">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="2db1e-984">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2db1e-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-985">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2db1e-986">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-986">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2db1e-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-987">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2db1e-988">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-988">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2db1e-989">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="2db1e-989">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="2db1e-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-990">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2db1e-991">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-991">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2db1e-992">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2db1e-992">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2db1e-993">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="2db1e-993">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="2db1e-994">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="2db1e-994">Access configuration during startup</span></span>

<span data-ttu-id="2db1e-995">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-995">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2db1e-996">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="2db1e-996">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="2db1e-997">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2db1e-997">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="2db1e-998">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="2db1e-998">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="2db1e-999">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="2db1e-999">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="2db1e-1000">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="2db1e-1000">In a Razor Pages page:</span></span>

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

<span data-ttu-id="2db1e-1001">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="2db1e-1001">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2db1e-1002">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="2db1e-1002">Add configuration from an external assembly</span></span>

<span data-ttu-id="2db1e-1003">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2db1e-1003">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2db1e-1004">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2db1e-1004">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2db1e-1005">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2db1e-1005">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
