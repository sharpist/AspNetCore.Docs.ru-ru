---
title: Конфигурация в .NET Core
author: rick-anderson
description: Узнайте, как использовать API конфигурации для настройки приложения ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 7715adc9b39edd4f8a5882b2e60a1b5513fe400b
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205999"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="a83b8-103">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="a83b8-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="a83b8-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="a83b8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a83b8-105">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="a83b8-106">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="a83b8-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="a83b8-107">файлы параметров, например *appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="a83b8-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="a83b8-108">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-108">Environment variables</span></span>
* <span data-ttu-id="a83b8-109">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-109">Azure Key Vault</span></span>
* <span data-ttu-id="a83b8-110">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-110">Azure App Configuration</span></span>
* <span data-ttu-id="a83b8-111">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="a83b8-111">Command-line arguments</span></span>
* <span data-ttu-id="a83b8-112">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="a83b8-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="a83b8-113">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="a83b8-113">Directory files</span></span>
* <span data-ttu-id="a83b8-114">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="a83b8-114">In-memory .NET objects</span></span>

<span data-ttu-id="a83b8-115">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a83b8-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="a83b8-116">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a83b8-116">Default configuration</span></span>

<span data-ttu-id="a83b8-117">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="a83b8-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="a83b8-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="a83b8-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="a83b8-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="a83b8-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="a83b8-120">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="a83b8-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="a83b8-121">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="a83b8-122">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="a83b8-123">Например, *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="a83b8-124">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="a83b8-125">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="a83b8-126">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="a83b8-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="a83b8-127">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="a83b8-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="a83b8-128">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="a83b8-129">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#command-line-configuration-provider) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="a83b8-129">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="a83b8-130">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="a83b8-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="a83b8-131">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="a83b8-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="a83b8-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="a83b8-132">appsettings.json</span></span>

<span data-ttu-id="a83b8-133">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="a83b8-134">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-135"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="a83b8-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="a83b8-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="a83b8-136">*appsettings.json*</span></span>
1. <span data-ttu-id="a83b8-137">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="a83b8-138">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a83b8-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="a83b8-139">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a83b8-140">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="a83b8-141">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="a83b8-141">For example, by default:</span></span>

* <span data-ttu-id="a83b8-142">В среде разработки конфигурация *appsettings*.***Development***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="a83b8-143">В рабочей среде конфигурация *appsettings*.***Production***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="a83b8-144">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="a83b8-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

#### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="a83b8-145">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="a83b8-145">Bind hierarchical configuration data using the options pattern</span></span>

