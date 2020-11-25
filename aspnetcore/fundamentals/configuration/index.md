---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c04dcc65f7518d2d8b32cdce7a7fbb756dd8ec3a
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417543"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="f5e1a-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="f5e1a-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="f5e1a-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5e1a-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="f5e1a-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="f5e1a-107">файлов параметров, таких как *appsettings.json* ;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="f5e1a-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-108">Environment variables</span></span>
* <span data-ttu-id="f5e1a-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-109">Azure Key Vault</span></span>
* <span data-ttu-id="f5e1a-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-110">Azure App Configuration</span></span>
* <span data-ttu-id="f5e1a-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-111">Command-line arguments</span></span>
* <span data-ttu-id="f5e1a-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="f5e1a-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-113">Directory files</span></span>
* <span data-ttu-id="f5e1a-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-114">In-memory .NET objects</span></span>

<span data-ttu-id="f5e1a-115">В этом разделе приведены сведения о конфигурации в ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="f5e1a-116">Сведения об использовании конфигурации в консольных приложениях см. в статье [Конфигурация .NET](/dotnet/core/extensions/configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="f5e1a-117">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5e1a-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="f5e1a-118">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f5e1a-118">Default configuration</span></span>

<span data-ttu-id="f5e1a-119">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="f5e1a-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="f5e1a-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="f5e1a-122">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="f5e1a-123">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="f5e1a-124">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="f5e1a-125">Например, *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="f5e1a-126">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="f5e1a-127">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="f5e1a-128">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="f5e1a-129">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="f5e1a-130">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="f5e1a-131">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#clcp) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="f5e1a-132">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="f5e1a-133">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="f5e1a-134">Рассмотрим следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="f5e1a-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="f5e1a-135">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-136"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="f5e1a-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production\*\*_._json* и *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="f5e1a-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="f5e1a-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="f5e1a-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="f5e1a-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-141">For example, by default:</span></span>

* <span data-ttu-id="f5e1a-142">В среде разработки конфигурация *appsettings*.***Development** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="f5e1a-143">В рабочей среде конфигурация *appsettings*.***Production** _._json* перезаписывает значения, найденные в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="f5e1a-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="f5e1a-145">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="f5e1a-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="f5e1a-146">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="f5e1a-147">Изменения, внесенные в файлы *appsettings.json* и *appsettings.* `Environment` *.json* \***после** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="f5e1a-148">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="f5e1a-149">Объединение коллекций служб</span><span class="sxs-lookup"><span data-stu-id="f5e1a-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="f5e1a-150">Безопасность и диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="f5e1a-150">Security and secret manager</span></span>

<span data-ttu-id="f5e1a-151">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-151">Configuration data guidelines:</span></span>

<span data-ttu-id="f5e1a-152">_Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="f5e1a-153">Для хранения секретов во время разработки можно использовать [диспетчер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="f5e1a-154">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="f5e1a-155">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="f5e1a-156">[По умолчанию](#default) [диспетчер секретов](xref:security/app-secrets) считывает параметры конфигурации после *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="f5e1a-157">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="f5e1a-158"><xref:security/app-secrets>.  Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="f5e1a-159">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="f5e1a-160">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="f5e1a-161">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="f5e1a-162">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-162">Environment variables</span></span>

<span data-ttu-id="f5e1a-163">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json* , *appsettings.* `Environment` *.json* и [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f5e1a-164">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json* , *appsettings.* `Environment` *.json* и диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="f5e1a-165">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-165">The following `set` commands:</span></span>

* <span data-ttu-id="f5e1a-166">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="f5e1a-167">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="f5e1a-168">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="f5e1a-169">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-169">The preceding environment settings:</span></span>

* <span data-ttu-id="f5e1a-170">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="f5e1a-171">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="f5e1a-172">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="f5e1a-173">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="f5e1a-174">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="f5e1a-175">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="f5e1a-176">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="f5e1a-177">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="f5e1a-178">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="f5e1a-179">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="f5e1a-180">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-180">In the preceding code:</span></span>

* <span data-ttu-id="f5e1a-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="f5e1a-182">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="f5e1a-183">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="f5e1a-184">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="f5e1a-185">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="f5e1a-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="f5e1a-186">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="f5e1a-187">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="f5e1a-188">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="f5e1a-189">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="f5e1a-190">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="f5e1a-191">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="f5e1a-192">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="f5e1a-193">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-193">Exposed as environment variables.</span></span>

<span data-ttu-id="f5e1a-194">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="f5e1a-195">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="f5e1a-196">Именование переменных среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-196">Naming of environment variables</span></span>

<span data-ttu-id="f5e1a-197">Имена переменных среды отражают структуру файла *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-197">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="f5e1a-198">Каждый элемент в иерархии отделяется двойным символом подчеркивания (предпочтительно) или двоеточием.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-198">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="f5e1a-199">Если структура элемента включает массив, индекс массива должен рассматриваться как дополнительное имя элемента в этом пути.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-199">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="f5e1a-200">Рассмотрим следующий файл *appsettings.json* и его эквивалентные значения, представленные в виде переменных среды:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-200">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="f5e1a-201">**Переменные среды**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-201">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="f5e1a-202">Переменные среды, заданные в launchSettings.json</span><span class="sxs-lookup"><span data-stu-id="f5e1a-202">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="f5e1a-203">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-203">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="f5e1a-204">Командная строка</span><span class="sxs-lookup"><span data-stu-id="f5e1a-204">Command-line</span></span>

<span data-ttu-id="f5e1a-205">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-205">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="f5e1a-206">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-206">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="f5e1a-207">[Секреты приложения (диспетчер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-207">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="f5e1a-208">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-208">Environment variables.</span></span>

<span data-ttu-id="f5e1a-209">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-209">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="f5e1a-210">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-210">Command-line arguments</span></span>

<span data-ttu-id="f5e1a-211">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-211">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="f5e1a-212">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-212">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="f5e1a-213">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-213">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="f5e1a-214">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-214">The key value:</span></span>

* <span data-ttu-id="f5e1a-215">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-215">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="f5e1a-216">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-216">Isn't required if `=` is used.</span></span> <span data-ttu-id="f5e1a-217">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-217">For example, `MySetting=`.</span></span>

<span data-ttu-id="f5e1a-218">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-218">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="f5e1a-219">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="f5e1a-219">Switch mappings</span></span>

<span data-ttu-id="f5e1a-220">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-220">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="f5e1a-221">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-221">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="f5e1a-222">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-222">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="f5e1a-223">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-223">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="f5e1a-224">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-224">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="f5e1a-225">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-225">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="f5e1a-226">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-226">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="f5e1a-227">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-227">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="f5e1a-228">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-228">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="f5e1a-229">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-229">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-230">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-230">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="f5e1a-231">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-231">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="f5e1a-232">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-232">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f5e1a-233">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-233">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="f5e1a-234">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-234">Hierarchical configuration data</span></span>

<span data-ttu-id="f5e1a-235">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-235">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="f5e1a-236">[Пример скачивания](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="f5e1a-236">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="f5e1a-237">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-237">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-238">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-238">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="f5e1a-239">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-239">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="f5e1a-240">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="f5e1a-241">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-241">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="f5e1a-242">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-242">Configuration keys and values</span></span>

<span data-ttu-id="f5e1a-243">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-243">Configuration keys:</span></span>

* <span data-ttu-id="f5e1a-244">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-244">Are case-insensitive.</span></span> <span data-ttu-id="f5e1a-245">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-245">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="f5e1a-246">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-246">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="f5e1a-247">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-247">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="f5e1a-248">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="f5e1a-248">Hierarchical keys</span></span>
  * <span data-ttu-id="f5e1a-249">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-249">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="f5e1a-250">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-250">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="f5e1a-251">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-251">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="f5e1a-252">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-252">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="f5e1a-253">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-253">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="f5e1a-254"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-254">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f5e1a-255">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-255">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="f5e1a-256">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-256">Configuration values:</span></span>

* <span data-ttu-id="f5e1a-257">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-257">Are strings.</span></span>
* <span data-ttu-id="f5e1a-258">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-258">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="f5e1a-259">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-259">Configuration providers</span></span>

<span data-ttu-id="f5e1a-260">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-260">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="f5e1a-261">Поставщик</span><span class="sxs-lookup"><span data-stu-id="f5e1a-261">Provider</span></span> | <span data-ttu-id="f5e1a-262">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="f5e1a-262">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="f5e1a-263">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f5e1a-263">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="f5e1a-264">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-264">Azure Key Vault</span></span> |
| [<span data-ttu-id="f5e1a-265">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="f5e1a-265">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="f5e1a-266">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-266">Azure App Configuration</span></span> |
| [<span data-ttu-id="f5e1a-267">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-267">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="f5e1a-268">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-268">Command-line parameters</span></span> |
| [<span data-ttu-id="f5e1a-269">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-269">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="f5e1a-270">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-270">Custom source</span></span> |
| [<span data-ttu-id="f5e1a-271">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-271">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="f5e1a-272">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-272">Environment variables</span></span> |
| [<span data-ttu-id="f5e1a-273">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-273">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="f5e1a-274">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="f5e1a-274">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="f5e1a-275">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-275">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="f5e1a-276">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-276">Directory files</span></span> |
| [<span data-ttu-id="f5e1a-277">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-277">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="f5e1a-278">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-278">In-memory collections</span></span> |
| [<span data-ttu-id="f5e1a-279">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="f5e1a-279">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="f5e1a-280">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-280">File in the user profile directory</span></span> |

<span data-ttu-id="f5e1a-281">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-281">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="f5e1a-282">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-282">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="f5e1a-283">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-283">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="f5e1a-284">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="f5e1a-284">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="f5e1a-285">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="f5e1a-285">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="f5e1a-286">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-286">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="f5e1a-287">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-287">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="f5e1a-288">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-288">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="f5e1a-289">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-289">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="f5e1a-290">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-290">Connection string prefixes</span></span>

<span data-ttu-id="f5e1a-291">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-291">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="f5e1a-292">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-292">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="f5e1a-293">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-293">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="f5e1a-294">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-294">Connection string prefix</span></span> | <span data-ttu-id="f5e1a-295">Поставщик</span><span class="sxs-lookup"><span data-stu-id="f5e1a-295">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="f5e1a-296">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-296">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="f5e1a-297">MySQL</span><span class="sxs-lookup"><span data-stu-id="f5e1a-297">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="f5e1a-298">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="f5e1a-298">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="f5e1a-299">SQL Server</span><span class="sxs-lookup"><span data-stu-id="f5e1a-299">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="f5e1a-300">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-300">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="f5e1a-301">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-301">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="f5e1a-302">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-302">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="f5e1a-303">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-303">Environment variable key</span></span> | <span data-ttu-id="f5e1a-304">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-304">Converted configuration key</span></span> | <span data-ttu-id="f5e1a-305">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="f5e1a-305">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-306">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-306">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-307">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-308">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-308">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-309">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-309">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-310">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-310">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-311">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-311">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-312">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-312">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="f5e1a-313">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-313">File configuration provider</span></span>

<span data-ttu-id="f5e1a-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="f5e1a-315">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-315">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="f5e1a-316">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="f5e1a-316">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="f5e1a-317">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="f5e1a-317">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="f5e1a-318">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="f5e1a-318">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="f5e1a-319">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="f5e1a-319">INI configuration provider</span></span>

<span data-ttu-id="f5e1a-320"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-320">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-321">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="f5e1a-322">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-322">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="f5e1a-323">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-323">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="f5e1a-324">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-324">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="f5e1a-325">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="f5e1a-326">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="f5e1a-327">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="f5e1a-327">JSON configuration provider</span></span>

<span data-ttu-id="f5e1a-328"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-328">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="f5e1a-329">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-329">Overloads can specify:</span></span>

* <span data-ttu-id="f5e1a-330">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-330">Whether the file is optional.</span></span>
* <span data-ttu-id="f5e1a-331">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-331">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="f5e1a-332">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-332">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="f5e1a-333">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-333">The preceding code:</span></span>

* <span data-ttu-id="f5e1a-334">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-334">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="f5e1a-335">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-335">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="f5e1a-336">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-336">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="f5e1a-337">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-337">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="f5e1a-338">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-338">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="f5e1a-339">Обычно \***не** _ требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-339">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="f5e1a-340">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-340">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="f5e1a-341">В приведенном выше коде параметры в файлах _MyConfig.json\* и  *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-341">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="f5e1a-342">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-342">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="f5e1a-343">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-343">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="f5e1a-344">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-344">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="f5e1a-345">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-345">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="f5e1a-346">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="f5e1a-346">XML configuration provider</span></span>

<span data-ttu-id="f5e1a-347"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-347">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-348">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="f5e1a-349">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-349">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="f5e1a-350">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-350">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="f5e1a-351">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-351">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="f5e1a-352">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="f5e1a-353">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-354">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-354">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="f5e1a-355">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-355">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-356">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-356">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="f5e1a-357">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-357">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f5e1a-358">key:attribute</span><span class="sxs-lookup"><span data-stu-id="f5e1a-358">key:attribute</span></span>
* <span data-ttu-id="f5e1a-359">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="f5e1a-359">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="f5e1a-360">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-360">Key-per-file configuration provider</span></span>

<span data-ttu-id="f5e1a-361"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-361">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="f5e1a-362">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-362">The key is the file name.</span></span> <span data-ttu-id="f5e1a-363">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-363">The value contains the file's contents.</span></span> <span data-ttu-id="f5e1a-364">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-364">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="f5e1a-365">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-365">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="f5e1a-366">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-366">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="f5e1a-367">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-367">Overloads permit specifying:</span></span>

* <span data-ttu-id="f5e1a-368">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-368">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="f5e1a-369">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-369">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="f5e1a-370">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-370">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="f5e1a-371">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-371">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="f5e1a-372">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-372">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="f5e1a-373">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-373">Memory configuration provider</span></span>

<span data-ttu-id="f5e1a-374"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-374">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="f5e1a-375">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-375">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="f5e1a-376">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-376">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-377">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-377">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="f5e1a-378">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-378">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="f5e1a-379">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-379">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="f5e1a-380">GetValue</span><span class="sxs-lookup"><span data-stu-id="f5e1a-380">GetValue</span></span>

<span data-ttu-id="f5e1a-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-382">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-382">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="f5e1a-383">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="f5e1a-383">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="f5e1a-384">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-384">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="f5e1a-385">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-385">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="f5e1a-386">GetSection</span><span class="sxs-lookup"><span data-stu-id="f5e1a-386">GetSection</span></span>

<span data-ttu-id="f5e1a-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="f5e1a-388">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-388">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-389">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-389">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-390">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-390">`GetSection` never returns `null`.</span></span> <span data-ttu-id="f5e1a-391">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-391">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="f5e1a-392">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-392">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="f5e1a-393"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="f5e1a-394">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="f5e1a-394">GetChildren and Exists</span></span>

<span data-ttu-id="f5e1a-395">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-395">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-396">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-396">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="f5e1a-397">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="f5e1a-397">Bind an array</span></span>

<span data-ttu-id="f5e1a-398">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-398">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f5e1a-399">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-399">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="f5e1a-400">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="f5e1a-400">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="f5e1a-401">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-401">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="f5e1a-402">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-402">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-403">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="f5e1a-404">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-404">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="f5e1a-405">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-405">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="f5e1a-406">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="f5e1a-406">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="f5e1a-407">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-407">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="f5e1a-408">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-408">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-409">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-409">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="f5e1a-410">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-410">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="f5e1a-411">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-411">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="f5e1a-412">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-412">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="f5e1a-413">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-413">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="f5e1a-414">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-414">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="f5e1a-415">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-415">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="f5e1a-416">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-416">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-417">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-417">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="f5e1a-418">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-418">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="f5e1a-419">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-419">Custom configuration provider</span></span>

<span data-ttu-id="f5e1a-420">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-420">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="f5e1a-421">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-421">The provider has the following characteristics:</span></span>

* <span data-ttu-id="f5e1a-422">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-422">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="f5e1a-423">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-423">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="f5e1a-424">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-424">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="f5e1a-425">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-425">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="f5e1a-426">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-426">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="f5e1a-427">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-427">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="f5e1a-428">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-428">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="f5e1a-429">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-429">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="f5e1a-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="f5e1a-431">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-431">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="f5e1a-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="f5e1a-433">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-433">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="f5e1a-434">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-434">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="f5e1a-435">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-435">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="f5e1a-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="f5e1a-437">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-437">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="f5e1a-438">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-438">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="f5e1a-439">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-439">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="f5e1a-440">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="f5e1a-440">Access configuration in Startup</span></span>

<span data-ttu-id="f5e1a-441">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-441">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="f5e1a-442">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-442">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="f5e1a-443">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="f5e1a-443">Access configuration in Razor Pages</span></span>

<span data-ttu-id="f5e1a-444">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-444">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="f5e1a-445">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-445">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="f5e1a-446">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-446">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="f5e1a-447">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="f5e1a-447">Access configuration in a MVC view file</span></span>

<span data-ttu-id="f5e1a-448">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-448">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="f5e1a-449">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="f5e1a-449">Configure options with a delegate</span></span>

<span data-ttu-id="f5e1a-450">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-450">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="f5e1a-451">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-451">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="f5e1a-452">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-452">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="f5e1a-453">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-453">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="f5e1a-454">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-454">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="f5e1a-455">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json* , а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-455">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="f5e1a-456">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-456">Host versus app configuration</span></span>

<span data-ttu-id="f5e1a-457">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="f5e1a-458">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="f5e1a-459">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="f5e1a-460">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="f5e1a-461">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="f5e1a-462">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f5e1a-462">Default host configuration</span></span>

<span data-ttu-id="f5e1a-463">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="f5e1a-464">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="f5e1a-465">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="f5e1a-466">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f5e1a-467">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="f5e1a-468">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="f5e1a-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="f5e1a-469">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="f5e1a-470">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="f5e1a-471">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="f5e1a-472">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="f5e1a-473">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="f5e1a-473">Other configuration</span></span>

<span data-ttu-id="f5e1a-474">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="f5e1a-475">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="f5e1a-476">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="f5e1a-477">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="f5e1a-478">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="f5e1a-479">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="f5e1a-480">Переменные среды, заданные в *launchSettings.json*, переопределяют переменные, заданные в системной среде.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-480">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="f5e1a-481">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="f5e1a-482">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="f5e1a-483">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="f5e1a-484">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5e1a-485">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f5e1a-485">Additional resources</span></span>

* [<span data-ttu-id="f5e1a-486">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5e1a-487">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="f5e1a-488">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="f5e1a-489">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-489">Azure Key Vault</span></span>
* <span data-ttu-id="f5e1a-490">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-490">Azure App Configuration</span></span>
* <span data-ttu-id="f5e1a-491">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-491">Command-line arguments</span></span>
* <span data-ttu-id="f5e1a-492">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="f5e1a-493">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-493">Directory files</span></span>
* <span data-ttu-id="f5e1a-494">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-494">Environment variables</span></span>
* <span data-ttu-id="f5e1a-495">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-495">In-memory .NET objects</span></span>
* <span data-ttu-id="f5e1a-496">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-496">Settings files</span></span>

<span data-ttu-id="f5e1a-497">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f5e1a-498">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="f5e1a-499">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="f5e1a-500">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="f5e1a-501">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f5e1a-502">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5e1a-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="f5e1a-503">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-503">Host versus app configuration</span></span>

<span data-ttu-id="f5e1a-504">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="f5e1a-505">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="f5e1a-506">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="f5e1a-507">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="f5e1a-508">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="f5e1a-509">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="f5e1a-509">Other configuration</span></span>

<span data-ttu-id="f5e1a-510">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="f5e1a-511">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="f5e1a-512">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="f5e1a-513">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="f5e1a-514">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="f5e1a-515">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="f5e1a-516">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="f5e1a-517">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="f5e1a-517">Default configuration</span></span>

<span data-ttu-id="f5e1a-518">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="f5e1a-519">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="f5e1a-520">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="f5e1a-521">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="f5e1a-522">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="f5e1a-523">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f5e1a-524">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f5e1a-525">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="f5e1a-526">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="f5e1a-527">Файл *appsettings.json* , использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f5e1a-528">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f5e1a-529">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="f5e1a-530">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="f5e1a-531">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="f5e1a-532">Безопасность</span><span class="sxs-lookup"><span data-stu-id="f5e1a-532">Security</span></span>

<span data-ttu-id="f5e1a-533">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="f5e1a-534">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="f5e1a-535">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="f5e1a-536">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="f5e1a-537">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="f5e1a-538"><xref:security/app-secrets>. Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-538"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="f5e1a-539">Менеджер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="f5e1a-540">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="f5e1a-541">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="f5e1a-542">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="f5e1a-543">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-543">Hierarchical configuration data</span></span>

<span data-ttu-id="f5e1a-544">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="f5e1a-545">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="f5e1a-546">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="f5e1a-547">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="f5e1a-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-548">section0:key0</span></span>
* <span data-ttu-id="f5e1a-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-549">section0:key1</span></span>
* <span data-ttu-id="f5e1a-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-550">section1:key0</span></span>
* <span data-ttu-id="f5e1a-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-551">section1:key1</span></span>

<span data-ttu-id="f5e1a-552">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="f5e1a-553">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="f5e1a-554">Соглашения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="f5e1a-555">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="f5e1a-555">Sources and providers</span></span>

<span data-ttu-id="f5e1a-556">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="f5e1a-557">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="f5e1a-558">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="f5e1a-559">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f5e1a-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="f5e1a-561">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="f5e1a-562">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="f5e1a-563">Клавиши</span><span class="sxs-lookup"><span data-stu-id="f5e1a-563">Keys</span></span>

<span data-ttu-id="f5e1a-564">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="f5e1a-565">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-565">Keys are case-insensitive.</span></span> <span data-ttu-id="f5e1a-566">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="f5e1a-567">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="f5e1a-568">Дополнительные сведения о повторяющихся ключах JSON см. в [этой проблеме на GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-568">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="f5e1a-569">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="f5e1a-569">Hierarchical keys</span></span>
  * <span data-ttu-id="f5e1a-570">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-570">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="f5e1a-571">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-571">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="f5e1a-572">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-572">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="f5e1a-573">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-573">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f5e1a-574">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-574">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="f5e1a-575"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-575">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f5e1a-576">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-576">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="f5e1a-577">Значения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-577">Values</span></span>

<span data-ttu-id="f5e1a-578">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-578">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="f5e1a-579">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-579">Values are strings.</span></span>
* <span data-ttu-id="f5e1a-580">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-580">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="f5e1a-581">Поставщики</span><span class="sxs-lookup"><span data-stu-id="f5e1a-581">Providers</span></span>

<span data-ttu-id="f5e1a-582">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-582">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="f5e1a-583">Поставщик</span><span class="sxs-lookup"><span data-stu-id="f5e1a-583">Provider</span></span> | <span data-ttu-id="f5e1a-584">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-584">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="f5e1a-585">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-585">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="f5e1a-586">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-586">Azure Key Vault</span></span> |
| <span data-ttu-id="f5e1a-587">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-587">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="f5e1a-588">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-588">Azure App Configuration</span></span> |
| [<span data-ttu-id="f5e1a-589">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-589">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="f5e1a-590">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-590">Command-line parameters</span></span> |
| [<span data-ttu-id="f5e1a-591">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-591">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="f5e1a-592">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-592">Custom source</span></span> |
| [<span data-ttu-id="f5e1a-593">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-593">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="f5e1a-594">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-594">Environment variables</span></span> |
| [<span data-ttu-id="f5e1a-595">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-595">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="f5e1a-596">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-596">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="f5e1a-597">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-597">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="f5e1a-598">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="f5e1a-598">Directory files</span></span> |
| [<span data-ttu-id="f5e1a-599">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-599">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="f5e1a-600">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-600">In-memory collections</span></span> |
| <span data-ttu-id="f5e1a-601">[Секреты пользователей (Менеджер секретов)](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-601">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="f5e1a-602">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-602">File in the user profile directory</span></span> |

<span data-ttu-id="f5e1a-603">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-603">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="f5e1a-604">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-604">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="f5e1a-605">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-605">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="f5e1a-606">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-606">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="f5e1a-607">Файлы ( *appsettings.json* , *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-607">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="f5e1a-608">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="f5e1a-608">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="f5e1a-609">[Секреты пользователя (Менеджер секретов)](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-609">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="f5e1a-610">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-610">Environment variables</span></span>
1. <span data-ttu-id="f5e1a-611">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-611">Command-line arguments</span></span>

<span data-ttu-id="f5e1a-612">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-612">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="f5e1a-613">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-613">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f5e1a-614">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-614">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="f5e1a-615">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="f5e1a-615">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="f5e1a-616">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-616">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="f5e1a-617">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="f5e1a-617">ConfigureAppConfiguration</span></span>

<span data-ttu-id="f5e1a-618">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-618">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="f5e1a-619">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-619">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="f5e1a-620">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-620">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="f5e1a-621">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="f5e1a-621">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="f5e1a-622">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="f5e1a-622">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="f5e1a-623">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-623">Consume configuration during app startup</span></span>

<span data-ttu-id="f5e1a-624">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-624">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5e1a-625">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-625">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="f5e1a-626">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-626">Command-line Configuration Provider</span></span>

<span data-ttu-id="f5e1a-627"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-627">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-628">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-628">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f5e1a-629">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-629">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="f5e1a-630">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-630">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="f5e1a-631">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-631">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f5e1a-632">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-632">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="f5e1a-633">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-633">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="f5e1a-634">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-634">Environment variables.</span></span>

<span data-ttu-id="f5e1a-635">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-635">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="f5e1a-636">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-636">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="f5e1a-637">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-637">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="f5e1a-638">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-638">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="f5e1a-639">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-639">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f5e1a-640">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-640">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="f5e1a-641">**Пример**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-641">**Example**</span></span>

<span data-ttu-id="f5e1a-642">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-642">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="f5e1a-643">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-643">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="f5e1a-644">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-644">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="f5e1a-645">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-645">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f5e1a-646">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-646">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="f5e1a-647">Аргументы</span><span class="sxs-lookup"><span data-stu-id="f5e1a-647">Arguments</span></span>

<span data-ttu-id="f5e1a-648">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-648">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="f5e1a-649">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-649">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="f5e1a-650">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="f5e1a-650">Key prefix</span></span>               | <span data-ttu-id="f5e1a-651">Пример</span><span class="sxs-lookup"><span data-stu-id="f5e1a-651">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="f5e1a-652">Без префикса</span><span class="sxs-lookup"><span data-stu-id="f5e1a-652">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="f5e1a-653">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-653">Two dashes (`--`)</span></span>        | <span data-ttu-id="f5e1a-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="f5e1a-655">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="f5e1a-655">Forward slash (`/`)</span></span>      | <span data-ttu-id="f5e1a-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="f5e1a-657">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-657">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="f5e1a-658">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-658">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="f5e1a-659">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="f5e1a-659">Switch mappings</span></span>

<span data-ttu-id="f5e1a-660">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-660">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="f5e1a-661">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-661">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="f5e1a-662">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-662">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="f5e1a-663">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-663">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="f5e1a-664">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-664">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="f5e1a-665">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-665">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="f5e1a-666">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-666">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="f5e1a-667">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-667">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="f5e1a-668">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-668">Create a switch mappings dictionary.</span></span> <span data-ttu-id="f5e1a-669">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-669">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="f5e1a-670">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-670">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="f5e1a-671">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-671">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="f5e1a-672">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-672">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f5e1a-673">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-673">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="f5e1a-674">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-674">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="f5e1a-675">Ключ</span><span class="sxs-lookup"><span data-stu-id="f5e1a-675">Key</span></span>       | <span data-ttu-id="f5e1a-676">Значение</span><span class="sxs-lookup"><span data-stu-id="f5e1a-676">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="f5e1a-677">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-677">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="f5e1a-678">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-678">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="f5e1a-679">Ключ</span><span class="sxs-lookup"><span data-stu-id="f5e1a-679">Key</span></span>               | <span data-ttu-id="f5e1a-680">Значение</span><span class="sxs-lookup"><span data-stu-id="f5e1a-680">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="f5e1a-681">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-681">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="f5e1a-682"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-682">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-683">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-683">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="f5e1a-684">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="f5e1a-685">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-685">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="f5e1a-686">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-686">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="f5e1a-687">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-687">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="f5e1a-688">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-688">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f5e1a-689">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-689">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="f5e1a-690">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="f5e1a-690">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="f5e1a-691">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-691">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="f5e1a-692">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-692">Command-line arguments.</span></span>

<span data-ttu-id="f5e1a-693">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-693">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="f5e1a-694">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-694">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="f5e1a-695">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-695">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="f5e1a-696">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-696">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="f5e1a-697">**Пример**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-697">**Example**</span></span>

<span data-ttu-id="f5e1a-698">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-698">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="f5e1a-699">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-699">Run the sample app.</span></span> <span data-ttu-id="f5e1a-700">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-700">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f5e1a-701">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-701">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="f5e1a-702">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-702">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="f5e1a-703">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-703">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="f5e1a-704">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-704">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="f5e1a-705">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-705">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="f5e1a-706">Префиксы</span><span class="sxs-lookup"><span data-stu-id="f5e1a-706">Prefixes</span></span>

<span data-ttu-id="f5e1a-707">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-707">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="f5e1a-708">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-708">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="f5e1a-709">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="f5e1a-709">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="f5e1a-710">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-710">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="f5e1a-711">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-711">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="f5e1a-712">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-712">**Connection string prefixes**</span></span>

<span data-ttu-id="f5e1a-713">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-713">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="f5e1a-714">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-714">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="f5e1a-715">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="f5e1a-715">Connection string prefix</span></span> | <span data-ttu-id="f5e1a-716">Поставщик</span><span class="sxs-lookup"><span data-stu-id="f5e1a-716">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="f5e1a-717">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="f5e1a-717">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="f5e1a-718">MySQL</span><span class="sxs-lookup"><span data-stu-id="f5e1a-718">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="f5e1a-719">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="f5e1a-719">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="f5e1a-720">SQL Server</span><span class="sxs-lookup"><span data-stu-id="f5e1a-720">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="f5e1a-721">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-721">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="f5e1a-722">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-722">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="f5e1a-723">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-723">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="f5e1a-724">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="f5e1a-724">Environment variable key</span></span> | <span data-ttu-id="f5e1a-725">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-725">Converted configuration key</span></span> | <span data-ttu-id="f5e1a-726">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="f5e1a-726">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-727">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-727">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-728">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-729">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-729">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-730">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-731">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-731">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="f5e1a-732">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-732">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="f5e1a-733">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-733">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="f5e1a-734">**Пример**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-734">**Example**</span></span>

<span data-ttu-id="f5e1a-735">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-735">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="f5e1a-736">Имя: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-736">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="f5e1a-737">Значение: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-737">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="f5e1a-738">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-738">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="f5e1a-739">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-739">File Configuration Provider</span></span>

<span data-ttu-id="f5e1a-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="f5e1a-741">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-741">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="f5e1a-742">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="f5e1a-742">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="f5e1a-743">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="f5e1a-743">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="f5e1a-744">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="f5e1a-744">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="f5e1a-745">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="f5e1a-745">INI Configuration Provider</span></span>

<span data-ttu-id="f5e1a-746"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-746">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-747">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-747">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f5e1a-748">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-748">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="f5e1a-749">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-749">Overloads permit specifying:</span></span>

* <span data-ttu-id="f5e1a-750">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-750">Whether the file is optional.</span></span>
* <span data-ttu-id="f5e1a-751">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-751">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f5e1a-752"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-752">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f5e1a-753">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-753">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="f5e1a-754">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-754">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="f5e1a-755">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-755">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f5e1a-756">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-756">section0:key0</span></span>
* <span data-ttu-id="f5e1a-757">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-757">section0:key1</span></span>
* <span data-ttu-id="f5e1a-758">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="f5e1a-758">section1:subsection:key</span></span>
* <span data-ttu-id="f5e1a-759">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="f5e1a-759">section2:subsection0:key</span></span>
* <span data-ttu-id="f5e1a-760">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="f5e1a-760">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="f5e1a-761">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="f5e1a-761">JSON Configuration Provider</span></span>

<span data-ttu-id="f5e1a-762"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-762">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="f5e1a-763">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-763">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f5e1a-764">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-764">Overloads permit specifying:</span></span>

* <span data-ttu-id="f5e1a-765">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-765">Whether the file is optional.</span></span>
* <span data-ttu-id="f5e1a-766">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-766">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f5e1a-767"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-767">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f5e1a-768">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-768">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f5e1a-769">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-769">The method is called to load configuration from:</span></span>

* <span data-ttu-id="f5e1a-770">*appsettings.json* : Этот файл считывается первым.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-770">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="f5e1a-771">Версия файла среды может переопределить значения, предоставленные файлом *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-771">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="f5e1a-772">*appsettings.{Environment}.json*: Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-772">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="f5e1a-773">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-773">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="f5e1a-774">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-774">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f5e1a-775">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-775">Environment variables.</span></span>
* <span data-ttu-id="f5e1a-776">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-776">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="f5e1a-777">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-777">Command-line arguments.</span></span>

<span data-ttu-id="f5e1a-778">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-778">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="f5e1a-779">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-779">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="f5e1a-780">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-780">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="f5e1a-781">**Пример**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-781">**Example**</span></span>

<span data-ttu-id="f5e1a-782">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-782">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="f5e1a-783">Первый вызов `AddJsonFile` загружает конфигурацию из файла *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="f5e1a-783">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="f5e1a-784">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-784">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="f5e1a-785">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-785">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="f5e1a-786">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-786">Run the sample app.</span></span> <span data-ttu-id="f5e1a-787">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-787">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f5e1a-788">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-788">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="f5e1a-789">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-789">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="f5e1a-790">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-790">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="f5e1a-791">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-791">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="f5e1a-792">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-792">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="f5e1a-793">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-793">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="f5e1a-794">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5e1a-794">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="f5e1a-795">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-795">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="f5e1a-796">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="f5e1a-796">XML Configuration Provider</span></span>

<span data-ttu-id="f5e1a-797"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-797">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="f5e1a-798">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-798">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f5e1a-799">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-799">Overloads permit specifying:</span></span>

* <span data-ttu-id="f5e1a-800">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-800">Whether the file is optional.</span></span>
* <span data-ttu-id="f5e1a-801">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-801">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f5e1a-802"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-802">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f5e1a-803">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-803">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="f5e1a-804">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-804">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="f5e1a-805">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-805">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="f5e1a-806">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-806">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="f5e1a-807">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f5e1a-808">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-808">section0:key0</span></span>
* <span data-ttu-id="f5e1a-809">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-809">section0:key1</span></span>
* <span data-ttu-id="f5e1a-810">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-810">section1:key0</span></span>
* <span data-ttu-id="f5e1a-811">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-811">section1:key1</span></span>

<span data-ttu-id="f5e1a-812">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-812">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="f5e1a-813">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f5e1a-814">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-814">section:section0:key:key0</span></span>
* <span data-ttu-id="f5e1a-815">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-815">section:section0:key:key1</span></span>
* <span data-ttu-id="f5e1a-816">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-816">section:section1:key:key0</span></span>
* <span data-ttu-id="f5e1a-817">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-817">section:section1:key:key1</span></span>

<span data-ttu-id="f5e1a-818">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-818">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="f5e1a-819">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-819">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f5e1a-820">key:attribute</span><span class="sxs-lookup"><span data-stu-id="f5e1a-820">key:attribute</span></span>
* <span data-ttu-id="f5e1a-821">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="f5e1a-821">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="f5e1a-822">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="f5e1a-822">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="f5e1a-823"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-823">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="f5e1a-824">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-824">The key is the file name.</span></span> <span data-ttu-id="f5e1a-825">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-825">The value contains the file's contents.</span></span> <span data-ttu-id="f5e1a-826">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-826">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="f5e1a-827">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-827">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="f5e1a-828">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-828">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="f5e1a-829">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-829">Overloads permit specifying:</span></span>

* <span data-ttu-id="f5e1a-830">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-830">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="f5e1a-831">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-831">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="f5e1a-832">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-832">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="f5e1a-833">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-833">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="f5e1a-834">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="f5e1a-835">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="f5e1a-835">Memory Configuration Provider</span></span>

<span data-ttu-id="f5e1a-836"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-836">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="f5e1a-837">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-837">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f5e1a-838">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-838">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="f5e1a-839">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-839">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="f5e1a-840">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-840">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="f5e1a-841">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-841">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="f5e1a-842">GetValue</span><span class="sxs-lookup"><span data-stu-id="f5e1a-842">GetValue</span></span>

<span data-ttu-id="f5e1a-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="f5e1a-844">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-844">An overload accepts a default value.</span></span>

<span data-ttu-id="f5e1a-845">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-845">The following example:</span></span>

* <span data-ttu-id="f5e1a-846">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-846">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="f5e1a-847">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-847">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="f5e1a-848">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-848">Types the value as an `int`.</span></span>
* <span data-ttu-id="f5e1a-849">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-849">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="f5e1a-850">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="f5e1a-850">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="f5e1a-851">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-851">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="f5e1a-852">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-852">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="f5e1a-853">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-853">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="f5e1a-854">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-854">section0:key0</span></span>
* <span data-ttu-id="f5e1a-855">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-855">section0:key1</span></span>
* <span data-ttu-id="f5e1a-856">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-856">section1:key0</span></span>
* <span data-ttu-id="f5e1a-857">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-857">section1:key1</span></span>
* <span data-ttu-id="f5e1a-858">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-858">section2:subsection0:key0</span></span>
* <span data-ttu-id="f5e1a-859">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-859">section2:subsection0:key1</span></span>
* <span data-ttu-id="f5e1a-860">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-860">section2:subsection1:key0</span></span>
* <span data-ttu-id="f5e1a-861">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-861">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="f5e1a-862">GetSection</span><span class="sxs-lookup"><span data-stu-id="f5e1a-862">GetSection</span></span>

<span data-ttu-id="f5e1a-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="f5e1a-864">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-864">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="f5e1a-865">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-865">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="f5e1a-866">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-866">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="f5e1a-867">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-867">`GetSection` never returns `null`.</span></span> <span data-ttu-id="f5e1a-868">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-868">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="f5e1a-869">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-869">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="f5e1a-870"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="f5e1a-871">GetChildren</span><span class="sxs-lookup"><span data-stu-id="f5e1a-871">GetChildren</span></span>

<span data-ttu-id="f5e1a-872">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-872">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="f5e1a-873">Exists</span><span class="sxs-lookup"><span data-stu-id="f5e1a-873">Exists</span></span>

<span data-ttu-id="f5e1a-874">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="f5e1a-875">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-875">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="f5e1a-876">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="f5e1a-876">Bind to an object graph</span></span>

<span data-ttu-id="f5e1a-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="f5e1a-878">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-878">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="f5e1a-879">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-879">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="f5e1a-880">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-880">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="f5e1a-881">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-881">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="f5e1a-882">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-882">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="f5e1a-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="f5e1a-884">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-884">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="f5e1a-885">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-885">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f5e1a-886">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="f5e1a-886">Bind an array to a class</span></span>

<span data-ttu-id="f5e1a-887">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="f5e1a-887">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="f5e1a-888"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-888">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f5e1a-889">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-889">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="f5e1a-890">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-890">Binding is provided by convention.</span></span> <span data-ttu-id="f5e1a-891">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-891">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="f5e1a-892">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-892">**In-memory array processing**</span></span>

<span data-ttu-id="f5e1a-893">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-893">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="f5e1a-894">Ключ</span><span class="sxs-lookup"><span data-stu-id="f5e1a-894">Key</span></span>             | <span data-ttu-id="f5e1a-895">Значение</span><span class="sxs-lookup"><span data-stu-id="f5e1a-895">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f5e1a-896">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-896">array:entries:0</span></span> | <span data-ttu-id="f5e1a-897">value0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-897">value0</span></span> |
| <span data-ttu-id="f5e1a-898">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-898">array:entries:1</span></span> | <span data-ttu-id="f5e1a-899">value1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-899">value1</span></span> |
| <span data-ttu-id="f5e1a-900">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-900">array:entries:2</span></span> | <span data-ttu-id="f5e1a-901">value2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-901">value2</span></span> |
| <span data-ttu-id="f5e1a-902">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-902">array:entries:4</span></span> | <span data-ttu-id="f5e1a-903">value4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-903">value4</span></span> |
| <span data-ttu-id="f5e1a-904">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="f5e1a-904">array:entries:5</span></span> | <span data-ttu-id="f5e1a-905">value5</span><span class="sxs-lookup"><span data-stu-id="f5e1a-905">value5</span></span> |

<span data-ttu-id="f5e1a-906">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-906">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="f5e1a-907">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-907">The array skips a value for index &num;3.</span></span> <span data-ttu-id="f5e1a-908">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-908">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="f5e1a-909">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-909">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="f5e1a-910">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-910">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="f5e1a-911">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="f5e1a-912">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-912">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="f5e1a-913">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-913">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f5e1a-914">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-914">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f5e1a-915">0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-915">0</span></span>                            | <span data-ttu-id="f5e1a-916">value0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-916">value0</span></span>                       |
| <span data-ttu-id="f5e1a-917">1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-917">1</span></span>                            | <span data-ttu-id="f5e1a-918">value1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-918">value1</span></span>                       |
| <span data-ttu-id="f5e1a-919">2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-919">2</span></span>                            | <span data-ttu-id="f5e1a-920">value2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-920">value2</span></span>                       |
| <span data-ttu-id="f5e1a-921">3</span><span class="sxs-lookup"><span data-stu-id="f5e1a-921">3</span></span>                            | <span data-ttu-id="f5e1a-922">value4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-922">value4</span></span>                       |
| <span data-ttu-id="f5e1a-923">4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-923">4</span></span>                            | <span data-ttu-id="f5e1a-924">value5</span><span class="sxs-lookup"><span data-stu-id="f5e1a-924">value5</span></span>                       |

<span data-ttu-id="f5e1a-925">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-925">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="f5e1a-926">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-926">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="f5e1a-927">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-927">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="f5e1a-928">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-928">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="f5e1a-929">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-929">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="f5e1a-930">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-930">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="f5e1a-931">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-931">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="f5e1a-932">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-932">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="f5e1a-933">Ключ</span><span class="sxs-lookup"><span data-stu-id="f5e1a-933">Key</span></span>             | <span data-ttu-id="f5e1a-934">Значение</span><span class="sxs-lookup"><span data-stu-id="f5e1a-934">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f5e1a-935">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="f5e1a-935">array:entries:3</span></span> | <span data-ttu-id="f5e1a-936">value3</span><span class="sxs-lookup"><span data-stu-id="f5e1a-936">value3</span></span> |

<span data-ttu-id="f5e1a-937">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-937">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="f5e1a-938">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-938">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f5e1a-939">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-939">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f5e1a-940">0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-940">0</span></span>                            | <span data-ttu-id="f5e1a-941">value0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-941">value0</span></span>                       |
| <span data-ttu-id="f5e1a-942">1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-942">1</span></span>                            | <span data-ttu-id="f5e1a-943">value1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-943">value1</span></span>                       |
| <span data-ttu-id="f5e1a-944">2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-944">2</span></span>                            | <span data-ttu-id="f5e1a-945">value2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-945">value2</span></span>                       |
| <span data-ttu-id="f5e1a-946">3</span><span class="sxs-lookup"><span data-stu-id="f5e1a-946">3</span></span>                            | <span data-ttu-id="f5e1a-947">value3</span><span class="sxs-lookup"><span data-stu-id="f5e1a-947">value3</span></span>                       |
| <span data-ttu-id="f5e1a-948">4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-948">4</span></span>                            | <span data-ttu-id="f5e1a-949">value4</span><span class="sxs-lookup"><span data-stu-id="f5e1a-949">value4</span></span>                       |
| <span data-ttu-id="f5e1a-950">5</span><span class="sxs-lookup"><span data-stu-id="f5e1a-950">5</span></span>                            | <span data-ttu-id="f5e1a-951">value5</span><span class="sxs-lookup"><span data-stu-id="f5e1a-951">value5</span></span>                       |

<span data-ttu-id="f5e1a-952">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="f5e1a-952">**JSON array processing**</span></span>

<span data-ttu-id="f5e1a-953">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-953">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="f5e1a-954">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-954">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="f5e1a-955">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="f5e1a-955">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="f5e1a-956">Ключ</span><span class="sxs-lookup"><span data-stu-id="f5e1a-956">Key</span></span>                     | <span data-ttu-id="f5e1a-957">Значение</span><span class="sxs-lookup"><span data-stu-id="f5e1a-957">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="f5e1a-958">json_array:key</span><span class="sxs-lookup"><span data-stu-id="f5e1a-958">json_array:key</span></span>          | <span data-ttu-id="f5e1a-959">valueA</span><span class="sxs-lookup"><span data-stu-id="f5e1a-959">valueA</span></span> |
| <span data-ttu-id="f5e1a-960">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-960">json_array:subsection:0</span></span> | <span data-ttu-id="f5e1a-961">valueB</span><span class="sxs-lookup"><span data-stu-id="f5e1a-961">valueB</span></span> |
| <span data-ttu-id="f5e1a-962">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-962">json_array:subsection:1</span></span> | <span data-ttu-id="f5e1a-963">valueC</span><span class="sxs-lookup"><span data-stu-id="f5e1a-963">valueC</span></span> |
| <span data-ttu-id="f5e1a-964">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-964">json_array:subsection:2</span></span> | <span data-ttu-id="f5e1a-965">valueD</span><span class="sxs-lookup"><span data-stu-id="f5e1a-965">valueD</span></span> |

<span data-ttu-id="f5e1a-966">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-966">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="f5e1a-967">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-967">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="f5e1a-968">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-968">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="f5e1a-969">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-969">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="f5e1a-970">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="f5e1a-970">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="f5e1a-971">0</span><span class="sxs-lookup"><span data-stu-id="f5e1a-971">0</span></span>                                   | <span data-ttu-id="f5e1a-972">valueB</span><span class="sxs-lookup"><span data-stu-id="f5e1a-972">valueB</span></span>                              |
| <span data-ttu-id="f5e1a-973">1</span><span class="sxs-lookup"><span data-stu-id="f5e1a-973">1</span></span>                                   | <span data-ttu-id="f5e1a-974">valueC</span><span class="sxs-lookup"><span data-stu-id="f5e1a-974">valueC</span></span>                              |
| <span data-ttu-id="f5e1a-975">2</span><span class="sxs-lookup"><span data-stu-id="f5e1a-975">2</span></span>                                   | <span data-ttu-id="f5e1a-976">valueD</span><span class="sxs-lookup"><span data-stu-id="f5e1a-976">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="f5e1a-977">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="f5e1a-977">Custom configuration provider</span></span>

<span data-ttu-id="f5e1a-978">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-978">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="f5e1a-979">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-979">The provider has the following characteristics:</span></span>

* <span data-ttu-id="f5e1a-980">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-980">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="f5e1a-981">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-981">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="f5e1a-982">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-982">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="f5e1a-983">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-983">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="f5e1a-984">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-984">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="f5e1a-985">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-985">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="f5e1a-986">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-986">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="f5e1a-987">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-987">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="f5e1a-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="f5e1a-989">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-989">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="f5e1a-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="f5e1a-991">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-991">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="f5e1a-992">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-992">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="f5e1a-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="f5e1a-994">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-994">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="f5e1a-995">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-995">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="f5e1a-996">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-996">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="f5e1a-997">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="f5e1a-997">Access configuration during startup</span></span>

<span data-ttu-id="f5e1a-998">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-998">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f5e1a-999">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-999">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="f5e1a-1000">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1000">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="f5e1a-1001">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1001">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="f5e1a-1002">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1002">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="f5e1a-1003">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1003">In a Razor Pages page:</span></span>

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

<span data-ttu-id="f5e1a-1004">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1004">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="f5e1a-1005">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1005">Add configuration from an external assembly</span></span>

<span data-ttu-id="f5e1a-1006">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1006">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="f5e1a-1007">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1007">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5e1a-1008">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f5e1a-1008">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