<span data-ttu-id="a83b8-146">Предпочтительный способ чтения связанных значений конфигурации — использование [шаблона параметров](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a83b8-146">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a83b8-147">Например, чтобы считать следующие значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-147">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="a83b8-148">Создайте следующий класс `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-148">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a83b8-149">Все открытые свойства чтения/записи типа привязаны.</span><span class="sxs-lookup"><span data-stu-id="a83b8-149">All the public read-write properties of the type are bound.</span></span> <span data-ttu-id="a83b8-150">Поля ***не*** привязаны.</span><span class="sxs-lookup"><span data-stu-id="a83b8-150">Fields are ***not*** bound.</span></span>

<span data-ttu-id="a83b8-151">В приведенном ниже коде</span><span class="sxs-lookup"><span data-stu-id="a83b8-151">The following code:</span></span>

* <span data-ttu-id="a83b8-152">Вызывает [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) для привязки класса `PositionOptions` к разделу `Position`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-152">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="a83b8-153">Отображает данные конфигурации `Position`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-153">Displays the `Position` configuration data.</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="a83b8-154">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a83b8-155">Метод `ConfigurationBinder.Get<T>` может быть более удобным, чем `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-155">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="a83b8-156">В приведенном ниже примере кода демонстрируются способы использования `ConfigurationBinder.Get<T>` с классом `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-156">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-157">Альтернативный подход при использовании ***шаблона параметров*** — привязка раздела `Position` и добавление его в [контейнер службы внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a83b8-157">An alternative approach when using the ***options pattern*** is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a83b8-158">В следующем коде `PositionOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-158">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="a83b8-159">С помощью приведенного выше кода следующий код считывает параметры расположения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-159">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-160">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="a83b8-160">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="a83b8-161">Изменения, внесенные в файл *appsettings.json* и *appsettings.* `Environment` *.json*, ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-161">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="a83b8-162">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-162">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="a83b8-163">Безопасность и диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="a83b8-163">Security and secret manager</span></span>

<span data-ttu-id="a83b8-164">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-164">Configuration data guidelines:</span></span>

* <span data-ttu-id="a83b8-165">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="a83b8-165">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="a83b8-166">Для хранения секретов во время разработки можно использовать [диспетчер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a83b8-166">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="a83b8-167">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="a83b8-167">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a83b8-168">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-168">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a83b8-169">[По умолчанию](#default) [диспетчер секретов](xref:security/app-secrets) считывает параметры конфигурации после *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-169">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="a83b8-170">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="a83b8-170">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a83b8-171"><xref:security/app-secrets>.  Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-171"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a83b8-172">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-172">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="a83b8-173">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a83b8-173">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a83b8-174">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-174">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="a83b8-175">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-175">Environment variables</span></span>

<span data-ttu-id="a83b8-176">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json*, *appsettings.* `Environment` *.json* и [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a83b8-176">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a83b8-177">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json*, *appsettings.* `Environment` *.json* и диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-177">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a83b8-178">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-178">The following `set` commands:</span></span>

* <span data-ttu-id="a83b8-179">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="a83b8-179">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="a83b8-180">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="a83b8-180">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="a83b8-181">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="a83b8-181">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="a83b8-182">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="a83b8-182">The preceding environment settings:</span></span>

* <span data-ttu-id="a83b8-183">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="a83b8-183">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="a83b8-184">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a83b8-184">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="a83b8-185">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="a83b8-185">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="a83b8-186">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="a83b8-186">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="a83b8-187">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="a83b8-187">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="a83b8-188">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="a83b8-188">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="a83b8-189">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="a83b8-189">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="a83b8-190">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-190">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="a83b8-191">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-191">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="a83b8-192">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="a83b8-192">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="a83b8-193">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="a83b8-193">In the preceding code:</span></span>

* <span data-ttu-id="a83b8-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="a83b8-194">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="a83b8-195">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-195">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="a83b8-196">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="a83b8-196">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="a83b8-197">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="a83b8-197">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="a83b8-198">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="a83b8-198">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="a83b8-199">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="a83b8-199">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="a83b8-200">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-200">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="a83b8-201">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="a83b8-201">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="a83b8-202">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a83b8-202">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="a83b8-203">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="a83b8-203">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="a83b8-204">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="a83b8-204">Azure App Service application settings are:</span></span>

* <span data-ttu-id="a83b8-205">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-205">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="a83b8-206">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-206">Exposed as environment variables.</span></span>

<span data-ttu-id="a83b8-207">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a83b8-207">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a83b8-208">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="a83b8-208">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="a83b8-209">Командная строка</span><span class="sxs-lookup"><span data-stu-id="a83b8-209">Command-line</span></span>

<span data-ttu-id="a83b8-210">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-210">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="a83b8-211">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-211">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="a83b8-212">[Секреты приложения (диспетчер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-212">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a83b8-213">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-213">Environment variables.</span></span>

<span data-ttu-id="a83b8-214">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-214">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="a83b8-215">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="a83b8-215">Command-line arguments</span></span>

<span data-ttu-id="a83b8-216">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-216">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="a83b8-217">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-217">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="a83b8-218">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-218">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="a83b8-219">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="a83b8-219">The key value:</span></span>

* <span data-ttu-id="a83b8-220">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-220">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="a83b8-221">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-221">Isn't required if `=` is used.</span></span> <span data-ttu-id="a83b8-222">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-222">For example, `MySetting=`.</span></span>

<span data-ttu-id="a83b8-223">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-223">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="a83b8-224">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="a83b8-224">Switch mappings</span></span>

<span data-ttu-id="a83b8-225">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="a83b8-225">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="a83b8-226">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-226">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a83b8-227">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-227">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a83b8-228">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-228">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a83b8-229">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-229">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a83b8-230">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="a83b8-230">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a83b8-231">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-231">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="a83b8-232">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-232">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a83b8-233">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-233">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="a83b8-234">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="a83b8-234">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-235">Выполните следующую команду, чтобы проверить замену ключа:</span><span class="sxs-lookup"><span data-stu-id="a83b8-235">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="a83b8-236">Примечание. В настоящее время `=` нельзя использовать для задания значений замены ключа с одним тире `-`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-236">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="a83b8-237">Также см. [эту проблему в GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="a83b8-237">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="a83b8-238">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="a83b8-238">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="a83b8-239">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="a83b8-239">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a83b8-240">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-240">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a83b8-241">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="a83b8-241">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a83b8-242">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-242">Hierarchical configuration data</span></span>

<span data-ttu-id="a83b8-243">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-243">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a83b8-244">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-244">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="a83b8-245">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-245">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-246">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="a83b8-246">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="a83b8-247">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-247">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="a83b8-248">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-248"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a83b8-249">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="a83b8-249">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="a83b8-250">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-250">Configuration keys and values</span></span>

<span data-ttu-id="a83b8-251">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-251">Configuration keys:</span></span>

* <span data-ttu-id="a83b8-252">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="a83b8-252">Are case-insensitive.</span></span> <span data-ttu-id="a83b8-253">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-253">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a83b8-254">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="a83b8-254">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="a83b8-255">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="a83b8-255">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="a83b8-256">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="a83b8-256">Hierarchical keys</span></span>
  * <span data-ttu-id="a83b8-257">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="a83b8-257">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a83b8-258">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="a83b8-258">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a83b8-259">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-259">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="a83b8-260">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="a83b8-260">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="a83b8-261">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-261">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a83b8-262"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-262">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a83b8-263">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="a83b8-263">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="a83b8-264">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-264">Configuration values:</span></span>

* <span data-ttu-id="a83b8-265">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-265">Are strings.</span></span>
* <span data-ttu-id="a83b8-266">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="a83b8-266">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="a83b8-267">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-267">Configuration providers</span></span>

<span data-ttu-id="a83b8-268">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a83b8-268">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a83b8-269">Поставщик</span><span class="sxs-lookup"><span data-stu-id="a83b8-269">Provider</span></span> | <span data-ttu-id="a83b8-270">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="a83b8-270">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="a83b8-271">Поставщик конфигурации Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a83b8-271">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="a83b8-272">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-272">Azure Key Vault</span></span> |
| [<span data-ttu-id="a83b8-273">Поставщик конфигурации приложения Azure</span><span class="sxs-lookup"><span data-stu-id="a83b8-273">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="a83b8-274">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-274">Azure App Configuration</span></span> |
| [<span data-ttu-id="a83b8-275">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-275">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="a83b8-276">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-276">Command-line parameters</span></span> |
| [<span data-ttu-id="a83b8-277">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-277">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a83b8-278">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-278">Custom source</span></span> |
| [<span data-ttu-id="a83b8-279">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-279">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="a83b8-280">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-280">Environment variables</span></span> |
| [<span data-ttu-id="a83b8-281">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-281">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a83b8-282">Файлы INI, JSON и XML</span><span class="sxs-lookup"><span data-stu-id="a83b8-282">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="a83b8-283">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-283">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a83b8-284">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="a83b8-284">Directory files</span></span> |
| [<span data-ttu-id="a83b8-285">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-285">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a83b8-286">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-286">In-memory collections</span></span> |
| [<span data-ttu-id="a83b8-287">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="a83b8-287">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="a83b8-288">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-288">File in the user profile directory</span></span> |

<span data-ttu-id="a83b8-289">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-289">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="a83b8-290">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="a83b8-290">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a83b8-291">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-291">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a83b8-292">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="a83b8-292">*appsettings.json*</span></span>
1. <span data-ttu-id="a83b8-293">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="a83b8-293">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="a83b8-294">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="a83b8-294">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="a83b8-295">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-295">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="a83b8-296">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-296">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="a83b8-297">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="a83b8-297">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a83b8-298">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="a83b8-298">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="a83b8-299">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="a83b8-299">Connection string prefixes</span></span>

<span data-ttu-id="a83b8-300">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-300">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="a83b8-301">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-301">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a83b8-302">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-302">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a83b8-303">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="a83b8-303">Connection string prefix</span></span> | <span data-ttu-id="a83b8-304">Поставщик</span><span class="sxs-lookup"><span data-stu-id="a83b8-304">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a83b8-305">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-305">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a83b8-306">MySQL</span><span class="sxs-lookup"><span data-stu-id="a83b8-306">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a83b8-307">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="a83b8-307">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a83b8-308">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a83b8-308">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a83b8-309">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-309">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a83b8-310">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-310">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a83b8-311">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="a83b8-311">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a83b8-312">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-312">Environment variable key</span></span> | <span data-ttu-id="a83b8-313">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-313">Converted configuration key</span></span> | <span data-ttu-id="a83b8-314">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="a83b8-314">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-315">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="a83b8-315">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-316">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-316">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-317">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-317">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-318">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-318">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-319">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-319">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-320">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-320">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-321">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-321">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="a83b8-322">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="a83b8-322">JSON configuration provider</span></span>

<span data-ttu-id="a83b8-323"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="a83b8-324">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="a83b8-324">Overloads can specify:</span></span>

* <span data-ttu-id="a83b8-325">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="a83b8-325">Whether the file is optional.</span></span>
* <span data-ttu-id="a83b8-326">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="a83b8-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="a83b8-327">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="a83b8-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="a83b8-328">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="a83b8-328">The preceding code:</span></span>

* <span data-ttu-id="a83b8-329">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="a83b8-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="a83b8-330">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="a83b8-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="a83b8-331">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="a83b8-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="a83b8-332">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="a83b8-333">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a83b8-334">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a83b8-335">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="a83b8-336">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="a83b8-337">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="a83b8-338">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a83b8-339">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="a83b8-340">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="a83b8-341">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-341">File configuration provider</span></span>

<span data-ttu-id="a83b8-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="a83b8-342"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a83b8-343">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-343">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="a83b8-344">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="a83b8-344">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a83b8-345">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="a83b8-345">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="a83b8-346">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="a83b8-346">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a83b8-347">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="a83b8-347">INI configuration provider</span></span>

<span data-ttu-id="a83b8-348"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-348">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-349">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-349">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="a83b8-350">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-350">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="a83b8-351">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-351">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="a83b8-352">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-352">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a83b8-353">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="a83b8-354">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-354">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="a83b8-355">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="a83b8-355">XML configuration provider</span></span>

<span data-ttu-id="a83b8-356"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-356">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-357">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-357">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="a83b8-358">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-358">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="a83b8-359">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-359">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="a83b8-360">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-360">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="a83b8-361">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-361">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="a83b8-362">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-362">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-363">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-363">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="a83b8-364">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-364">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-365">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="a83b8-365">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a83b8-366">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-366">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a83b8-367">key:attribute</span><span class="sxs-lookup"><span data-stu-id="a83b8-367">key:attribute</span></span>
* <span data-ttu-id="a83b8-368">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="a83b8-368">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a83b8-369">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-369">Key-per-file configuration provider</span></span>

<span data-ttu-id="a83b8-370"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-370">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a83b8-371">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-371">The key is the file name.</span></span> <span data-ttu-id="a83b8-372">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-372">The value contains the file's contents.</span></span> <span data-ttu-id="a83b8-373">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="a83b8-373">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a83b8-374">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-374">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a83b8-375">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="a83b8-375">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a83b8-376">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-376">Overloads permit specifying:</span></span>

* <span data-ttu-id="a83b8-377">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="a83b8-377">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a83b8-378">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-378">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a83b8-379">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-379">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a83b8-380">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-380">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a83b8-381">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="a83b8-382">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-382">Memory configuration provider</span></span>

<span data-ttu-id="a83b8-383"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-383">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a83b8-384">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-384">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="a83b8-385">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-385">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-386">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="a83b8-386">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="a83b8-387">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="a83b8-387">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="a83b8-388">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-388">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="a83b8-389">GetValue</span><span class="sxs-lookup"><span data-stu-id="a83b8-389">GetValue</span></span>

<span data-ttu-id="a83b8-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="a83b8-390">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-391">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-391">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a83b8-392">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="a83b8-392">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a83b8-393">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-393">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="a83b8-394">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-394">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="a83b8-395">GetSection</span><span class="sxs-lookup"><span data-stu-id="a83b8-395">GetSection</span></span>

<span data-ttu-id="a83b8-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="a83b8-396">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a83b8-397">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-397">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-398">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-398">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-399">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-399">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a83b8-400">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-400">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a83b8-401">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="a83b8-401">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a83b8-402"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="a83b8-402">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="a83b8-403">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="a83b8-403">GetChildren and Exists</span></span>

<span data-ttu-id="a83b8-404">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-404">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-405">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="a83b8-405">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="a83b8-406">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="a83b8-406">Bind an array</span></span>

<span data-ttu-id="a83b8-407">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-407">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a83b8-408">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="a83b8-408">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="a83b8-409">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="a83b8-409">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="a83b8-410">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-410">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="a83b8-411">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-411">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-412">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="a83b8-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="a83b8-413">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-413">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="a83b8-414">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-414">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="a83b8-415">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="a83b8-415">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="a83b8-416">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="a83b8-416">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="a83b8-417">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-417">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-418">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="a83b8-418">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="a83b8-419">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-419">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a83b8-420">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="a83b8-420">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a83b8-421">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="a83b8-421">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a83b8-422">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="a83b8-422">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="a83b8-423">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="a83b8-423">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="a83b8-424">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-424">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="a83b8-425">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-425">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="a83b8-426">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="a83b8-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="a83b8-427">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="a83b8-427">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="a83b8-428">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-428">Custom configuration provider</span></span>

<span data-ttu-id="a83b8-429">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a83b8-429">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a83b8-430">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="a83b8-430">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a83b8-431">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="a83b8-431">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a83b8-432">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-432">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a83b8-433">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="a83b8-433">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a83b8-434">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="a83b8-434">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a83b8-435">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-435">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a83b8-436">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-436">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a83b8-437">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-437">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a83b8-438">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="a83b8-438">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a83b8-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-439">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a83b8-440">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-440">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a83b8-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-441">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a83b8-442">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-442">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a83b8-443">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-443">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="a83b8-444">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="a83b8-444">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="a83b8-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-445">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a83b8-446">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-446">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a83b8-447">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-447">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a83b8-448">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-448">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="a83b8-449">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="a83b8-449">Access configuration in Startup</span></span>

<span data-ttu-id="a83b8-450">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-450">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="a83b8-451">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a83b8-451">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="a83b8-452">Доступ к конфигурации в Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a83b8-452">Access configuration in Razor Pages</span></span>

<span data-ttu-id="a83b8-453">В следующем коде отображаются данные конфигурации на странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="a83b8-453">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="a83b8-454">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="a83b8-454">Access configuration in a MVC view file</span></span>

<span data-ttu-id="a83b8-455">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="a83b8-455">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a83b8-456">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="a83b8-456">Host versus app configuration</span></span>

<span data-ttu-id="a83b8-457">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a83b8-458">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="a83b8-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a83b8-459">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a83b8-460">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a83b8-461">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="a83b8-462">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a83b8-462">Default host configuration</span></span>

<span data-ttu-id="a83b8-463">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="a83b8-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="a83b8-464">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a83b8-465">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a83b8-466">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a83b8-467">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a83b8-468">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="a83b8-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="a83b8-469">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="a83b8-470">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="a83b8-471">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="a83b8-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="a83b8-472">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="a83b8-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a83b8-473">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="a83b8-473">Other configuration</span></span>

<span data-ttu-id="a83b8-474">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a83b8-475">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a83b8-476">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a83b8-477">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a83b8-478">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a83b8-479">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a83b8-480">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-480">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a83b8-481">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="a83b8-481">Add configuration from an external assembly</span></span>

<span data-ttu-id="a83b8-482">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-482">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a83b8-483">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-483">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a83b8-484">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a83b8-484">Additional resources</span></span>

* [<span data-ttu-id="a83b8-485">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-485">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a83b8-486">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-486">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="a83b8-487">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="a83b8-487">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="a83b8-488">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-488">Azure Key Vault</span></span>
* <span data-ttu-id="a83b8-489">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-489">Azure App Configuration</span></span>
* <span data-ttu-id="a83b8-490">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="a83b8-490">Command-line arguments</span></span>
* <span data-ttu-id="a83b8-491">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="a83b8-491">Custom providers (installed or created)</span></span>
* <span data-ttu-id="a83b8-492">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="a83b8-492">Directory files</span></span>
* <span data-ttu-id="a83b8-493">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-493">Environment variables</span></span>
* <span data-ttu-id="a83b8-494">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="a83b8-494">In-memory .NET objects</span></span>
* <span data-ttu-id="a83b8-495">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="a83b8-495">Settings files</span></span>

<span data-ttu-id="a83b8-496">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a83b8-496">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a83b8-497">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="a83b8-497">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="a83b8-498">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-498">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="a83b8-499">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="a83b8-499">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="a83b8-500">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-500">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a83b8-501">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a83b8-501">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a83b8-502">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="a83b8-502">Host versus app configuration</span></span>

<span data-ttu-id="a83b8-503">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-503">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a83b8-504">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="a83b8-504">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a83b8-505">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-505">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a83b8-506">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-506">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a83b8-507">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-507">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a83b8-508">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="a83b8-508">Other configuration</span></span>

<span data-ttu-id="a83b8-509">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-509">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a83b8-510">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-510">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a83b8-511">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-511">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a83b8-512">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-512">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a83b8-513">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-513">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a83b8-514">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="a83b8-514">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a83b8-515">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-515">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="a83b8-516">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a83b8-516">Default configuration</span></span>

<span data-ttu-id="a83b8-517">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-517">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="a83b8-518">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="a83b8-518">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="a83b8-519">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="a83b8-519">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="a83b8-520">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a83b8-520">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="a83b8-521">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-521">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a83b8-522">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-522">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a83b8-523">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-523">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a83b8-524">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-524">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a83b8-525">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="a83b8-525">App configuration is provided from:</span></span>
  * <span data-ttu-id="a83b8-526">Файл *appsettings.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-526">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a83b8-527">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-527">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a83b8-528">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="a83b8-528">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a83b8-529">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-529">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="a83b8-530">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a83b8-530">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="a83b8-531">Безопасность</span><span class="sxs-lookup"><span data-stu-id="a83b8-531">Security</span></span>

<span data-ttu-id="a83b8-532">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-532">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="a83b8-533">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="a83b8-533">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="a83b8-534">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="a83b8-534">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a83b8-535">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-535">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a83b8-536">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="a83b8-536">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a83b8-537"><xref:security/app-secrets> &ndash; содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-537"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a83b8-538">Менеджер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-538">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="a83b8-539">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="a83b8-539">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a83b8-540">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a83b8-540">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a83b8-541">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-541">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a83b8-542">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-542">Hierarchical configuration data</span></span>

<span data-ttu-id="a83b8-543">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-543">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a83b8-544">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-544">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="a83b8-545">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-545">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="a83b8-546">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="a83b8-546">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="a83b8-547">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-547">section0:key0</span></span>
* <span data-ttu-id="a83b8-548">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-548">section0:key1</span></span>
* <span data-ttu-id="a83b8-549">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-549">section1:key0</span></span>
* <span data-ttu-id="a83b8-550">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-550">section1:key1</span></span>

<span data-ttu-id="a83b8-551">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-551"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a83b8-552">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="a83b8-552">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="a83b8-553">Соглашения</span><span class="sxs-lookup"><span data-stu-id="a83b8-553">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="a83b8-554">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="a83b8-554">Sources and providers</span></span>

<span data-ttu-id="a83b8-555">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-555">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="a83b8-556">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="a83b8-556">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="a83b8-557">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="a83b8-557">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="a83b8-558">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-558"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a83b8-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="a83b8-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="a83b8-560">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-560">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="a83b8-561">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-561">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="a83b8-562">Клавиши</span><span class="sxs-lookup"><span data-stu-id="a83b8-562">Keys</span></span>

<span data-ttu-id="a83b8-563">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-563">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="a83b8-564">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-564">Keys are case-insensitive.</span></span> <span data-ttu-id="a83b8-565">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-565">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a83b8-566">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="a83b8-566">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="a83b8-567">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="a83b8-567">Hierarchical keys</span></span>
  * <span data-ttu-id="a83b8-568">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="a83b8-568">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a83b8-569">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="a83b8-569">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a83b8-570">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="a83b8-570">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="a83b8-571">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="a83b8-571">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a83b8-572">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="a83b8-572">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a83b8-573"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-573">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a83b8-574">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a83b8-574">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="a83b8-575">Значения</span><span class="sxs-lookup"><span data-stu-id="a83b8-575">Values</span></span>

<span data-ttu-id="a83b8-576">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-576">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="a83b8-577">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="a83b8-577">Values are strings.</span></span>
* <span data-ttu-id="a83b8-578">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="a83b8-578">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="a83b8-579">Поставщики</span><span class="sxs-lookup"><span data-stu-id="a83b8-579">Providers</span></span>

<span data-ttu-id="a83b8-580">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a83b8-580">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a83b8-581">Поставщик</span><span class="sxs-lookup"><span data-stu-id="a83b8-581">Provider</span></span> | <span data-ttu-id="a83b8-582">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="a83b8-582">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="a83b8-583">[Поставщик конфигурации хранилища ключей Azure](xref:security/key-vault-configuration) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="a83b8-583">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="a83b8-584">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-584">Azure Key Vault</span></span> |
| <span data-ttu-id="a83b8-585">[Поставщик конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="a83b8-585">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="a83b8-586">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="a83b8-586">Azure App Configuration</span></span> |
| [<span data-ttu-id="a83b8-587">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-587">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="a83b8-588">Параметры командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-588">Command-line parameters</span></span> |
| [<span data-ttu-id="a83b8-589">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-589">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a83b8-590">Источник пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-590">Custom source</span></span> |
| [<span data-ttu-id="a83b8-591">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-591">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="a83b8-592">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-592">Environment variables</span></span> |
| [<span data-ttu-id="a83b8-593">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-593">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a83b8-594">Файлы (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="a83b8-594">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="a83b8-595">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-595">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a83b8-596">Справочные файлы</span><span class="sxs-lookup"><span data-stu-id="a83b8-596">Directory files</span></span> |
| [<span data-ttu-id="a83b8-597">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-597">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a83b8-598">Коллекции оперативной памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-598">In-memory collections</span></span> |
| <span data-ttu-id="a83b8-599">[Секреты пользователей (Менеджер секретов)](xref:security/app-secrets) (раздел *Безопасность*)</span><span class="sxs-lookup"><span data-stu-id="a83b8-599">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="a83b8-600">Файл в каталоге профиля пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-600">File in the user profile directory</span></span> |

<span data-ttu-id="a83b8-601">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-601">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="a83b8-602">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="a83b8-602">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="a83b8-603">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="a83b8-603">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a83b8-604">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-604">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a83b8-605">Файлы (*appsettings.json*, *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="a83b8-605">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="a83b8-606">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="a83b8-606">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="a83b8-607">[Секреты пользователя (Менеджер секретов)](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="a83b8-607">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="a83b8-608">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-608">Environment variables</span></span>
1. <span data-ttu-id="a83b8-609">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="a83b8-609">Command-line arguments</span></span>

<span data-ttu-id="a83b8-610">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="a83b8-610">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a83b8-611">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-611">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a83b8-612">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a83b8-612">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="a83b8-613">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="a83b8-613">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="a83b8-614">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="a83b8-614">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="a83b8-615">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a83b8-615">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a83b8-616">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-616">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="a83b8-617">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-617">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="a83b8-618">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="a83b8-618">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="a83b8-619">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="a83b8-619">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="a83b8-620">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="a83b8-620">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="a83b8-621">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="a83b8-621">Consume configuration during app startup</span></span>

<span data-ttu-id="a83b8-622">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-622">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a83b8-623">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="a83b8-623">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="a83b8-624">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="a83b8-624">Command-line Configuration Provider</span></span>

<span data-ttu-id="a83b8-625"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-625">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-626">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-626">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a83b8-627">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-627">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="a83b8-628">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a83b8-628">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a83b8-629">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="a83b8-629">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a83b8-630">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="a83b8-630">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a83b8-631">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-631">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a83b8-632">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-632">Environment variables.</span></span>

<span data-ttu-id="a83b8-633">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-633">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="a83b8-634">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="a83b8-634">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="a83b8-635">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="a83b8-635">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="a83b8-636">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-636">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="a83b8-637">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-637">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a83b8-638">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="a83b8-638">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="a83b8-639">**Пример**</span><span class="sxs-lookup"><span data-stu-id="a83b8-639">**Example**</span></span>

<span data-ttu-id="a83b8-640">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-640">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="a83b8-641">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="a83b8-641">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="a83b8-642">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-642">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="a83b8-643">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-643">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a83b8-644">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-644">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="a83b8-645">Аргументы</span><span class="sxs-lookup"><span data-stu-id="a83b8-645">Arguments</span></span>

<span data-ttu-id="a83b8-646">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-646">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="a83b8-647">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-647">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="a83b8-648">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="a83b8-648">Key prefix</span></span>               | <span data-ttu-id="a83b8-649">Пример</span><span class="sxs-lookup"><span data-stu-id="a83b8-649">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="a83b8-650">Без префикса</span><span class="sxs-lookup"><span data-stu-id="a83b8-650">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="a83b8-651">Два дефиса (`--`)</span><span class="sxs-lookup"><span data-stu-id="a83b8-651">Two dashes (`--`)</span></span>        | <span data-ttu-id="a83b8-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="a83b8-652">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="a83b8-653">Прямая косая черта (`/`)</span><span class="sxs-lookup"><span data-stu-id="a83b8-653">Forward slash (`/`)</span></span>      | <span data-ttu-id="a83b8-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="a83b8-654">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="a83b8-655">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="a83b8-655">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="a83b8-656">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="a83b8-656">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="a83b8-657">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="a83b8-657">Switch mappings</span></span>

<span data-ttu-id="a83b8-658">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="a83b8-658">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="a83b8-659">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="a83b8-659">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a83b8-660">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-660">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a83b8-661">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-661">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a83b8-662">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-662">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a83b8-663">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="a83b8-663">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a83b8-664">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-664">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a83b8-665">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-665">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a83b8-666">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="a83b8-666">Create a switch mappings dictionary.</span></span> <span data-ttu-id="a83b8-667">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="a83b8-667">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="a83b8-668">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="a83b8-668">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="a83b8-669">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="a83b8-669">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a83b8-670">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-670">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a83b8-671">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="a83b8-671">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="a83b8-672">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="a83b8-672">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a83b8-673">Ключ</span><span class="sxs-lookup"><span data-stu-id="a83b8-673">Key</span></span>       | <span data-ttu-id="a83b8-674">Значение</span><span class="sxs-lookup"><span data-stu-id="a83b8-674">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="a83b8-675">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="a83b8-675">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="a83b8-676">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="a83b8-676">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="a83b8-677">Ключ</span><span class="sxs-lookup"><span data-stu-id="a83b8-677">Key</span></span>               | <span data-ttu-id="a83b8-678">Значение</span><span class="sxs-lookup"><span data-stu-id="a83b8-678">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="a83b8-679">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-679">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="a83b8-680"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-680">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-681">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-681">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a83b8-682">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-682">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="a83b8-683">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a83b8-683">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a83b8-684">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-684">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="a83b8-685">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a83b8-685">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a83b8-686">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="a83b8-686">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a83b8-687">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="a83b8-687">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="a83b8-688">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="a83b8-688">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a83b8-689">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-689">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a83b8-690">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-690">Command-line arguments.</span></span>

<span data-ttu-id="a83b8-691">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-691">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="a83b8-692">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-692">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="a83b8-693">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="a83b8-693">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="a83b8-694">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="a83b8-694">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="a83b8-695">**Пример**</span><span class="sxs-lookup"><span data-stu-id="a83b8-695">**Example**</span></span>

<span data-ttu-id="a83b8-696">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-696">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="a83b8-697">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-697">Run the sample app.</span></span> <span data-ttu-id="a83b8-698">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-698">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a83b8-699">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-699">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="a83b8-700">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-700">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="a83b8-701">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-701">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="a83b8-702">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-702">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="a83b8-703">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="a83b8-703">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="a83b8-704">Префиксы</span><span class="sxs-lookup"><span data-stu-id="a83b8-704">Prefixes</span></span>

<span data-ttu-id="a83b8-705">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-705">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="a83b8-706">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-706">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="a83b8-707">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="a83b8-707">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="a83b8-708">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-708">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="a83b8-709">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a83b8-709">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a83b8-710">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="a83b8-710">**Connection string prefixes**</span></span>

<span data-ttu-id="a83b8-711">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-711">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a83b8-712">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="a83b8-712">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a83b8-713">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="a83b8-713">Connection string prefix</span></span> | <span data-ttu-id="a83b8-714">Поставщик</span><span class="sxs-lookup"><span data-stu-id="a83b8-714">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a83b8-715">Поставщик пользователя</span><span class="sxs-lookup"><span data-stu-id="a83b8-715">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a83b8-716">MySQL</span><span class="sxs-lookup"><span data-stu-id="a83b8-716">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a83b8-717">База данных SQL Azure</span><span class="sxs-lookup"><span data-stu-id="a83b8-717">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a83b8-718">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a83b8-718">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a83b8-719">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-719">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a83b8-720">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a83b8-720">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a83b8-721">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="a83b8-721">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a83b8-722">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="a83b8-722">Environment variable key</span></span> | <span data-ttu-id="a83b8-723">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-723">Converted configuration key</span></span> | <span data-ttu-id="a83b8-724">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="a83b8-724">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-725">Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="a83b8-725">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-726">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-727">Значение: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-727">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-728">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-729">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-729">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a83b8-730">Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a83b8-731">Значение: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a83b8-731">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="a83b8-732">**Пример**</span><span class="sxs-lookup"><span data-stu-id="a83b8-732">**Example**</span></span>

<span data-ttu-id="a83b8-733">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="a83b8-733">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="a83b8-734">Имя &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="a83b8-734">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="a83b8-735">Значение &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="a83b8-735">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="a83b8-736">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="a83b8-736">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="a83b8-737">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-737">File Configuration Provider</span></span>

<span data-ttu-id="a83b8-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="a83b8-738"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a83b8-739">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="a83b8-739">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="a83b8-740">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="a83b8-740">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a83b8-741">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="a83b8-741">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="a83b8-742">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="a83b8-742">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a83b8-743">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="a83b8-743">INI Configuration Provider</span></span>

<span data-ttu-id="a83b8-744"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-744">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-745">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-745">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a83b8-746">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="a83b8-746">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="a83b8-747">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-747">Overloads permit specifying:</span></span>

* <span data-ttu-id="a83b8-748">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="a83b8-748">Whether the file is optional.</span></span>
* <span data-ttu-id="a83b8-749">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="a83b8-749">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a83b8-750"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-750">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a83b8-751">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-751">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a83b8-752">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-752">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="a83b8-753">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-753">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a83b8-754">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-754">section0:key0</span></span>
* <span data-ttu-id="a83b8-755">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-755">section0:key1</span></span>
* <span data-ttu-id="a83b8-756">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="a83b8-756">section1:subsection:key</span></span>
* <span data-ttu-id="a83b8-757">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="a83b8-757">section2:subsection0:key</span></span>
* <span data-ttu-id="a83b8-758">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="a83b8-758">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="a83b8-759">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="a83b8-759">JSON Configuration Provider</span></span>

<span data-ttu-id="a83b8-760"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-760">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="a83b8-761">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-761">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a83b8-762">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-762">Overloads permit specifying:</span></span>

* <span data-ttu-id="a83b8-763">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="a83b8-763">Whether the file is optional.</span></span>
* <span data-ttu-id="a83b8-764">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="a83b8-764">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a83b8-765"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-765">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a83b8-766">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-766">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a83b8-767">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="a83b8-767">The method is called to load configuration from:</span></span>

* <span data-ttu-id="a83b8-768">*appsettings.json* &ndash; первым читается этот файл.</span><span class="sxs-lookup"><span data-stu-id="a83b8-768">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="a83b8-769">Версия файла среды может переопределить значения, предоставленные *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-769">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="a83b8-770">*appsettings.{Environment}.json* &ndash; версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a83b8-770">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="a83b8-771">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="a83b8-771">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a83b8-772">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="a83b8-772">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a83b8-773">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="a83b8-773">Environment variables.</span></span>
* <span data-ttu-id="a83b8-774">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-774">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a83b8-775">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-775">Command-line arguments.</span></span>

<span data-ttu-id="a83b8-776">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-776">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="a83b8-777">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-777">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="a83b8-778">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-778">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a83b8-779">**Пример**</span><span class="sxs-lookup"><span data-stu-id="a83b8-779">**Example**</span></span>

<span data-ttu-id="a83b8-780">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-780">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="a83b8-781">Первый вызов `AddJsonFile` загружает конфигурацию из *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-781">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="a83b8-782">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-782">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="a83b8-783">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="a83b8-783">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="a83b8-784">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-784">Run the sample app.</span></span> <span data-ttu-id="a83b8-785">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-785">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a83b8-786">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-786">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="a83b8-787">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="a83b8-787">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="a83b8-788">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="a83b8-788">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="a83b8-789">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-789">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="a83b8-790">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-790">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="a83b8-791">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="a83b8-791">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="a83b8-792">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-792">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="a83b8-793">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-793">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="a83b8-794">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="a83b8-794">XML Configuration Provider</span></span>

<span data-ttu-id="a83b8-795"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-795">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a83b8-796">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-796">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a83b8-797">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-797">Overloads permit specifying:</span></span>

* <span data-ttu-id="a83b8-798">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="a83b8-798">Whether the file is optional.</span></span>
* <span data-ttu-id="a83b8-799">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="a83b8-799">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a83b8-800"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-800">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a83b8-801">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-801">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="a83b8-802">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="a83b8-802">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="a83b8-803">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a83b8-804">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-804">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="a83b8-805">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-805">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a83b8-806">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-806">section0:key0</span></span>
* <span data-ttu-id="a83b8-807">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-807">section0:key1</span></span>
* <span data-ttu-id="a83b8-808">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-808">section1:key0</span></span>
* <span data-ttu-id="a83b8-809">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-809">section1:key1</span></span>

<span data-ttu-id="a83b8-810">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-810">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="a83b8-811">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-811">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a83b8-812">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-812">section:section0:key:key0</span></span>
* <span data-ttu-id="a83b8-813">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-813">section:section0:key:key1</span></span>
* <span data-ttu-id="a83b8-814">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-814">section:section1:key:key0</span></span>
* <span data-ttu-id="a83b8-815">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-815">section:section1:key:key1</span></span>

<span data-ttu-id="a83b8-816">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="a83b8-816">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a83b8-817">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-817">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a83b8-818">key:attribute</span><span class="sxs-lookup"><span data-stu-id="a83b8-818">key:attribute</span></span>
* <span data-ttu-id="a83b8-819">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="a83b8-819">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a83b8-820">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="a83b8-820">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="a83b8-821"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-821">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a83b8-822">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-822">The key is the file name.</span></span> <span data-ttu-id="a83b8-823">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="a83b8-823">The value contains the file's contents.</span></span> <span data-ttu-id="a83b8-824">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="a83b8-824">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a83b8-825">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-825">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a83b8-826">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="a83b8-826">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a83b8-827">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="a83b8-827">Overloads permit specifying:</span></span>

* <span data-ttu-id="a83b8-828">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="a83b8-828">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a83b8-829">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-829">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a83b8-830">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-830">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a83b8-831">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-831">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a83b8-832">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-832">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="a83b8-833">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="a83b8-833">Memory Configuration Provider</span></span>

<span data-ttu-id="a83b8-834"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-834">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a83b8-835">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-835">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a83b8-836">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-836">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="a83b8-837">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-837">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a83b8-838">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-838">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="a83b8-839">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="a83b8-839">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="a83b8-840">GetValue</span><span class="sxs-lookup"><span data-stu-id="a83b8-840">GetValue</span></span>

<span data-ttu-id="a83b8-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="a83b8-841">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="a83b8-842">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a83b8-842">An overload accepts a default value.</span></span>

<span data-ttu-id="a83b8-843">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="a83b8-843">The following example:</span></span>

* <span data-ttu-id="a83b8-844">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-844">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="a83b8-845">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-845">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="a83b8-846">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-846">Types the value as an `int`.</span></span>
* <span data-ttu-id="a83b8-847">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="a83b8-847">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a83b8-848">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="a83b8-848">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a83b8-849">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="a83b8-849">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="a83b8-850">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="a83b8-850">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="a83b8-851">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-851">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="a83b8-852">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-852">section0:key0</span></span>
* <span data-ttu-id="a83b8-853">section0:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-853">section0:key1</span></span>
* <span data-ttu-id="a83b8-854">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-854">section1:key0</span></span>
* <span data-ttu-id="a83b8-855">section1:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-855">section1:key1</span></span>
* <span data-ttu-id="a83b8-856">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-856">section2:subsection0:key0</span></span>
* <span data-ttu-id="a83b8-857">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-857">section2:subsection0:key1</span></span>
* <span data-ttu-id="a83b8-858">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="a83b8-858">section2:subsection1:key0</span></span>
* <span data-ttu-id="a83b8-859">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="a83b8-859">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="a83b8-860">GetSection</span><span class="sxs-lookup"><span data-stu-id="a83b8-860">GetSection</span></span>

<span data-ttu-id="a83b8-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="a83b8-861">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a83b8-862">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="a83b8-862">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="a83b8-863">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="a83b8-863">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="a83b8-864">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="a83b8-864">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="a83b8-865">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-865">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a83b8-866">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-866">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a83b8-867">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="a83b8-867">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a83b8-868"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="a83b8-868">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="a83b8-869">GetChildren</span><span class="sxs-lookup"><span data-stu-id="a83b8-869">GetChildren</span></span>

<span data-ttu-id="a83b8-870">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="a83b8-870">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="a83b8-871">Exists</span><span class="sxs-lookup"><span data-stu-id="a83b8-871">Exists</span></span>

<span data-ttu-id="a83b8-872">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-872">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="a83b8-873">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-873">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="a83b8-874">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="a83b8-874">Bind to an object graph</span></span>

<span data-ttu-id="a83b8-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="a83b8-875"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="a83b8-876">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="a83b8-876">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="a83b8-877">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="a83b8-877">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="a83b8-878">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-878">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="a83b8-879">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-879">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="a83b8-880">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="a83b8-880">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="a83b8-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="a83b8-881">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a83b8-882">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-882">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="a83b8-883">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="a83b8-883">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a83b8-884">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="a83b8-884">Bind an array to a class</span></span>

<span data-ttu-id="a83b8-885">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="a83b8-885">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="a83b8-886"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-886">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a83b8-887">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="a83b8-887">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="a83b8-888">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="a83b8-888">Binding is provided by convention.</span></span> <span data-ttu-id="a83b8-889">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="a83b8-889">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="a83b8-890">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="a83b8-890">**In-memory array processing**</span></span>

<span data-ttu-id="a83b8-891">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="a83b8-891">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="a83b8-892">Ключ</span><span class="sxs-lookup"><span data-stu-id="a83b8-892">Key</span></span>             | <span data-ttu-id="a83b8-893">Значение</span><span class="sxs-lookup"><span data-stu-id="a83b8-893">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a83b8-894">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="a83b8-894">array:entries:0</span></span> | <span data-ttu-id="a83b8-895">value0</span><span class="sxs-lookup"><span data-stu-id="a83b8-895">value0</span></span> |
| <span data-ttu-id="a83b8-896">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="a83b8-896">array:entries:1</span></span> | <span data-ttu-id="a83b8-897">value1</span><span class="sxs-lookup"><span data-stu-id="a83b8-897">value1</span></span> |
| <span data-ttu-id="a83b8-898">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="a83b8-898">array:entries:2</span></span> | <span data-ttu-id="a83b8-899">value2</span><span class="sxs-lookup"><span data-stu-id="a83b8-899">value2</span></span> |
| <span data-ttu-id="a83b8-900">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="a83b8-900">array:entries:4</span></span> | <span data-ttu-id="a83b8-901">value4</span><span class="sxs-lookup"><span data-stu-id="a83b8-901">value4</span></span> |
| <span data-ttu-id="a83b8-902">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="a83b8-902">array:entries:5</span></span> | <span data-ttu-id="a83b8-903">value5</span><span class="sxs-lookup"><span data-stu-id="a83b8-903">value5</span></span> |

<span data-ttu-id="a83b8-904">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="a83b8-904">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="a83b8-905">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="a83b8-905">The array skips a value for index &num;3.</span></span> <span data-ttu-id="a83b8-906">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="a83b8-906">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="a83b8-907">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-907">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="a83b8-908">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="a83b8-908">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="a83b8-909">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="a83b8-909">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="a83b8-910">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-910">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="a83b8-911">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a83b8-911">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a83b8-912">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a83b8-912">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a83b8-913">0</span><span class="sxs-lookup"><span data-stu-id="a83b8-913">0</span></span>                            | <span data-ttu-id="a83b8-914">value0</span><span class="sxs-lookup"><span data-stu-id="a83b8-914">value0</span></span>                       |
| <span data-ttu-id="a83b8-915">1</span><span class="sxs-lookup"><span data-stu-id="a83b8-915">1</span></span>                            | <span data-ttu-id="a83b8-916">value1</span><span class="sxs-lookup"><span data-stu-id="a83b8-916">value1</span></span>                       |
| <span data-ttu-id="a83b8-917">2</span><span class="sxs-lookup"><span data-stu-id="a83b8-917">2</span></span>                            | <span data-ttu-id="a83b8-918">value2</span><span class="sxs-lookup"><span data-stu-id="a83b8-918">value2</span></span>                       |
| <span data-ttu-id="a83b8-919">3</span><span class="sxs-lookup"><span data-stu-id="a83b8-919">3</span></span>                            | <span data-ttu-id="a83b8-920">value4</span><span class="sxs-lookup"><span data-stu-id="a83b8-920">value4</span></span>                       |
| <span data-ttu-id="a83b8-921">4</span><span class="sxs-lookup"><span data-stu-id="a83b8-921">4</span></span>                            | <span data-ttu-id="a83b8-922">value5</span><span class="sxs-lookup"><span data-stu-id="a83b8-922">value5</span></span>                       |

<span data-ttu-id="a83b8-923">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-923">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a83b8-924">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="a83b8-924">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a83b8-925">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="a83b8-925">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a83b8-926">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-926">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="a83b8-927">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-927">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="a83b8-928">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-928">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="a83b8-929">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a83b8-929">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="a83b8-930">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-930">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="a83b8-931">Ключ</span><span class="sxs-lookup"><span data-stu-id="a83b8-931">Key</span></span>             | <span data-ttu-id="a83b8-932">Значение</span><span class="sxs-lookup"><span data-stu-id="a83b8-932">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a83b8-933">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="a83b8-933">array:entries:3</span></span> | <span data-ttu-id="a83b8-934">value3</span><span class="sxs-lookup"><span data-stu-id="a83b8-934">value3</span></span> |

<span data-ttu-id="a83b8-935">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="a83b8-935">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="a83b8-936">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a83b8-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a83b8-937">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a83b8-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a83b8-938">0</span><span class="sxs-lookup"><span data-stu-id="a83b8-938">0</span></span>                            | <span data-ttu-id="a83b8-939">value0</span><span class="sxs-lookup"><span data-stu-id="a83b8-939">value0</span></span>                       |
| <span data-ttu-id="a83b8-940">1</span><span class="sxs-lookup"><span data-stu-id="a83b8-940">1</span></span>                            | <span data-ttu-id="a83b8-941">value1</span><span class="sxs-lookup"><span data-stu-id="a83b8-941">value1</span></span>                       |
| <span data-ttu-id="a83b8-942">2</span><span class="sxs-lookup"><span data-stu-id="a83b8-942">2</span></span>                            | <span data-ttu-id="a83b8-943">value2</span><span class="sxs-lookup"><span data-stu-id="a83b8-943">value2</span></span>                       |
| <span data-ttu-id="a83b8-944">3</span><span class="sxs-lookup"><span data-stu-id="a83b8-944">3</span></span>                            | <span data-ttu-id="a83b8-945">value3</span><span class="sxs-lookup"><span data-stu-id="a83b8-945">value3</span></span>                       |
| <span data-ttu-id="a83b8-946">4</span><span class="sxs-lookup"><span data-stu-id="a83b8-946">4</span></span>                            | <span data-ttu-id="a83b8-947">value4</span><span class="sxs-lookup"><span data-stu-id="a83b8-947">value4</span></span>                       |
| <span data-ttu-id="a83b8-948">5</span><span class="sxs-lookup"><span data-stu-id="a83b8-948">5</span></span>                            | <span data-ttu-id="a83b8-949">value5</span><span class="sxs-lookup"><span data-stu-id="a83b8-949">value5</span></span>                       |

<span data-ttu-id="a83b8-950">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="a83b8-950">**JSON array processing**</span></span>

<span data-ttu-id="a83b8-951">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="a83b8-951">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="a83b8-952">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="a83b8-952">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="a83b8-953">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="a83b8-953">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="a83b8-954">Ключ</span><span class="sxs-lookup"><span data-stu-id="a83b8-954">Key</span></span>                     | <span data-ttu-id="a83b8-955">Значение</span><span class="sxs-lookup"><span data-stu-id="a83b8-955">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="a83b8-956">json_array:key</span><span class="sxs-lookup"><span data-stu-id="a83b8-956">json_array:key</span></span>          | <span data-ttu-id="a83b8-957">valueA</span><span class="sxs-lookup"><span data-stu-id="a83b8-957">valueA</span></span> |
| <span data-ttu-id="a83b8-958">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="a83b8-958">json_array:subsection:0</span></span> | <span data-ttu-id="a83b8-959">valueB</span><span class="sxs-lookup"><span data-stu-id="a83b8-959">valueB</span></span> |
| <span data-ttu-id="a83b8-960">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="a83b8-960">json_array:subsection:1</span></span> | <span data-ttu-id="a83b8-961">valueC</span><span class="sxs-lookup"><span data-stu-id="a83b8-961">valueC</span></span> |
| <span data-ttu-id="a83b8-962">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="a83b8-962">json_array:subsection:2</span></span> | <span data-ttu-id="a83b8-963">valueD</span><span class="sxs-lookup"><span data-stu-id="a83b8-963">valueD</span></span> |

<span data-ttu-id="a83b8-964">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="a83b8-964">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="a83b8-965">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-965">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="a83b8-966">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-966">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="a83b8-967">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a83b8-967">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="a83b8-968">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a83b8-968">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="a83b8-969">0</span><span class="sxs-lookup"><span data-stu-id="a83b8-969">0</span></span>                                   | <span data-ttu-id="a83b8-970">valueB</span><span class="sxs-lookup"><span data-stu-id="a83b8-970">valueB</span></span>                              |
| <span data-ttu-id="a83b8-971">1</span><span class="sxs-lookup"><span data-stu-id="a83b8-971">1</span></span>                                   | <span data-ttu-id="a83b8-972">valueC</span><span class="sxs-lookup"><span data-stu-id="a83b8-972">valueC</span></span>                              |
| <span data-ttu-id="a83b8-973">2</span><span class="sxs-lookup"><span data-stu-id="a83b8-973">2</span></span>                                   | <span data-ttu-id="a83b8-974">valueD</span><span class="sxs-lookup"><span data-stu-id="a83b8-974">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="a83b8-975">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="a83b8-975">Custom configuration provider</span></span>

<span data-ttu-id="a83b8-976">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a83b8-976">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a83b8-977">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="a83b8-977">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a83b8-978">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="a83b8-978">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a83b8-979">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="a83b8-979">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a83b8-980">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="a83b8-980">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a83b8-981">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="a83b8-981">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a83b8-982">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="a83b8-982">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a83b8-983">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-983">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a83b8-984">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-984">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a83b8-985">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="a83b8-985">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a83b8-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-986">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a83b8-987">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-987">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a83b8-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-988">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a83b8-989">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-989">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a83b8-990">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="a83b8-990">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="a83b8-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-991">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a83b8-992">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-992">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a83b8-993">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a83b8-993">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a83b8-994">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="a83b8-994">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="a83b8-995">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="a83b8-995">Access configuration during startup</span></span>

<span data-ttu-id="a83b8-996">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-996">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a83b8-997">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="a83b8-997">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="a83b8-998">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a83b8-998">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="a83b8-999">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="a83b8-999">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="a83b8-1000">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="a83b8-1000">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="a83b8-1001">На странице Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a83b8-1001">In a Razor Pages page:</span></span>

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

<span data-ttu-id="a83b8-1002">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="a83b8-1002">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a83b8-1003">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="a83b8-1003">Add configuration from an external assembly</span></span>

<span data-ttu-id="a83b8-1004">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="a83b8-1004">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a83b8-1005">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a83b8-1005">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a83b8-1006">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a83b8-1006">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
