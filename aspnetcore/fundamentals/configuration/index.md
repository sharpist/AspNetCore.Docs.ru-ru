---
<span data-ttu-id="5407e-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-102">'Blazor'</span></span>
- <span data-ttu-id="5407e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-103">'Identity'</span></span>
- <span data-ttu-id="5407e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-105">'Razor'</span></span>
- <span data-ttu-id="5407e-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="5407e-107">Конфигурация в .NET Core</span><span class="sxs-lookup"><span data-stu-id="5407e-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="5407e-108">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)</span><span class="sxs-lookup"><span data-stu-id="5407e-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5407e-109">Настройка в ASP.NET Core выполняется с помощью одного или нескольких [поставщиков конфигурации](#cp).</span><span class="sxs-lookup"><span data-stu-id="5407e-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="5407e-110">Поставщики конфигурации получают данные конфигурации в парах "ключ-значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="5407e-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="5407e-111">файлы параметров, например *appsettings.json*;</span><span class="sxs-lookup"><span data-stu-id="5407e-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="5407e-112">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5407e-112">Environment variables</span></span>
* <span data-ttu-id="5407e-113">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="5407e-113">Azure Key Vault</span></span>
* <span data-ttu-id="5407e-114">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="5407e-114">Azure App Configuration</span></span>
* <span data-ttu-id="5407e-115">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="5407e-115">Command-line arguments</span></span>
* <span data-ttu-id="5407e-116">пользовательские поставщики, установленные или созданные;</span><span class="sxs-lookup"><span data-stu-id="5407e-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="5407e-117">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="5407e-117">Directory files</span></span>
* <span data-ttu-id="5407e-118">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="5407e-118">In-memory .NET objects</span></span>

<span data-ttu-id="5407e-119">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5407e-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="5407e-120">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5407e-120">Default configuration</span></span>

<span data-ttu-id="5407e-121">Веб-приложения ASP.NET Core, созданные с помощью [dotnet new](/dotnet/core/tools/dotnet-new) или Visual Studio, создают следующий код:</span><span class="sxs-lookup"><span data-stu-id="5407e-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="5407e-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="5407e-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="5407e-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  добавляет существующий `IConfiguration` в качестве источника.</span><span class="sxs-lookup"><span data-stu-id="5407e-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="5407e-124">В случае конфигурации по умолчанию добавляет конфигурацию [узла](#hvac) и задает ее в качестве первого источника для конфигурации _приложения_.</span><span class="sxs-lookup"><span data-stu-id="5407e-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="5407e-125">Файл [appsettings.json](#appsettingsjson), использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="5407e-126">Файл *appsettings.* `Environment` *.json*, использующий [поставщик конфигурации JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="5407e-127">Например, *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="5407e-128">[Секреты приложения](xref:security/app-secrets), когда приложение выполняется в среде `Development`.</span><span class="sxs-lookup"><span data-stu-id="5407e-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="5407e-129">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5407e-130">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line).</span><span class="sxs-lookup"><span data-stu-id="5407e-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="5407e-131">Поставщики конфигурации, добавленные позже, переопределяют предыдущие параметры ключа.</span><span class="sxs-lookup"><span data-stu-id="5407e-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="5407e-132">Например, если `MyKey` задан в *appsettings.json* и в среде, используется значение среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="5407e-133">При использовании поставщиков конфигурации по умолчанию [поставщик конфигурации командной строки](#command-line-configuration-provider) переопределяет остальных поставщиков.</span><span class="sxs-lookup"><span data-stu-id="5407e-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="5407e-134">Дополнительные сведения о `CreateDefaultBuilder` см. в разделе [Параметры сборщика по умолчанию](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="5407e-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="5407e-135">В следующем коде отображаются включенные поставщики конфигурации в том порядке, в котором они были добавлены:</span><span class="sxs-lookup"><span data-stu-id="5407e-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="5407e-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="5407e-136">appsettings.json</span></span>

<span data-ttu-id="5407e-137">Рассмотрите следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5407e-138">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-139"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> по умолчанию загружает конфигурацию в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="5407e-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="5407e-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5407e-140">*appsettings.json*</span></span>
1. <span data-ttu-id="5407e-141">*appsettings.* `Environment` *.json* : Например, файлы *appsettings*.***Production***.*json* и *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="5407e-142">Версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="5407e-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="5407e-143">Для получения дополнительной информации см. <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5407e-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5407e-144">Значения в *appsettings*.`Environment`.*json* переопределяют ключи в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="5407e-145">Например, по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="5407e-145">For example, by default:</span></span>

* <span data-ttu-id="5407e-146">В среде разработки конфигурация *appsettings*.***Development***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="5407e-147">В рабочей среде конфигурация *appsettings*.***Production***.*json* перезаписывает значения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="5407e-148">Например, при развертывании приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="5407e-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="5407e-149">Привязка иерархических данных конфигурации с помощью шаблона параметров</span><span class="sxs-lookup"><span data-stu-id="5407e-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="5407e-150">С помощью конфигурации [по умолчанию](#default) файлы *appsettings.json* и *appsettings.* `Environment` *.json* включаются с помощью [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="5407e-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="5407e-151">Изменения, внесенные в файл *appsettings.json* и *appsettings.* `Environment` *.json*, ***после*** запуска приложения, считываются [поставщиком конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5407e-152">Сведения о добавлении дополнительных файлов конфигурации JSON см. в разделе [Поставщик конфигурации JSON](#jcp) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="5407e-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="5407e-153">Безопасность и диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="5407e-153">Security and secret manager</span></span>

<span data-ttu-id="5407e-154">Рекомендации по данным конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="5407e-155">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="5407e-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="5407e-156">Для хранения секретов во время разработки можно использовать [диспетчер секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5407e-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="5407e-157">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="5407e-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5407e-158">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="5407e-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5407e-159">[По умолчанию](#default) [диспетчер секретов](xref:security/app-secrets) считывает параметры конфигурации после *appsettings.json* и *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="5407e-160">Дополнительные сведения о хранении паролей или других конфиденциальных данных:</span><span class="sxs-lookup"><span data-stu-id="5407e-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5407e-161"><xref:security/app-secrets>.  Содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5407e-162">Диспетчер секретов использует [поставщик конфигурации файла](#fcp) для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="5407e-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="5407e-163">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5407e-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5407e-164">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5407e-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="5407e-165">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5407e-165">Environment variables</span></span>

<span data-ttu-id="5407e-166">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пар "ключ-значение" в переменных среды после чтения *appsettings.json*, *appsettings.* `Environment` *.json* и [диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5407e-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="5407e-167">Поэтому значения ключей, считанные из среды, переопределяют значения, считанные из *appsettings.json*, *appsettings.* `Environment` *.json* и диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="5407e-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5407e-168">Следующие команды `set`:</span><span class="sxs-lookup"><span data-stu-id="5407e-168">The following `set` commands:</span></span>

* <span data-ttu-id="5407e-169">Задают ключи и значения среды в [предыдущем примере](#appsettingsjson) в Windows.</span><span class="sxs-lookup"><span data-stu-id="5407e-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="5407e-170">Проверяют параметры при использовании [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="5407e-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="5407e-171">Команда `dotnet run` должна выполняться в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="5407e-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="5407e-172">Предыдущие параметры среды:</span><span class="sxs-lookup"><span data-stu-id="5407e-172">The preceding environment settings:</span></span>

* <span data-ttu-id="5407e-173">Задаются только в процессах, запускаемых из командного окна, в котором они были установлены.</span><span class="sxs-lookup"><span data-stu-id="5407e-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="5407e-174">Не будут считываться браузерами, запущенными в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5407e-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="5407e-175">Следующие команды [setx](/windows-server/administration/windows-commands/setx) можно использовать для задания ключей и значений среды в Windows.</span><span class="sxs-lookup"><span data-stu-id="5407e-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="5407e-176">В отличие от `set`, параметры `setx` сохраняются.</span><span class="sxs-lookup"><span data-stu-id="5407e-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="5407e-177">`/M` задает переменную в системной среде.</span><span class="sxs-lookup"><span data-stu-id="5407e-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="5407e-178">Если параметр `/M` не используется, задается переменная среды пользователя.</span><span class="sxs-lookup"><span data-stu-id="5407e-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="5407e-179">Чтобы проверить, что предыдущие команды переопределяют *appsettings.json* и *appsettings.* `Environment` *.json*, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="5407e-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="5407e-180">В Visual Studio: Выйдите из среды Visual Studio и перезапустите ее.</span><span class="sxs-lookup"><span data-stu-id="5407e-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="5407e-181">В CLI: Откройте новое командное окно и введите `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="5407e-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="5407e-182">Вызовите <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> со строкой, чтобы указать префикс для переменных среды:</span><span class="sxs-lookup"><span data-stu-id="5407e-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="5407e-183">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="5407e-183">In the preceding code:</span></span>

* <span data-ttu-id="5407e-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="5407e-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5407e-185">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="5407e-186">Переменные среды, установленные с префиксом `MyCustomPrefix_`, переопределяют [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="5407e-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="5407e-187">Сюда входят переменные среды без префикса.</span><span class="sxs-lookup"><span data-stu-id="5407e-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="5407e-188">Префикс отделяется при создании пары конфигурации "ключ-значение".</span><span class="sxs-lookup"><span data-stu-id="5407e-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="5407e-189">Следующие команды проверяют пользовательский префикс:</span><span class="sxs-lookup"><span data-stu-id="5407e-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="5407e-190">[Конфигурация по умолчанию](#default) загружает переменные среды и аргументы командной строки с префиксом `DOTNET_` и `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="5407e-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="5407e-191">Префиксы `DOTNET_` и `ASPNETCORE_` используются ASP.NET Core для [конфигурации узла и приложения](xref:fundamentals/host/generic-host#host-configuration), но не для конфигурации пользователя.</span><span class="sxs-lookup"><span data-stu-id="5407e-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="5407e-192">Дополнительные сведения о конфигурации узла и приложения см. в разделе [Универсальный узел .NET](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="5407e-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="5407e-193">В [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) выберите **Новый параметр приложения** на странице **Параметры > Конфигурация**.</span><span class="sxs-lookup"><span data-stu-id="5407e-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="5407e-194">Параметры приложения Службы приложений Azure:</span><span class="sxs-lookup"><span data-stu-id="5407e-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="5407e-195">Шифруются, когда они неактивны, и передаются по зашифрованному каналу.</span><span class="sxs-lookup"><span data-stu-id="5407e-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="5407e-196">Предоставляются как переменные среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-196">Exposed as environment variables.</span></span>

<span data-ttu-id="5407e-197">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5407e-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5407e-198">Сведения о строках подключения к базе данных Azure см. в разделе [Префиксы строк подключения](#constr).</span><span class="sxs-lookup"><span data-stu-id="5407e-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="5407e-199">Командная строка</span><span class="sxs-lookup"><span data-stu-id="5407e-199">Command-line</span></span>

<span data-ttu-id="5407e-200">При использовании конфигурации [по умолчанию](#default) <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пар "ключ-значение" аргументов командной строки после следующих источников конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="5407e-201">Файлы *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5407e-202">[Секреты приложения (диспетчер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5407e-203">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-203">Environment variables.</span></span>

<span data-ttu-id="5407e-204">По [умолчанию](#default) значения конфигурации, заданные для значений конфигурации переопределения в командной строке, задаются для всех остальных поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="5407e-205">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="5407e-205">Command-line arguments</span></span>

<span data-ttu-id="5407e-206">Следующая команда задает ключи и значения с помощью `=`:</span><span class="sxs-lookup"><span data-stu-id="5407e-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="5407e-207">Следующая команда задает ключи и значения с помощью `/`:</span><span class="sxs-lookup"><span data-stu-id="5407e-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="5407e-208">Следующая команда задает ключи и значения с помощью `--`:</span><span class="sxs-lookup"><span data-stu-id="5407e-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="5407e-209">Значение ключа:</span><span class="sxs-lookup"><span data-stu-id="5407e-209">The key value:</span></span>

* <span data-ttu-id="5407e-210">Должно соответствовать `=`, или ключ должен иметь префикс `--` или `/`, когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="5407e-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="5407e-211">Является обязательным, если используется параметр `=`.</span><span class="sxs-lookup"><span data-stu-id="5407e-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="5407e-212">Например, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="5407e-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="5407e-213">В рамках одной и той же команды не смешивайте пары "ключ-значение" аргумента командной строки, которые используют `=` с парами "ключ-значение" с пробелом.</span><span class="sxs-lookup"><span data-stu-id="5407e-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="5407e-214">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="5407e-214">Switch mappings</span></span>

<span data-ttu-id="5407e-215">Сопоставление параметров позволяет указать логику замены имен **ключей**.</span><span class="sxs-lookup"><span data-stu-id="5407e-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="5407e-216">Предоставьте словарь замены параметров для метода <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="5407e-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5407e-217">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5407e-218">Если ключ в командной строке находится в словаре, значение словаря передается обратно, чтобы установить пару "ключ-значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5407e-219">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="5407e-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5407e-220">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="5407e-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5407e-221">Параметры должны начинаться с `-` или `--`.</span><span class="sxs-lookup"><span data-stu-id="5407e-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="5407e-222">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5407e-223">Чтобы использовать словарь сопоставлений параметров, передайте его в вызов `AddCommandLine`:</span><span class="sxs-lookup"><span data-stu-id="5407e-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="5407e-224">В следующем коде показаны ключевые значения для замененных ключей:</span><span class="sxs-lookup"><span data-stu-id="5407e-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-225">Выполните следующую команду, чтобы проверить замену ключа:</span><span class="sxs-lookup"><span data-stu-id="5407e-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5407e-226">Примечание. В настоящее время `=` нельзя использовать для задания значений замены ключа с одним тире `-`.</span><span class="sxs-lookup"><span data-stu-id="5407e-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="5407e-227">Также см. [эту проблему в GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="5407e-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="5407e-228">Следующая команда проверяет замену ключа:</span><span class="sxs-lookup"><span data-stu-id="5407e-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="5407e-229">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="5407e-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5407e-230">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные параметры, и нет возможности передать словарь сопоставления параметров в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5407e-231">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="5407e-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5407e-232">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-232">Hierarchical configuration data</span></span>

<span data-ttu-id="5407e-233">API конфигурации считывает иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5407e-234">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="5407e-235">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-236">Предпочтительный способ чтения иерархических данных конфигурации — использование шаблона параметров.</span><span class="sxs-lookup"><span data-stu-id="5407e-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="5407e-237">Дополнительные сведения см. в разделе [Привязка иерархических данных конфигурации](#optpat) в этом документе.</span><span class="sxs-lookup"><span data-stu-id="5407e-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="5407e-238">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5407e-239">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="5407e-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="5407e-240">Ключи и значения конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-240">Configuration keys and values</span></span>

<span data-ttu-id="5407e-241">Ключи конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-241">Configuration keys:</span></span>

* <span data-ttu-id="5407e-242">Не учитывают регистр.</span><span class="sxs-lookup"><span data-stu-id="5407e-242">Are case-insensitive.</span></span> <span data-ttu-id="5407e-243">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5407e-244">Если ключ и значение заданы более чем в одном поставщике конфигурации, используется значение из последнего добавленного поставщика.</span><span class="sxs-lookup"><span data-stu-id="5407e-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="5407e-245">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="5407e-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="5407e-246">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="5407e-246">Hierarchical keys</span></span>
  * <span data-ttu-id="5407e-247">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="5407e-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5407e-248">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="5407e-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5407e-249">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие — `:`.</span><span class="sxs-lookup"><span data-stu-id="5407e-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="5407e-250">В Azure Key Vault иерархические ключи используют `--` в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="5407e-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="5407e-251">[Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) автоматически заменяет `--` на `:` при загрузке секретов в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5407e-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5407e-253">Привязка массива описана в разделе [Привязка массива к классу](#boa).</span><span class="sxs-lookup"><span data-stu-id="5407e-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="5407e-254">Значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-254">Configuration values:</span></span>

* <span data-ttu-id="5407e-255">Представляют собой строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-255">Are strings.</span></span>
* <span data-ttu-id="5407e-256">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="5407e-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="5407e-257">Поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-257">Configuration providers</span></span>

<span data-ttu-id="5407e-258">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5407e-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5407e-259">Поставщик</span><span class="sxs-lookup"><span data-stu-id="5407e-259">Provider</span></span> | <span data-ttu-id="5407e-260">Предоставляет конфигурацию из</span><span class="sxs-lookup"><span data-stu-id="5407e-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="5407e-261">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-262">'Blazor'</span></span>
- <span data-ttu-id="5407e-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-263">'Identity'</span></span>
- <span data-ttu-id="5407e-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-265">'Razor'</span></span>
- <span data-ttu-id="5407e-266">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-267">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-268">'Blazor'</span></span>
- <span data-ttu-id="5407e-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-269">'Identity'</span></span>
- <span data-ttu-id="5407e-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-271">'Razor'</span></span>
- <span data-ttu-id="5407e-272">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-272">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-273">---- | --- название: автор: описание:  monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-274">'Blazor'</span></span>
- <span data-ttu-id="5407e-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-275">'Identity'</span></span>
- <span data-ttu-id="5407e-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-277">'Razor'</span></span>
- <span data-ttu-id="5407e-278">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-279">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-280">'Blazor'</span></span>
- <span data-ttu-id="5407e-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-281">'Identity'</span></span>
- <span data-ttu-id="5407e-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-283">'Razor'</span></span>
- <span data-ttu-id="5407e-284">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-285">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-286">'Blazor'</span></span>
- <span data-ttu-id="5407e-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-287">'Identity'</span></span>
- <span data-ttu-id="5407e-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-289">'Razor'</span></span>
- <span data-ttu-id="5407e-290">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-291">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-292">'Blazor'</span></span>
- <span data-ttu-id="5407e-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-293">'Identity'</span></span>
- <span data-ttu-id="5407e-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-295">'Razor'</span></span>
- <span data-ttu-id="5407e-296">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-297">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-298">'Blazor'</span></span>
- <span data-ttu-id="5407e-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-299">'Identity'</span></span>
- <span data-ttu-id="5407e-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-301">'Razor'</span></span>
- <span data-ttu-id="5407e-302">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-303">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-304">'Blazor'</span></span>
- <span data-ttu-id="5407e-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-305">'Identity'</span></span>
- <span data-ttu-id="5407e-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-307">'Razor'</span></span>
- <span data-ttu-id="5407e-308">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-309">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-310">'Blazor'</span></span>
- <span data-ttu-id="5407e-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-311">'Identity'</span></span>
- <span data-ttu-id="5407e-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-313">'Razor'</span></span>
- <span data-ttu-id="5407e-314">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-315">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-316">'Blazor'</span></span>
- <span data-ttu-id="5407e-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-317">'Identity'</span></span>
- <span data-ttu-id="5407e-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-319">'Razor'</span></span>
- <span data-ttu-id="5407e-320">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-321">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-322">'Blazor'</span></span>
- <span data-ttu-id="5407e-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-323">'Identity'</span></span>
- <span data-ttu-id="5407e-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-325">'Razor'</span></span>
- <span data-ttu-id="5407e-326">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-327">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-328">'Blazor'</span></span>
- <span data-ttu-id="5407e-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-329">'Identity'</span></span>
- <span data-ttu-id="5407e-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-331">'Razor'</span></span>
- <span data-ttu-id="5407e-332">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-333">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-334">'Blazor'</span></span>
- <span data-ttu-id="5407e-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-335">'Identity'</span></span>
- <span data-ttu-id="5407e-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-337">'Razor'</span></span>
- <span data-ttu-id="5407e-338">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-339">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-340">'Blazor'</span></span>
- <span data-ttu-id="5407e-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-341">'Identity'</span></span>
- <span data-ttu-id="5407e-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-343">'Razor'</span></span>
- <span data-ttu-id="5407e-344">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-345">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-346">'Blazor'</span></span>
- <span data-ttu-id="5407e-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-347">'Identity'</span></span>
- <span data-ttu-id="5407e-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-349">'Razor'</span></span>
- <span data-ttu-id="5407e-350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-351">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-352">'Blazor'</span></span>
- <span data-ttu-id="5407e-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-353">'Identity'</span></span>
- <span data-ttu-id="5407e-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-355">'Razor'</span></span>
- <span data-ttu-id="5407e-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-357">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-358">'Blazor'</span></span>
- <span data-ttu-id="5407e-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-359">'Identity'</span></span>
- <span data-ttu-id="5407e-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-361">'Razor'</span></span>
- <span data-ttu-id="5407e-362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-362">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-363">------------------ | | [Поставщик конфигурации Azure Key Vault ](xref:security/key-vault-configuration) | Azure Key Vault | | [ Поставщик Конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Конфигурация приложений Azure | | [ Поставщик конфигурации командной строки](#clcp) | Параметры командной строки | | [Поставщик настраиваемой конфигурации](#custom-configuration-provider) | Настраиваемый источник | | [Поставщик конфигурации переменных среды](#evcp) | Переменные среды | | [Поставщик конфигурации файлов](#file-configuration-provider) | Файлы INI, JSON и XML | | [Поставщик конфигурации ключа для каждого файла](#key-per-file-configuration-provider) | Файлы каталога | | [Поставщик конфигурации памяти](#memory-configuration-provider) | Коллекции в памяти | | [Диспетчер секретов](xref:security/app-secrets) | Файл в каталоге профиля пользователя |</span><span class="sxs-lookup"><span data-stu-id="5407e-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="5407e-364">Источники конфигурации считываются в том порядке, в котором указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="5407e-365">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="5407e-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5407e-366">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5407e-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="5407e-367">*appsettings.json*</span></span>
1. <span data-ttu-id="5407e-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="5407e-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="5407e-369">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="5407e-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="5407e-370">Переменные среды, использующие [поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="5407e-371">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="5407e-372">Общепринятой практикой является добавление поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="5407e-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5407e-373">Предыдущая последовательность поставщиков используется в [конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="5407e-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="5407e-374">Префиксы строк подключения</span><span class="sxs-lookup"><span data-stu-id="5407e-374">Connection string prefixes</span></span>

<span data-ttu-id="5407e-375">API конфигурации имеет особые правила обработки для четырех переменных среды строки подключения.</span><span class="sxs-lookup"><span data-stu-id="5407e-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="5407e-376">Эти строки подключения участвуют в настройке строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5407e-377">Переменные среды с префиксами, указанными в таблице, загружаются в приложение с [конфигурацией по умолчанию](#default) или если префикс не предоставлен для `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="5407e-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5407e-378">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="5407e-378">Connection string prefix</span></span> | <span data-ttu-id="5407e-379">Поставщик</span><span class="sxs-lookup"><span data-stu-id="5407e-379">Provider</span></span> |
| ---
<span data-ttu-id="5407e-380">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-381">'Blazor'</span></span>
- <span data-ttu-id="5407e-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-382">'Identity'</span></span>
- <span data-ttu-id="5407e-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-384">'Razor'</span></span>
- <span data-ttu-id="5407e-385">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-386">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-387">'Blazor'</span></span>
- <span data-ttu-id="5407e-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-388">'Identity'</span></span>
- <span data-ttu-id="5407e-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-390">'Razor'</span></span>
- <span data-ttu-id="5407e-391">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-392">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-393">'Blazor'</span></span>
- <span data-ttu-id="5407e-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-394">'Identity'</span></span>
- <span data-ttu-id="5407e-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-396">'Razor'</span></span>
- <span data-ttu-id="5407e-397">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-398">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-399">'Blazor'</span></span>
- <span data-ttu-id="5407e-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-400">'Identity'</span></span>
- <span data-ttu-id="5407e-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-402">'Razor'</span></span>
- <span data-ttu-id="5407e-403">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-404">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-405">'Blazor'</span></span>
- <span data-ttu-id="5407e-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-406">'Identity'</span></span>
- <span data-ttu-id="5407e-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-408">'Razor'</span></span>
- <span data-ttu-id="5407e-409">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-410">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-411">'Blazor'</span></span>
- <span data-ttu-id="5407e-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-412">'Identity'</span></span>
- <span data-ttu-id="5407e-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-414">'Razor'</span></span>
- <span data-ttu-id="5407e-415">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-416">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-417">'Blazor'</span></span>
- <span data-ttu-id="5407e-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-418">'Identity'</span></span>
- <span data-ttu-id="5407e-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-420">'Razor'</span></span>
- <span data-ttu-id="5407e-421">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-422">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-423">'Blazor'</span></span>
- <span data-ttu-id="5407e-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-424">'Identity'</span></span>
- <span data-ttu-id="5407e-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-426">'Razor'</span></span>
- <span data-ttu-id="5407e-427">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-428">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-429">'Blazor'</span></span>
- <span data-ttu-id="5407e-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-430">'Identity'</span></span>
- <span data-ttu-id="5407e-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-432">'Razor'</span></span>
- <span data-ttu-id="5407e-433">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-434">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-435">'Blazor'</span></span>
- <span data-ttu-id="5407e-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-436">'Identity'</span></span>
- <span data-ttu-id="5407e-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-438">'Razor'</span></span>
- <span data-ttu-id="5407e-439">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-439">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-440">------------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-441">'Blazor'</span></span>
- <span data-ttu-id="5407e-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-442">'Identity'</span></span>
- <span data-ttu-id="5407e-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-444">'Razor'</span></span>
- <span data-ttu-id="5407e-445">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-446">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-447">'Blazor'</span></span>
- <span data-ttu-id="5407e-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-448">'Identity'</span></span>
- <span data-ttu-id="5407e-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-450">'Razor'</span></span>
- <span data-ttu-id="5407e-451">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-451">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-452">---- | | `CUSTOMCONNSTR_` | Настраиваемый поставщик | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="5407e-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="5407e-453">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5407e-454">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="5407e-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5407e-455">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="5407e-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5407e-456">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="5407e-456">Environment variable key</span></span> | <span data-ttu-id="5407e-457">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-457">Converted configuration key</span></span> | <span data-ttu-id="5407e-458">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="5407e-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="5407e-459">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-460">'Blazor'</span></span>
- <span data-ttu-id="5407e-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-461">'Identity'</span></span>
- <span data-ttu-id="5407e-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-463">'Razor'</span></span>
- <span data-ttu-id="5407e-464">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-465">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-466">'Blazor'</span></span>
- <span data-ttu-id="5407e-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-467">'Identity'</span></span>
- <span data-ttu-id="5407e-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-469">'Razor'</span></span>
- <span data-ttu-id="5407e-470">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-471">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-472">'Blazor'</span></span>
- <span data-ttu-id="5407e-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-473">'Identity'</span></span>
- <span data-ttu-id="5407e-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-475">'Razor'</span></span>
- <span data-ttu-id="5407e-476">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-477">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-478">'Blazor'</span></span>
- <span data-ttu-id="5407e-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-479">'Identity'</span></span>
- <span data-ttu-id="5407e-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-481">'Razor'</span></span>
- <span data-ttu-id="5407e-482">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-483">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-484">'Blazor'</span></span>
- <span data-ttu-id="5407e-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-485">'Identity'</span></span>
- <span data-ttu-id="5407e-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-487">'Razor'</span></span>
- <span data-ttu-id="5407e-488">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-489">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-490">'Blazor'</span></span>
- <span data-ttu-id="5407e-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-491">'Identity'</span></span>
- <span data-ttu-id="5407e-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-493">'Razor'</span></span>
- <span data-ttu-id="5407e-494">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-495">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-496">'Blazor'</span></span>
- <span data-ttu-id="5407e-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-497">'Identity'</span></span>
- <span data-ttu-id="5407e-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-499">'Razor'</span></span>
- <span data-ttu-id="5407e-500">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-501">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-502">'Blazor'</span></span>
- <span data-ttu-id="5407e-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-503">'Identity'</span></span>
- <span data-ttu-id="5407e-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-505">'Razor'</span></span>
- <span data-ttu-id="5407e-506">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-507">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-508">'Blazor'</span></span>
- <span data-ttu-id="5407e-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-509">'Identity'</span></span>
- <span data-ttu-id="5407e-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-511">'Razor'</span></span>
- <span data-ttu-id="5407e-512">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-513">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-514">'Blazor'</span></span>
- <span data-ttu-id="5407e-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-515">'Identity'</span></span>
- <span data-ttu-id="5407e-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-517">'Razor'</span></span>
- <span data-ttu-id="5407e-518">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-518">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-519">------------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-520">'Blazor'</span></span>
- <span data-ttu-id="5407e-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-521">'Identity'</span></span>
- <span data-ttu-id="5407e-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-523">'Razor'</span></span>
- <span data-ttu-id="5407e-524">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-525">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-526">'Blazor'</span></span>
- <span data-ttu-id="5407e-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-527">'Identity'</span></span>
- <span data-ttu-id="5407e-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-529">'Razor'</span></span>
- <span data-ttu-id="5407e-530">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-531">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-532">'Blazor'</span></span>
- <span data-ttu-id="5407e-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-533">'Identity'</span></span>
- <span data-ttu-id="5407e-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-535">'Razor'</span></span>
- <span data-ttu-id="5407e-536">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-537">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-538">'Blazor'</span></span>
- <span data-ttu-id="5407e-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-539">'Identity'</span></span>
- <span data-ttu-id="5407e-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-541">'Razor'</span></span>
- <span data-ttu-id="5407e-542">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-543">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-544">'Blazor'</span></span>
- <span data-ttu-id="5407e-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-545">'Identity'</span></span>
- <span data-ttu-id="5407e-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-547">'Razor'</span></span>
- <span data-ttu-id="5407e-548">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-549">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-550">'Blazor'</span></span>
- <span data-ttu-id="5407e-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-551">'Identity'</span></span>
- <span data-ttu-id="5407e-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-553">'Razor'</span></span>
- <span data-ttu-id="5407e-554">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-555">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-556">'Blazor'</span></span>
- <span data-ttu-id="5407e-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-557">'Identity'</span></span>
- <span data-ttu-id="5407e-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-559">'Razor'</span></span>
- <span data-ttu-id="5407e-560">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-561">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-562">'Blazor'</span></span>
- <span data-ttu-id="5407e-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-563">'Identity'</span></span>
- <span data-ttu-id="5407e-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-565">'Razor'</span></span>
- <span data-ttu-id="5407e-566">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-567">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-568">'Blazor'</span></span>
- <span data-ttu-id="5407e-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-569">'Identity'</span></span>
- <span data-ttu-id="5407e-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-571">'Razor'</span></span>
- <span data-ttu-id="5407e-572">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-573">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-574">'Blazor'</span></span>
- <span data-ttu-id="5407e-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-575">'Identity'</span></span>
- <span data-ttu-id="5407e-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-577">'Razor'</span></span>
- <span data-ttu-id="5407e-578">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-579">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-580">'Blazor'</span></span>
- <span data-ttu-id="5407e-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-581">'Identity'</span></span>
- <span data-ttu-id="5407e-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-583">'Razor'</span></span>
- <span data-ttu-id="5407e-584">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-584">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-585">-------------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-586">'Blazor'</span></span>
- <span data-ttu-id="5407e-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-587">'Identity'</span></span>
- <span data-ttu-id="5407e-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-589">'Razor'</span></span>
- <span data-ttu-id="5407e-590">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-591">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-592">'Blazor'</span></span>
- <span data-ttu-id="5407e-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-593">'Identity'</span></span>
- <span data-ttu-id="5407e-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-595">'Razor'</span></span>
- <span data-ttu-id="5407e-596">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-597">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-598">'Blazor'</span></span>
- <span data-ttu-id="5407e-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-599">'Identity'</span></span>
- <span data-ttu-id="5407e-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-601">'Razor'</span></span>
- <span data-ttu-id="5407e-602">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-603">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-604">'Blazor'</span></span>
- <span data-ttu-id="5407e-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-605">'Identity'</span></span>
- <span data-ttu-id="5407e-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-607">'Razor'</span></span>
- <span data-ttu-id="5407e-608">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-609">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-610">'Blazor'</span></span>
- <span data-ttu-id="5407e-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-611">'Identity'</span></span>
- <span data-ttu-id="5407e-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-613">'Razor'</span></span>
- <span data-ttu-id="5407e-614">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-615">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-616">'Blazor'</span></span>
- <span data-ttu-id="5407e-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-617">'Identity'</span></span>
- <span data-ttu-id="5407e-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-619">'Razor'</span></span>
- <span data-ttu-id="5407e-620">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-621">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-622">'Blazor'</span></span>
- <span data-ttu-id="5407e-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-623">'Identity'</span></span>
- <span data-ttu-id="5407e-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-625">'Razor'</span></span>
- <span data-ttu-id="5407e-626">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-627">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-628">'Blazor'</span></span>
- <span data-ttu-id="5407e-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-629">'Identity'</span></span>
- <span data-ttu-id="5407e-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-631">'Razor'</span></span>
- <span data-ttu-id="5407e-632">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-633">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-634">'Blazor'</span></span>
- <span data-ttu-id="5407e-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-635">'Identity'</span></span>
- <span data-ttu-id="5407e-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-637">'Razor'</span></span>
- <span data-ttu-id="5407e-638">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-639">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-640">'Blazor'</span></span>
- <span data-ttu-id="5407e-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-641">'Identity'</span></span>
- <span data-ttu-id="5407e-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-643">'Razor'</span></span>
- <span data-ttu-id="5407e-644">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-645">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-646">'Blazor'</span></span>
- <span data-ttu-id="5407e-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-647">'Identity'</span></span>
- <span data-ttu-id="5407e-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-649">'Razor'</span></span>
- <span data-ttu-id="5407e-650">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-651">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-652">'Blazor'</span></span>
- <span data-ttu-id="5407e-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-653">'Identity'</span></span>
- <span data-ttu-id="5407e-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-655">'Razor'</span></span>
- <span data-ttu-id="5407e-656">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-657">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-658">'Blazor'</span></span>
- <span data-ttu-id="5407e-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-659">'Identity'</span></span>
- <span data-ttu-id="5407e-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-661">'Razor'</span></span>
- <span data-ttu-id="5407e-662">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-663">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-664">'Blazor'</span></span>
- <span data-ttu-id="5407e-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-665">'Identity'</span></span>
- <span data-ttu-id="5407e-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-667">'Razor'</span></span>
- <span data-ttu-id="5407e-668">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-669">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-670">'Blazor'</span></span>
- <span data-ttu-id="5407e-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-671">'Identity'</span></span>
- <span data-ttu-id="5407e-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-673">'Razor'</span></span>
- <span data-ttu-id="5407e-674">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-675">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-676">'Blazor'</span></span>
- <span data-ttu-id="5407e-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-677">'Identity'</span></span>
- <span data-ttu-id="5407e-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-679">'Razor'</span></span>
- <span data-ttu-id="5407e-680">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-681">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-682">'Blazor'</span></span>
- <span data-ttu-id="5407e-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-683">'Identity'</span></span>
- <span data-ttu-id="5407e-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-685">'Razor'</span></span>
- <span data-ttu-id="5407e-686">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-687">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-688">'Blazor'</span></span>
- <span data-ttu-id="5407e-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-689">'Identity'</span></span>
- <span data-ttu-id="5407e-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-691">'Razor'</span></span>
- <span data-ttu-id="5407e-692">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-693">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-694">'Blazor'</span></span>
- <span data-ttu-id="5407e-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-695">'Identity'</span></span>
- <span data-ttu-id="5407e-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-697">'Razor'</span></span>
- <span data-ttu-id="5407e-698">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-699">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-700">'Blazor'</span></span>
- <span data-ttu-id="5407e-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-701">'Identity'</span></span>
- <span data-ttu-id="5407e-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-703">'Razor'</span></span>
- <span data-ttu-id="5407e-704">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-705">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-706">'Blazor'</span></span>
- <span data-ttu-id="5407e-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-707">'Identity'</span></span>
- <span data-ttu-id="5407e-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-709">'Razor'</span></span>
- <span data-ttu-id="5407e-710">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-711">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-712">'Blazor'</span></span>
- <span data-ttu-id="5407e-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-713">'Identity'</span></span>
- <span data-ttu-id="5407e-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-715">'Razor'</span></span>
- <span data-ttu-id="5407e-716">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-717">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-718">'Blazor'</span></span>
- <span data-ttu-id="5407e-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-719">'Identity'</span></span>
- <span data-ttu-id="5407e-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-721">'Razor'</span></span>
- <span data-ttu-id="5407e-722">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-723">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-724">'Blazor'</span></span>
- <span data-ttu-id="5407e-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-725">'Identity'</span></span>
- <span data-ttu-id="5407e-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-727">'Razor'</span></span>
- <span data-ttu-id="5407e-728">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-729">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-730">'Blazor'</span></span>
- <span data-ttu-id="5407e-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-731">'Identity'</span></span>
- <span data-ttu-id="5407e-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-733">'Razor'</span></span>
- <span data-ttu-id="5407e-734">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-735">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-736">'Blazor'</span></span>
- <span data-ttu-id="5407e-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-737">'Identity'</span></span>
- <span data-ttu-id="5407e-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-739">'Razor'</span></span>
- <span data-ttu-id="5407e-740">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-741">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-742">'Blazor'</span></span>
- <span data-ttu-id="5407e-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-743">'Identity'</span></span>
- <span data-ttu-id="5407e-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-745">'Razor'</span></span>
- <span data-ttu-id="5407e-746">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-747">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-748">'Blazor'</span></span>
- <span data-ttu-id="5407e-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-749">'Identity'</span></span>
- <span data-ttu-id="5407e-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-751">'Razor'</span></span>
- <span data-ttu-id="5407e-752">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-753">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-754">'Blazor'</span></span>
- <span data-ttu-id="5407e-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-755">'Identity'</span></span>
- <span data-ttu-id="5407e-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-757">'Razor'</span></span>
- <span data-ttu-id="5407e-758">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-759">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-760">'Blazor'</span></span>
- <span data-ttu-id="5407e-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-761">'Identity'</span></span>
- <span data-ttu-id="5407e-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-763">'Razor'</span></span>
- <span data-ttu-id="5407e-764">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-765">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-766">'Blazor'</span></span>
- <span data-ttu-id="5407e-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-767">'Identity'</span></span>
- <span data-ttu-id="5407e-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-769">'Razor'</span></span>
- <span data-ttu-id="5407e-770">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-771">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-772">'Blazor'</span></span>
- <span data-ttu-id="5407e-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-773">'Identity'</span></span>
- <span data-ttu-id="5407e-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-775">'Razor'</span></span>
- <span data-ttu-id="5407e-776">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-777">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-778">'Blazor'</span></span>
- <span data-ttu-id="5407e-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-779">'Identity'</span></span>
- <span data-ttu-id="5407e-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-781">'Razor'</span></span>
- <span data-ttu-id="5407e-782">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-783">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-784">'Blazor'</span></span>
- <span data-ttu-id="5407e-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-785">'Identity'</span></span>
- <span data-ttu-id="5407e-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-787">'Razor'</span></span>
- <span data-ttu-id="5407e-788">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-789">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-790">'Blazor'</span></span>
- <span data-ttu-id="5407e-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-791">'Identity'</span></span>
- <span data-ttu-id="5407e-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-793">'Razor'</span></span>
- <span data-ttu-id="5407e-794">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-795">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-796">'Blazor'</span></span>
- <span data-ttu-id="5407e-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-797">'Identity'</span></span>
- <span data-ttu-id="5407e-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-799">'Razor'</span></span>
- <span data-ttu-id="5407e-800">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-801">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-802">'Blazor'</span></span>
- <span data-ttu-id="5407e-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-803">'Identity'</span></span>
- <span data-ttu-id="5407e-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-805">'Razor'</span></span>
- <span data-ttu-id="5407e-806">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-806">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="5407e-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="5407e-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-809">Значение: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`| Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-810">Значение: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-811">Значение: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="5407e-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="5407e-812">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="5407e-812">JSON configuration provider</span></span>

<span data-ttu-id="5407e-813"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ-значение" JSON-файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="5407e-814">Перегрузки могут указывать:</span><span class="sxs-lookup"><span data-stu-id="5407e-814">Overloads can specify:</span></span>

* <span data-ttu-id="5407e-815">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="5407e-815">Whether the file is optional.</span></span>
* <span data-ttu-id="5407e-816">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="5407e-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="5407e-817">Рассмотрим следующий код.</span><span class="sxs-lookup"><span data-stu-id="5407e-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="5407e-818">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="5407e-818">The preceding code:</span></span>

* <span data-ttu-id="5407e-819">Настраивает поставщик конфигурации JSON для загрузки файла *MyConfig.json* со следующими параметрами:</span><span class="sxs-lookup"><span data-stu-id="5407e-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="5407e-820">`optional: true`. Файл является необязательным.</span><span class="sxs-lookup"><span data-stu-id="5407e-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="5407e-821">`reloadOnChange: true`: При сохранении изменений файл перезагружается.</span><span class="sxs-lookup"><span data-stu-id="5407e-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="5407e-822">Считывает [поставщиков конфигурации по умолчанию](#default) до файла *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="5407e-823">Параметры в файле *MyConfig.json* переопределяют параметр в поставщиках конфигурации по умолчанию, включая [поставщик конфигурация переменных среды](#evcp) и [поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5407e-824">Обычно ***не*** требуется, чтобы пользовательские файлы JSON переопределяли значения, заданные в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5407e-825">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="5407e-826">В приведенном выше коде параметры в файлах *MyConfig.json* и *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="5407e-827">Переопределяют параметры в файлах *appsettings.json* и *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="5407e-828">Переопределяются параметрами в [поставщике конфигурации переменных среды](#evcp) и [поставщике конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5407e-829">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="5407e-830">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="5407e-831">Поставщик конфигурации файла</span><span class="sxs-lookup"><span data-stu-id="5407e-831">File configuration provider</span></span>

<span data-ttu-id="5407e-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="5407e-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5407e-833">Следующие поставщики конфигурации являются производными от `FileConfigurationProvider`:</span><span class="sxs-lookup"><span data-stu-id="5407e-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="5407e-834">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="5407e-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5407e-835">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="5407e-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="5407e-836">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="5407e-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5407e-837">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="5407e-837">INI configuration provider</span></span>

<span data-ttu-id="5407e-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-839">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="5407e-840">В приведенном выше коде параметры в *MyIniConfig.ini* и *MyIniConfig*.`Environment`.*ini* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="5407e-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* <span data-ttu-id="5407e-841">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-841">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="5407e-842">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5407e-843">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="5407e-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="5407e-844">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="5407e-845">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="5407e-845">XML configuration provider</span></span>

<span data-ttu-id="5407e-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-847">Следующий код очищает все поставщики конфигурации и добавляет несколько поставщиков конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="5407e-848">В приведенном выше коде параметры в *MyXMLFile.xml* и *MyXMLFile*.`Environment`.*xml* переопределяются параметрами в следующих поставщиках:</span><span class="sxs-lookup"><span data-stu-id="5407e-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* <span data-ttu-id="5407e-849">[Поставщик конфигурации переменных среды](#evcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-849">[Environment variables configuration provider](#evcp)</span></span>
* <span data-ttu-id="5407e-850">[Поставщик конфигурации командной строки](#clcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="5407e-851">[Пример загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) содержит следующий файл *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="5407e-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="5407e-852">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются некоторые из перечисленных выше параметров конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-853">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="5407e-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="5407e-854">Следующий код считывает предыдущий файл конфигурации и отображает ключи и значения:</span><span class="sxs-lookup"><span data-stu-id="5407e-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-855">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="5407e-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5407e-856">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="5407e-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5407e-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5407e-857">key:attribute</span></span>
* <span data-ttu-id="5407e-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5407e-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5407e-859">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="5407e-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="5407e-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5407e-861">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-861">The key is the file name.</span></span> <span data-ttu-id="5407e-862">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-862">The value contains the file's contents.</span></span> <span data-ttu-id="5407e-863">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="5407e-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5407e-864">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5407e-865">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="5407e-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5407e-866">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="5407e-867">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="5407e-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5407e-868">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="5407e-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5407e-869">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="5407e-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5407e-870">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5407e-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5407e-871">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="5407e-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="5407e-872">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="5407e-872">Memory configuration provider</span></span>

<span data-ttu-id="5407e-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5407e-874">Следующий код добавляет коллекцию памяти в систему конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="5407e-875">В следующем коде из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) отображаются перечисленные выше параметры конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-876">В предыдущем коде `config.AddInMemoryCollection(Dict)` добавляется после [поставщиков конфигурации по умолчанию](#default).</span><span class="sxs-lookup"><span data-stu-id="5407e-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="5407e-877">Пример упорядочивания поставщиков конфигурации см. в разделе [Поставщик конфигурации JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="5407e-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="5407e-878">В разделе [Привязка массива](#boa) вы найдете еще один пример использования `MemoryConfigurationProvider`.</span><span class="sxs-lookup"><span data-stu-id="5407e-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="5407e-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="5407e-879">GetValue</span></span>

<span data-ttu-id="5407e-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип:</span><span class="sxs-lookup"><span data-stu-id="5407e-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-881">В приведенном выше коде, если `NumberKey` отсутствует в конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="5407e-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5407e-882">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="5407e-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5407e-883">В следующих примерах мы рассмотрим следующий файл *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="5407e-884">Следующий код добавляет *MySubsection.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="5407e-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="5407e-885">GetSection</span></span>

<span data-ttu-id="5407e-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) возвращает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="5407e-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5407e-887">Следующий код возвращает значения для `section1`:</span><span class="sxs-lookup"><span data-stu-id="5407e-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-888">Следующий код возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="5407e-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-889">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="5407e-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5407e-890">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="5407e-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5407e-891">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="5407e-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5407e-892"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="5407e-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="5407e-893">GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="5407e-893">GetChildren and Exists</span></span>

<span data-ttu-id="5407e-894">Следующий код вызывает [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) и возвращает значения для `section2:subsection0`:</span><span class="sxs-lookup"><span data-stu-id="5407e-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-895">Приведенный выше код вызывает [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) для проверки существования раздела:</span><span class="sxs-lookup"><span data-stu-id="5407e-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="5407e-896">Привязка массива</span><span class="sxs-lookup"><span data-stu-id="5407e-896">Bind an array</span></span>

<span data-ttu-id="5407e-897">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) поддерживает привязку массивов к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5407e-898">Любой формат массива, который предоставляет сегмент числового ключа способен привязать массив к массиву класса [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object).</span><span class="sxs-lookup"><span data-stu-id="5407e-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="5407e-899">Рассмотрим *MyArray.json* из [примера загрузки](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="5407e-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="5407e-900">Следующий код добавляет *MyArray.json* к поставщикам конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="5407e-901">Следующий код считывает конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="5407e-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-902">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="5407e-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="5407e-903">В предыдущих выходных данных индекс 3 имеет значение `value40`, соответствующее `"4": "value40",` в *MyArray.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="5407e-904">Индексы привязанного массива непрерывны и не привязаны к индексу ключа конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="5407e-905">Модуль привязки конфигурации не поддерживает привязку значений NULL или создание записей NULL в связанных объектах</span><span class="sxs-lookup"><span data-stu-id="5407e-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="5407e-906">Следующий код загружает конфигурацию `array:entries` с помощью метода расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>:</span><span class="sxs-lookup"><span data-stu-id="5407e-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="5407e-907">Следующий код считывает конфигурацию в `arrayDict` `Dictionary` и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="5407e-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-908">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="5407e-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="5407e-909">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="5407e-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5407e-910">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="5407e-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5407e-911">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="5407e-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5407e-912">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который считывает пару "ключ-значение" индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="5407e-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="5407e-913">Рассмотрим следующий файл *Value3.json* из примера загрузки:</span><span class="sxs-lookup"><span data-stu-id="5407e-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="5407e-914">Следующий код включает конфигурацию для *Value3.json* и `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="5407e-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="5407e-915">Следующий код считывает предыдущую конфигурацию и отображает значения:</span><span class="sxs-lookup"><span data-stu-id="5407e-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-916">Предыдущий код возвращает следующие выходные данные:</span><span class="sxs-lookup"><span data-stu-id="5407e-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="5407e-917">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="5407e-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="5407e-918">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-918">Custom configuration provider</span></span>

<span data-ttu-id="5407e-919">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="5407e-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5407e-920">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="5407e-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5407e-921">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="5407e-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5407e-922">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5407e-923">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="5407e-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5407e-924">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="5407e-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5407e-925">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5407e-926">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5407e-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5407e-928">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="5407e-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5407e-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5407e-930">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5407e-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5407e-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5407e-932">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5407e-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5407e-933">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="5407e-934">Поскольку [конфигурационные ключи не учитывают регистр](#keys), словарь, используемый для инициализации базы данных, создается с помощью функции сравнения без учета регистра ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="5407e-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="5407e-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5407e-936">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5407e-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5407e-938">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5407e-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="5407e-939">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="5407e-939">Access configuration in Startup</span></span>

<span data-ttu-id="5407e-940">В следующем коде отображаются данные конфигурации в методах `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5407e-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="5407e-941">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5407e-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="5407e-942">Конфигурация доступа в RazorPages</span><span class="sxs-lookup"><span data-stu-id="5407e-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="5407e-943">В следующем коде отображаются данные конфигурации в RazorPage:</span><span class="sxs-lookup"><span data-stu-id="5407e-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="5407e-944">В следующем коде `MyOptions` добавляется в контейнер службы с помощью интерфейса <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> и привязывается к конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="5407e-945">В следующей разметке для разрешения и вывода значений параметров используется директива Razor [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="5407e-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="5407e-946">Доступ к конфигурации в файле представления MVC</span><span class="sxs-lookup"><span data-stu-id="5407e-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="5407e-947">В следующем коде отображаются данные конфигурации в представлении MVC:</span><span class="sxs-lookup"><span data-stu-id="5407e-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="5407e-948">Настройка параметров с помощью делегата</span><span class="sxs-lookup"><span data-stu-id="5407e-948">Configure options with a delegate</span></span>

<span data-ttu-id="5407e-949">Параметры, настроенные в делегате, переопределяют значения, заданные в поставщиках конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="5407e-950">Настройка параметров с помощью делегата демонстрируется в примере 2 в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="5407e-951">В приведенном ниже коде в контейнер службы добавляется служба <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="5407e-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="5407e-952">Она использует делегат для настройки значений для `MyOptions`:</span><span class="sxs-lookup"><span data-stu-id="5407e-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="5407e-953">Следующий код отображает значения параметров:</span><span class="sxs-lookup"><span data-stu-id="5407e-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="5407e-954">В предыдущем примере значения `Option1` и `Option2` задаются в файле *appsettings.json*, а затем переопределяются настроенным делегатом.</span><span class="sxs-lookup"><span data-stu-id="5407e-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5407e-955">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="5407e-955">Host versus app configuration</span></span>

<span data-ttu-id="5407e-956">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="5407e-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5407e-957">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="5407e-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5407e-958">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5407e-959">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5407e-960">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5407e-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="5407e-961">Конфигурация узла по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5407e-961">Default host configuration</span></span>

<span data-ttu-id="5407e-962">Подробные сведения о конфигурации по умолчанию при использовании [веб-узла](xref:fundamentals/host/web-host) см. в [разделе о версии ASP.NET Core 2.2 в этой статье](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5407e-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="5407e-963">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="5407e-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5407e-964">Переменные среды с префиксом `DOTNET_` (например, `DOTNET_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5407e-965">Префикс (`DOTNET_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5407e-966">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5407e-967">Устанавливается конфигурация веб-узла по умолчанию (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="5407e-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="5407e-968">Kestrel используется в качестве веб-сервера и настраивается с помощью поставщиков конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="5407e-969">Добавьте ПО промежуточного слоя фильтрации узлов.</span><span class="sxs-lookup"><span data-stu-id="5407e-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="5407e-970">Если переменной среды `ASPNETCORE_FORWARDEDHEADERS_ENABLED` присвоено значение `true`, добавьте ПО промежуточного слоя перенаправления заголовков.</span><span class="sxs-lookup"><span data-stu-id="5407e-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="5407e-971">Включите интеграцию служб IIS.</span><span class="sxs-lookup"><span data-stu-id="5407e-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5407e-972">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="5407e-972">Other configuration</span></span>

<span data-ttu-id="5407e-973">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="5407e-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5407e-974">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5407e-975">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="5407e-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5407e-976">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5407e-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5407e-977">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5407e-978">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="5407e-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5407e-979">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="5407e-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5407e-980">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="5407e-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="5407e-981">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="5407e-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5407e-982">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5407e-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5407e-983">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5407e-983">Additional resources</span></span>

* [<span data-ttu-id="5407e-984">Исходный код конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5407e-985">Конфигурация приложения в ASP.NET Core основана на парах "ключ — значение", установленных *поставщиками конфигурации*.</span><span class="sxs-lookup"><span data-stu-id="5407e-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5407e-986">Поставщики конфигурации получают данные конфигурации в парах "ключ — значение" из различных источников:</span><span class="sxs-lookup"><span data-stu-id="5407e-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5407e-987">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="5407e-987">Azure Key Vault</span></span>
* <span data-ttu-id="5407e-988">конфигурация приложения Azure;</span><span class="sxs-lookup"><span data-stu-id="5407e-988">Azure App Configuration</span></span>
* <span data-ttu-id="5407e-989">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="5407e-989">Command-line arguments</span></span>
* <span data-ttu-id="5407e-990">а также пользовательские поставщики (устанавливаемые или создаваемые).</span><span class="sxs-lookup"><span data-stu-id="5407e-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5407e-991">справочных файлов;</span><span class="sxs-lookup"><span data-stu-id="5407e-991">Directory files</span></span>
* <span data-ttu-id="5407e-992">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5407e-992">Environment variables</span></span>
* <span data-ttu-id="5407e-993">объектов .NET в памяти;</span><span class="sxs-lookup"><span data-stu-id="5407e-993">In-memory .NET objects</span></span>
* <span data-ttu-id="5407e-994">файлов параметров;</span><span class="sxs-lookup"><span data-stu-id="5407e-994">Settings files</span></span>

<span data-ttu-id="5407e-995">Пакеты конфигурации для распространенных вариантов провайдеров конфигурации ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) включаются в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5407e-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5407e-996">В приведенных ниже и представленных в образце приложения примерах кода используется пространство имен <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="5407e-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5407e-997">*Шаблон параметров* является расширением конфигурации основных понятий, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5407e-998">Параметры используют классы для представления групп связанных настроек.</span><span class="sxs-lookup"><span data-stu-id="5407e-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5407e-999">Для получения дополнительной информации см. <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5407e-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5407e-1000">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5407e-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5407e-1001">Конфигурация узла и приложения</span><span class="sxs-lookup"><span data-stu-id="5407e-1001">Host versus app configuration</span></span>

<span data-ttu-id="5407e-1002">Перед настройкой и запуском приложения настройте и запустите *узел*.</span><span class="sxs-lookup"><span data-stu-id="5407e-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5407e-1003">Узел отвечает за запуск приложения и управление временем существования.</span><span class="sxs-lookup"><span data-stu-id="5407e-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5407e-1004">Как приложение, так и узел настраиваются с использованием поставщиков конфигурации, описанных в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5407e-1005">Пары "ключ — значение" конфигурации узлов также включаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5407e-1006">Дополнительные сведения о том, как используются поставщики конфигурации при создании узла и как влияют источники конфигурации на узел, см. в разделе <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5407e-1007">Другая конфигурация</span><span class="sxs-lookup"><span data-stu-id="5407e-1007">Other configuration</span></span>

<span data-ttu-id="5407e-1008">Этот раздел относится только к *конфигурации приложений*.</span><span class="sxs-lookup"><span data-stu-id="5407e-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5407e-1009">Другие аспекты запуска и размещения приложений ASP.NET Core настраиваются с помощью файлов конфигурации, которые не рассматриваются в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5407e-1010">*launch.json*/*launchSettings.json* — это файлы конфигурации инструментов для среды разработки, описанные в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="5407e-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5407e-1011">В <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5407e-1012">В документации, где показано, как файлы используются для настройки приложений ASP.NET Core для сценариев разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5407e-1013">*web.config* — это файл конфигурации сервера, описанный в следующих источниках:</span><span class="sxs-lookup"><span data-stu-id="5407e-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5407e-1014">См. сведения о переносе конфигурации приложения из более ранних версий ASP.NET: <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5407e-1015">Конфигурация по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5407e-1015">Default configuration</span></span>

<span data-ttu-id="5407e-1016">Веб-приложения на основе шаблонов [dotnet new](/dotnet/core/tools/dotnet-new) ASP.NET Core вызывают <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> при создании узла.</span><span class="sxs-lookup"><span data-stu-id="5407e-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5407e-1017">`CreateDefaultBuilder` предоставляет конфигурацию по умолчанию для приложения в следующем порядке:</span><span class="sxs-lookup"><span data-stu-id="5407e-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5407e-1018">Приведенные ниже сведения относятся к приложениям, использующим [веб-узел](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="5407e-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5407e-1019">Подробные сведения о конфигурации по умолчанию при использовании [универсального узла](xref:fundamentals/host/generic-host) см. в [последней версии этой статьи](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5407e-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="5407e-1020">Существуют следующие способы предоставления конфигурации узла.</span><span class="sxs-lookup"><span data-stu-id="5407e-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5407e-1021">Переменные среды с префиксом `ASPNETCORE_` (например, `ASPNETCORE_ENVIRONMENT`), использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5407e-1022">Префикс (`ASPNETCORE_`) исключается при загрузке пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5407e-1023">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5407e-1024">Конфигурация приложения предоставляется из следующих ресурсов:</span><span class="sxs-lookup"><span data-stu-id="5407e-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="5407e-1025">Файл *appsettings.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5407e-1026">Файл *appsettings.{Environment}.json*, использующий [поставщик конфигурации файлов](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5407e-1027">[Менеджера секретов](xref:security/app-secrets), когда приложение выполняется в среде `Development` с использованием начальных сборок.</span><span class="sxs-lookup"><span data-stu-id="5407e-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5407e-1028">Переменные среды, использующие [поставщик конфигурации переменных среды](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5407e-1029">Аргументы командной строки, использующие [поставщик конфигурации командной строки](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5407e-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="5407e-1030">Безопасность</span><span class="sxs-lookup"><span data-stu-id="5407e-1030">Security</span></span>

<span data-ttu-id="5407e-1031">Применяйте описанные ниже методики для защиты конфиденциальных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5407e-1032">Никогда не храните пароли или другие конфиденциальные данные в коде поставщика конфигурации или в файлах конфигурации обычного текста.</span><span class="sxs-lookup"><span data-stu-id="5407e-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5407e-1033">Не используйте секреты рабочей среды в средах разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="5407e-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5407e-1034">Указывайте секреты вне проекта, чтобы их нельзя было случайно зафиксировать в репозитории с исходным кодом.</span><span class="sxs-lookup"><span data-stu-id="5407e-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5407e-1035">Дополнительные сведения см. в следующих разделах:</span><span class="sxs-lookup"><span data-stu-id="5407e-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5407e-1036"><xref:security/app-secrets> &ndash; содержит рекомендации по использованию переменных среды для хранения конфиденциальных данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-1036"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5407e-1037">Менеджер секретов использует поставщик конфигурации файла для хранения конфиденциальных данных пользователя в файле JSON в локальной системе.</span><span class="sxs-lookup"><span data-stu-id="5407e-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5407e-1038">Поставщик конфигурации файлов описан ниже в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="5407e-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5407e-1039">В [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) безопасно хранятся секреты приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5407e-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5407e-1040">Для получения дополнительной информации см. <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5407e-1041">Иерархическая модель конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="5407e-1042">API конфигурации способен поддерживать иерархические данные конфигурации, выполняя преобразование в плоскую структуру иерархических данных с использованием разделителя в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5407e-1043">В следующем файле JSON существуют четыре ключа в структурированной иерархии двух разделов.</span><span class="sxs-lookup"><span data-stu-id="5407e-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="5407e-1044">При считывании файла в конфигурацию для сохранения исходной иерархической структуры данных источника конфигурации создаются уникальные ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5407e-1045">Разделы и ключи преобразовываются в плоскую структуру с использованием двоеточия (`:`) для сохранения исходной структуры.</span><span class="sxs-lookup"><span data-stu-id="5407e-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5407e-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-1046">section0:key0</span></span>
* <span data-ttu-id="5407e-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-1047">section0:key1</span></span>
* <span data-ttu-id="5407e-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-1048">section1:key0</span></span>
* <span data-ttu-id="5407e-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-1049">section1:key1</span></span>

<span data-ttu-id="5407e-1050">Методы <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> и <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> доступны для изолирования разделов и дочерних элементов раздела в данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5407e-1051">Эти методы описаны далее в разделе [GetSection, GetChildren и Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="5407e-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5407e-1052">Соглашения</span><span class="sxs-lookup"><span data-stu-id="5407e-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5407e-1053">Источники и поставщики</span><span class="sxs-lookup"><span data-stu-id="5407e-1053">Sources and providers</span></span>

<span data-ttu-id="5407e-1054">При запуске приложения источники конфигурации считываются в порядке, в котором были указаны их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5407e-1055">Поставщики конфигурации, которые реализуют обнаружение изменений, имеют возможность перезагрузить конфигурацию при изменении базовых параметров.</span><span class="sxs-lookup"><span data-stu-id="5407e-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5407e-1056">Так, поставщик файла конфигурации (подробнее о нем далее в этой статье) и [поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) реализуют обнаружение изменений.</span><span class="sxs-lookup"><span data-stu-id="5407e-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5407e-1057">Объект <xref:Microsoft.Extensions.Configuration.IConfiguration> доступен в контейнере [внедрения зависимостей](xref:fundamentals/dependency-injection) приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5407e-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> можно внедрить в <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Razor Pages или <xref:Microsoft.AspNetCore.Mvc.Controller> MVC, чтобы получить конфигурацию для класса.</span><span class="sxs-lookup"><span data-stu-id="5407e-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="5407e-1059">В следующих примерах поле `_config` используется для доступа к значениям конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="5407e-1060">Поставщики конфигурации не могут использовать контейнер DI, так как он недоступен при настройке узла.</span><span class="sxs-lookup"><span data-stu-id="5407e-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5407e-1061">Клавиши</span><span class="sxs-lookup"><span data-stu-id="5407e-1061">Keys</span></span>

<span data-ttu-id="5407e-1062">В ключах конфигурации приняты следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5407e-1063">В ключах не учитывается регистр символов.</span><span class="sxs-lookup"><span data-stu-id="5407e-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="5407e-1064">Например `ConnectionString` и `connectionstring` обрабатываются как эквивалентные ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5407e-1065">Если значение для одного и того же ключа установлено одним и тем же или разными поставщиками конфигурации, последним значением, установленным на ключе, является используемое значение.</span><span class="sxs-lookup"><span data-stu-id="5407e-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5407e-1066">Иерархические ключи</span><span class="sxs-lookup"><span data-stu-id="5407e-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="5407e-1067">При взаимодействии с API конфигурации разделитель-двоеточие (`:`) поддерживается на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="5407e-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5407e-1068">В переменных среды разделитель-двоеточие может не работать на всех платформах.</span><span class="sxs-lookup"><span data-stu-id="5407e-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5407e-1069">Двойной знак подчеркивания (`__`) поддерживается на всех платформах и автоматически преобразовывается в двоеточие.</span><span class="sxs-lookup"><span data-stu-id="5407e-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5407e-1070">В хранилище ключей Azure иерархические ключи используют `--` (два дефиса) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="5407e-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5407e-1071">Чтобы заменить дефисы двоеточием, при загрузке секретов в конфигурацию приложения напишите код.</span><span class="sxs-lookup"><span data-stu-id="5407e-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5407e-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5407e-1073">Привязка массива описана в разделе [Привязка массива к классу](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="5407e-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5407e-1074">Значения</span><span class="sxs-lookup"><span data-stu-id="5407e-1074">Values</span></span>

<span data-ttu-id="5407e-1075">В значениях конфигурации учитываются следующие соглашения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5407e-1076">Значения являются строками.</span><span class="sxs-lookup"><span data-stu-id="5407e-1076">Values are strings.</span></span>
* <span data-ttu-id="5407e-1077">Значение NULL не может храниться в конфигурации или быть привязанным к объектам.</span><span class="sxs-lookup"><span data-stu-id="5407e-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5407e-1078">Поставщики</span><span class="sxs-lookup"><span data-stu-id="5407e-1078">Providers</span></span>

<span data-ttu-id="5407e-1079">В следующей таблице показаны поставщики конфигурации, доступные для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5407e-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5407e-1080">Поставщик</span><span class="sxs-lookup"><span data-stu-id="5407e-1080">Provider</span></span> | <span data-ttu-id="5407e-1081">Предоставляет конфигурацию из &hellip;</span><span class="sxs-lookup"><span data-stu-id="5407e-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="5407e-1082">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1083">'Blazor'</span></span>
- <span data-ttu-id="5407e-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1084">'Identity'</span></span>
- <span data-ttu-id="5407e-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1086">'Razor'</span></span>
- <span data-ttu-id="5407e-1087">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1088">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1089">'Blazor'</span></span>
- <span data-ttu-id="5407e-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1090">'Identity'</span></span>
- <span data-ttu-id="5407e-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1092">'Razor'</span></span>
- <span data-ttu-id="5407e-1093">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1094">---- | --- название: автор: описание:  monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1095">'Blazor'</span></span>
- <span data-ttu-id="5407e-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1096">'Identity'</span></span>
- <span data-ttu-id="5407e-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1098">'Razor'</span></span>
- <span data-ttu-id="5407e-1099">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1100">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1101">'Blazor'</span></span>
- <span data-ttu-id="5407e-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1102">'Identity'</span></span>
- <span data-ttu-id="5407e-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1104">'Razor'</span></span>
- <span data-ttu-id="5407e-1105">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1106">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1107">'Blazor'</span></span>
- <span data-ttu-id="5407e-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1108">'Identity'</span></span>
- <span data-ttu-id="5407e-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1110">'Razor'</span></span>
- <span data-ttu-id="5407e-1111">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1112">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1113">'Blazor'</span></span>
- <span data-ttu-id="5407e-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1114">'Identity'</span></span>
- <span data-ttu-id="5407e-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1116">'Razor'</span></span>
- <span data-ttu-id="5407e-1117">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1118">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1119">'Blazor'</span></span>
- <span data-ttu-id="5407e-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1120">'Identity'</span></span>
- <span data-ttu-id="5407e-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1122">'Razor'</span></span>
- <span data-ttu-id="5407e-1123">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1124">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1125">'Blazor'</span></span>
- <span data-ttu-id="5407e-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1126">'Identity'</span></span>
- <span data-ttu-id="5407e-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1128">'Razor'</span></span>
- <span data-ttu-id="5407e-1129">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1130">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1131">'Blazor'</span></span>
- <span data-ttu-id="5407e-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1132">'Identity'</span></span>
- <span data-ttu-id="5407e-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1134">'Razor'</span></span>
- <span data-ttu-id="5407e-1135">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1136">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1137">'Blazor'</span></span>
- <span data-ttu-id="5407e-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1138">'Identity'</span></span>
- <span data-ttu-id="5407e-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1140">'Razor'</span></span>
- <span data-ttu-id="5407e-1141">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1142">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1143">'Blazor'</span></span>
- <span data-ttu-id="5407e-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1144">'Identity'</span></span>
- <span data-ttu-id="5407e-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1146">'Razor'</span></span>
- <span data-ttu-id="5407e-1147">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1148">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1149">'Blazor'</span></span>
- <span data-ttu-id="5407e-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1150">'Identity'</span></span>
- <span data-ttu-id="5407e-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1152">'Razor'</span></span>
- <span data-ttu-id="5407e-1153">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1154">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1155">'Blazor'</span></span>
- <span data-ttu-id="5407e-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1156">'Identity'</span></span>
- <span data-ttu-id="5407e-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1158">'Razor'</span></span>
- <span data-ttu-id="5407e-1159">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1160">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1161">'Blazor'</span></span>
- <span data-ttu-id="5407e-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1162">'Identity'</span></span>
- <span data-ttu-id="5407e-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1164">'Razor'</span></span>
- <span data-ttu-id="5407e-1165">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1166">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1167">'Blazor'</span></span>
- <span data-ttu-id="5407e-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1168">'Identity'</span></span>
- <span data-ttu-id="5407e-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1170">'Razor'</span></span>
- <span data-ttu-id="5407e-1171">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1172">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1173">'Blazor'</span></span>
- <span data-ttu-id="5407e-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1174">'Identity'</span></span>
- <span data-ttu-id="5407e-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1176">'Razor'</span></span>
- <span data-ttu-id="5407e-1177">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1178">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1179">'Blazor'</span></span>
- <span data-ttu-id="5407e-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1180">'Identity'</span></span>
- <span data-ttu-id="5407e-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1182">'Razor'</span></span>
- <span data-ttu-id="5407e-1183">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1184">------------------ | | [Поставщик конфигурации Azure Key Vault](xref:security/key-vault-configuration) (разделы *Безопасность*) | Azure Key Vault | | [ Поставщик Конфигурации приложений Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (документация Azure) | Конфигурация приложений Azure | | [Поставщик конфигурации командной строки](#command-line-configuration-provider) | Параметры командной строки | | [Поставщик настраиваемой конфигурации](#custom-configuration-provider) | Настраиваемый источник | | [Поставщик конфигурации переменных среды](#environment-variables-configuration-provider) | Переменные среды | | [Поставщик конфигурации файлов](#file-configuration-provider) | Файлы (INI, JSON и XML) | |Поставщик конфигурации ключа для каждого файла[ | ](#key-per-file-configuration-provider)Файлы каталога | | [Поставщик конфигурации памяти](#memory-configuration-provider) | Коллекции в памяти | | [Секреты пользователей (диспетчер секретов)](xref:security/app-secrets) (разделы *Безопасность*) | Файл в каталоге профиля пользователя |</span><span class="sxs-lookup"><span data-stu-id="5407e-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="5407e-1185">Источники конфигурации считываются в том порядке, в котором при запуске указываются их поставщики конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5407e-1186">Поставщики конфигурации в этом разделе описаны в алфавитном порядке, а не в необходимом вам порядке в коде.</span><span class="sxs-lookup"><span data-stu-id="5407e-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="5407e-1187">Порядок поставщиков конфигурации в коде соответствует приоритетам ваших основных источников конфигурации, требуемых приложением.</span><span class="sxs-lookup"><span data-stu-id="5407e-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5407e-1188">Типичная последовательность поставщиков конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5407e-1189">Файлы (*appsettings.json*, *appsettings.{Environment}.json*, где `{Environment}` — это текущая среда размещения приложения)</span><span class="sxs-lookup"><span data-stu-id="5407e-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. <span data-ttu-id="5407e-1190">[Azure Key Vault](xref:security/key-vault-configuration);</span><span class="sxs-lookup"><span data-stu-id="5407e-1190">[Azure Key Vault](xref:security/key-vault-configuration)</span></span>
1. <span data-ttu-id="5407e-1191">[Секреты пользователя (Менеджер секретов)](xref:security/app-secrets) (только в среде разработки)</span><span class="sxs-lookup"><span data-stu-id="5407e-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5407e-1192">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5407e-1192">Environment variables</span></span>
1. <span data-ttu-id="5407e-1193">аргументов командной строки;</span><span class="sxs-lookup"><span data-stu-id="5407e-1193">Command-line arguments</span></span>

<span data-ttu-id="5407e-1194">Общепринятой практикой является размещение поставщика конфигурации командной строки последним в ряду поставщиков, чтобы аргументы командной строки могли переопределять конфигурацию, установленную другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="5407e-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5407e-1195">Предыдущая последовательность поставщиков используется при инициализации нового построителя узла с помощью `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5407e-1196">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5407e-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="5407e-1197">Настройка построителя узла с помощью UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="5407e-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="5407e-1198">Чтобы настроить построитель узла, вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> в построителе узла с конфигурацией.</span><span class="sxs-lookup"><span data-stu-id="5407e-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="5407e-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5407e-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5407e-1200">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать поставщики конфигурации приложения в дополнение к тем, которые автоматически добавлены `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5407e-1201">Переопределение предыдущей конфигурации с помощью аргументов командной строки</span><span class="sxs-lookup"><span data-stu-id="5407e-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5407e-1202">Чтобы предоставить конфигурацию приложения, которую можно переопределить с помощью аргументов командной строки, вызовите метод `AddCommandLine` последним:</span><span class="sxs-lookup"><span data-stu-id="5407e-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5407e-1203">Удаление поставщиков, добавленных CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="5407e-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5407e-1204">Чтобы удалить поставщиков, добавленных `CreateDefaultBuilder`, сначала вызовите [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) в [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources):</span><span class="sxs-lookup"><span data-stu-id="5407e-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5407e-1205">Использование конфигурации во время запуска приложения</span><span class="sxs-lookup"><span data-stu-id="5407e-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="5407e-1206">Конфигурация, предоставленная приложению в `ConfigureAppConfiguration`, доступна во время запуска приложения, включая `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5407e-1207">Дополнительные сведения см. в разделе [Доступ к конфигурации во время запуска](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="5407e-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5407e-1208">Поставщик конфигурации командной строки</span><span class="sxs-lookup"><span data-stu-id="5407e-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="5407e-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> загружает конфигурацию из пары "ключ — значение" аргумента командной строки в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-1210">Чтобы активировать конфигурацию командной строки, вызывается метод расширения <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> для экземпляра <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5407e-1211">`AddCommandLine` вызывается автоматически при вызове `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5407e-1212">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5407e-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5407e-1213">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="5407e-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5407e-1214">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="5407e-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5407e-1215">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5407e-1216">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-1216">Environment variables.</span></span>

<span data-ttu-id="5407e-1217">`CreateDefaultBuilder` добавляет последним поставщика конфигурации командной строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5407e-1218">Аргументы командной строки передаются в набор конфигурации переопределения среды выполнения, установленной другими поставщиками.</span><span class="sxs-lookup"><span data-stu-id="5407e-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5407e-1219">`CreateDefaultBuilder` действует, когда создается узел.</span><span class="sxs-lookup"><span data-stu-id="5407e-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5407e-1220">Поэтому конфигурация командной строки, активированная с помощью `CreateDefaultBuilder`, может повлиять на настройку узла.</span><span class="sxs-lookup"><span data-stu-id="5407e-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5407e-1221">Для приложений на основе шаблонов ASP.NET Core метод `AddCommandLine` уже был вызван методом `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5407e-1222">Чтобы добавить дополнительные поставщики конфигурации и обеспечить возможность переопределения конфигурации от этих поставщиков с помощью аргументов командной строки, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration` и вызовите `AddCommandLine` в последнюю очередь.</span><span class="sxs-lookup"><span data-stu-id="5407e-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5407e-1223">**Пример**</span><span class="sxs-lookup"><span data-stu-id="5407e-1223">**Example**</span></span>

<span data-ttu-id="5407e-1224">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5407e-1225">Откройте командную строку в каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="5407e-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5407e-1226">Поставьте аргумент командной строки в команду `dotnet run`: `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5407e-1227">После запуска приложения откройте браузер в приложении по адресу `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5407e-1228">Обратите внимание, что вывод содержит пару "ключ — значение" для аргумента командной строки конфигурации, предоставленного для `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5407e-1229">Аргументы</span><span class="sxs-lookup"><span data-stu-id="5407e-1229">Arguments</span></span>

<span data-ttu-id="5407e-1230">Значение должно соответствовать знаку равенства (`=`), или ключ должен иметь префикс (`--` или `/`), когда значение следует за пробелом.</span><span class="sxs-lookup"><span data-stu-id="5407e-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5407e-1231">Значение не требуется, если используется знак равенства (например, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="5407e-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5407e-1232">Префикс ключа</span><span class="sxs-lookup"><span data-stu-id="5407e-1232">Key prefix</span></span>               | <span data-ttu-id="5407e-1233">Пример</span><span class="sxs-lookup"><span data-stu-id="5407e-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="5407e-1234">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1235">'Blazor'</span></span>
- <span data-ttu-id="5407e-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1236">'Identity'</span></span>
- <span data-ttu-id="5407e-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1238">'Razor'</span></span>
- <span data-ttu-id="5407e-1239">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1240">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1241">'Blazor'</span></span>
- <span data-ttu-id="5407e-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1242">'Identity'</span></span>
- <span data-ttu-id="5407e-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1244">'Razor'</span></span>
- <span data-ttu-id="5407e-1245">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1246">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1247">'Blazor'</span></span>
- <span data-ttu-id="5407e-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1248">'Identity'</span></span>
- <span data-ttu-id="5407e-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1250">'Razor'</span></span>
- <span data-ttu-id="5407e-1251">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1252">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1253">'Blazor'</span></span>
- <span data-ttu-id="5407e-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1254">'Identity'</span></span>
- <span data-ttu-id="5407e-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1256">'Razor'</span></span>
- <span data-ttu-id="5407e-1257">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1258">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1259">'Blazor'</span></span>
- <span data-ttu-id="5407e-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1260">'Identity'</span></span>
- <span data-ttu-id="5407e-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1262">'Razor'</span></span>
- <span data-ttu-id="5407e-1263">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1264">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1265">'Blazor'</span></span>
- <span data-ttu-id="5407e-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1266">'Identity'</span></span>
- <span data-ttu-id="5407e-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1268">'Razor'</span></span>
- <span data-ttu-id="5407e-1269">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1270">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1271">'Blazor'</span></span>
- <span data-ttu-id="5407e-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1272">'Identity'</span></span>
- <span data-ttu-id="5407e-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1274">'Razor'</span></span>
- <span data-ttu-id="5407e-1275">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1276">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1277">'Blazor'</span></span>
- <span data-ttu-id="5407e-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1278">'Identity'</span></span>
- <span data-ttu-id="5407e-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1280">'Razor'</span></span>
- <span data-ttu-id="5407e-1281">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1282">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1283">'Blazor'</span></span>
- <span data-ttu-id="5407e-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1284">'Identity'</span></span>
- <span data-ttu-id="5407e-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1286">'Razor'</span></span>
- <span data-ttu-id="5407e-1287">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1288">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1289">'Blazor'</span></span>
- <span data-ttu-id="5407e-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1290">'Identity'</span></span>
- <span data-ttu-id="5407e-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1292">'Razor'</span></span>
- <span data-ttu-id="5407e-1293">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1294">------------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1295">'Blazor'</span></span>
- <span data-ttu-id="5407e-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1296">'Identity'</span></span>
- <span data-ttu-id="5407e-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1298">'Razor'</span></span>
- <span data-ttu-id="5407e-1299">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1300">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1301">'Blazor'</span></span>
- <span data-ttu-id="5407e-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1302">'Identity'</span></span>
- <span data-ttu-id="5407e-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1304">'Razor'</span></span>
- <span data-ttu-id="5407e-1305">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1306">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1307">'Blazor'</span></span>
- <span data-ttu-id="5407e-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1308">'Identity'</span></span>
- <span data-ttu-id="5407e-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1310">'Razor'</span></span>
- <span data-ttu-id="5407e-1311">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1312">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1313">'Blazor'</span></span>
- <span data-ttu-id="5407e-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1314">'Identity'</span></span>
- <span data-ttu-id="5407e-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1316">'Razor'</span></span>
- <span data-ttu-id="5407e-1317">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1318">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1319">'Blazor'</span></span>
- <span data-ttu-id="5407e-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1320">'Identity'</span></span>
- <span data-ttu-id="5407e-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1322">'Razor'</span></span>
- <span data-ttu-id="5407e-1323">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1324">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1325">'Blazor'</span></span>
- <span data-ttu-id="5407e-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1326">'Identity'</span></span>
- <span data-ttu-id="5407e-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1328">'Razor'</span></span>
- <span data-ttu-id="5407e-1329">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1330">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1331">'Blazor'</span></span>
- <span data-ttu-id="5407e-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1332">'Identity'</span></span>
- <span data-ttu-id="5407e-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1334">'Razor'</span></span>
- <span data-ttu-id="5407e-1335">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1336">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1337">'Blazor'</span></span>
- <span data-ttu-id="5407e-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1338">'Identity'</span></span>
- <span data-ttu-id="5407e-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1340">'Razor'</span></span>
- <span data-ttu-id="5407e-1341">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1342">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1343">'Blazor'</span></span>
- <span data-ttu-id="5407e-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1344">'Identity'</span></span>
- <span data-ttu-id="5407e-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1346">'Razor'</span></span>
- <span data-ttu-id="5407e-1347">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1348">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1349">'Blazor'</span></span>
- <span data-ttu-id="5407e-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1350">'Identity'</span></span>
- <span data-ttu-id="5407e-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1352">'Razor'</span></span>
- <span data-ttu-id="5407e-1353">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1354">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1355">'Blazor'</span></span>
- <span data-ttu-id="5407e-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1356">'Identity'</span></span>
- <span data-ttu-id="5407e-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1358">'Razor'</span></span>
- <span data-ttu-id="5407e-1359">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1360">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1361">'Blazor'</span></span>
- <span data-ttu-id="5407e-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1362">'Identity'</span></span>
- <span data-ttu-id="5407e-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1364">'Razor'</span></span>
- <span data-ttu-id="5407e-1365">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1366">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1367">'Blazor'</span></span>
- <span data-ttu-id="5407e-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1368">'Identity'</span></span>
- <span data-ttu-id="5407e-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1370">'Razor'</span></span>
- <span data-ttu-id="5407e-1371">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1372">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1373">'Blazor'</span></span>
- <span data-ttu-id="5407e-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1374">'Identity'</span></span>
- <span data-ttu-id="5407e-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1376">'Razor'</span></span>
- <span data-ttu-id="5407e-1377">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1378">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1379">'Blazor'</span></span>
- <span data-ttu-id="5407e-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1380">'Identity'</span></span>
- <span data-ttu-id="5407e-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1382">'Razor'</span></span>
- <span data-ttu-id="5407e-1383">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1384">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1385">'Blazor'</span></span>
- <span data-ttu-id="5407e-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1386">'Identity'</span></span>
- <span data-ttu-id="5407e-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1388">'Razor'</span></span>
- <span data-ttu-id="5407e-1389">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1390">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1391">'Blazor'</span></span>
- <span data-ttu-id="5407e-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1392">'Identity'</span></span>
- <span data-ttu-id="5407e-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1394">'Razor'</span></span>
- <span data-ttu-id="5407e-1395">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1396">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1397">'Blazor'</span></span>
- <span data-ttu-id="5407e-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1398">'Identity'</span></span>
- <span data-ttu-id="5407e-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1400">'Razor'</span></span>
- <span data-ttu-id="5407e-1401">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1402">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1403">'Blazor'</span></span>
- <span data-ttu-id="5407e-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1404">'Identity'</span></span>
- <span data-ttu-id="5407e-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1406">'Razor'</span></span>
- <span data-ttu-id="5407e-1407">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1408">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1409">'Blazor'</span></span>
- <span data-ttu-id="5407e-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1410">'Identity'</span></span>
- <span data-ttu-id="5407e-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1412">'Razor'</span></span>
- <span data-ttu-id="5407e-1413">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1414">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1415">'Blazor'</span></span>
- <span data-ttu-id="5407e-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1416">'Identity'</span></span>
- <span data-ttu-id="5407e-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1418">'Razor'</span></span>
- <span data-ttu-id="5407e-1419">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1420">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1421">'Blazor'</span></span>
- <span data-ttu-id="5407e-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1422">'Identity'</span></span>
- <span data-ttu-id="5407e-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1424">'Razor'</span></span>
- <span data-ttu-id="5407e-1425">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1426">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1427">'Blazor'</span></span>
- <span data-ttu-id="5407e-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1428">'Identity'</span></span>
- <span data-ttu-id="5407e-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1430">'Razor'</span></span>
- <span data-ttu-id="5407e-1431">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1432">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1433">'Blazor'</span></span>
- <span data-ttu-id="5407e-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1434">'Identity'</span></span>
- <span data-ttu-id="5407e-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1436">'Razor'</span></span>
- <span data-ttu-id="5407e-1437">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1438">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1439">'Blazor'</span></span>
- <span data-ttu-id="5407e-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1440">'Identity'</span></span>
- <span data-ttu-id="5407e-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1442">'Razor'</span></span>
- <span data-ttu-id="5407e-1443">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1444">--------------------------- | | Без префикса                | `CommandLineKey1=value1`                               |
| Два тире (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Косая черта (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="5407e-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="5407e-1445">В рамках одной и той же команды не смешивайте пары "ключ — значение" аргумента командной строки, которые используют знак равенства, с парами "ключ — значение", которые используют пробел.</span><span class="sxs-lookup"><span data-stu-id="5407e-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5407e-1446">Примеры команд.</span><span class="sxs-lookup"><span data-stu-id="5407e-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5407e-1447">Сопоставления переключений</span><span class="sxs-lookup"><span data-stu-id="5407e-1447">Switch mappings</span></span>

<span data-ttu-id="5407e-1448">Сопоставление параметров позволяет указать логику замены имен ключей.</span><span class="sxs-lookup"><span data-stu-id="5407e-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5407e-1449">Когда вручную создается конфигурация с помощью <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, методу <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> можно предоставить словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="5407e-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5407e-1450">В словаре сопоставлений переключений выполняется поиск ключа, который совпадает с ключом, предоставляемым аргументом командной строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5407e-1451">Если ключ в командной строке находится в словаре, значение словаря (замена ключа) передается обратно, чтобы установить пару "ключ — значение" в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5407e-1452">Сопоставление переключений необходимо для любого ключа командной строки с префиксом из одного дефиса (`-`).</span><span class="sxs-lookup"><span data-stu-id="5407e-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5407e-1453">Правила ключей из словаря сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="5407e-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5407e-1454">Переключения должны начинаться с дефиса (`-`) или двойного дефиса (`--`).</span><span class="sxs-lookup"><span data-stu-id="5407e-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5407e-1455">Словарь сопоставлений переключений не должен содержать повторяющиеся ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5407e-1456">Создайте словарь сопоставления переключений.</span><span class="sxs-lookup"><span data-stu-id="5407e-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5407e-1457">В следующем примере создаются два сопоставления переключений:</span><span class="sxs-lookup"><span data-stu-id="5407e-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5407e-1458">При сборке узла вызовите `AddCommandLine` со словарем сопоставлений переключений:</span><span class="sxs-lookup"><span data-stu-id="5407e-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5407e-1459">Для приложений, использующих сопоставления переключений, в вызове `CreateDefaultBuilder` аргументы передаваться не должны.</span><span class="sxs-lookup"><span data-stu-id="5407e-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5407e-1460">Вызов команды `AddCommandLine` метода `CreateDefaultBuilder` не включает сопоставленные переключения, и нет возможности передать словарь сопоставления переключений в `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5407e-1461">Чтобы решить эту проблему, нужно не передавать аргументы команде `CreateDefaultBuilder`, а позволить методу `AddCommandLine` метода `ConfigurationBuilder` обрабатывать как аргументы, так и словарь сопоставления параметров.</span><span class="sxs-lookup"><span data-stu-id="5407e-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5407e-1462">Созданный словарь сопоставлений переключений содержит данные, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5407e-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5407e-1463">Ключ</span><span class="sxs-lookup"><span data-stu-id="5407e-1463">Key</span></span>       | <span data-ttu-id="5407e-1464">Значение</span><span class="sxs-lookup"><span data-stu-id="5407e-1464">Value</span></span>             |
| ---
<span data-ttu-id="5407e-1465">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1466">'Blazor'</span></span>
- <span data-ttu-id="5407e-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1467">'Identity'</span></span>
- <span data-ttu-id="5407e-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1469">'Razor'</span></span>
- <span data-ttu-id="5407e-1470">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1471">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1472">'Blazor'</span></span>
- <span data-ttu-id="5407e-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1473">'Identity'</span></span>
- <span data-ttu-id="5407e-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1475">'Razor'</span></span>
- <span data-ttu-id="5407e-1476">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1477">----- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1478">'Blazor'</span></span>
- <span data-ttu-id="5407e-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1479">'Identity'</span></span>
- <span data-ttu-id="5407e-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1481">'Razor'</span></span>
- <span data-ttu-id="5407e-1482">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1483">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1484">'Blazor'</span></span>
- <span data-ttu-id="5407e-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1485">'Identity'</span></span>
- <span data-ttu-id="5407e-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1487">'Razor'</span></span>
- <span data-ttu-id="5407e-1488">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1489">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1490">'Blazor'</span></span>
- <span data-ttu-id="5407e-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1491">'Identity'</span></span>
- <span data-ttu-id="5407e-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1493">'Razor'</span></span>
- <span data-ttu-id="5407e-1494">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1495">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1496">'Blazor'</span></span>
- <span data-ttu-id="5407e-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1497">'Identity'</span></span>
- <span data-ttu-id="5407e-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1499">'Razor'</span></span>
- <span data-ttu-id="5407e-1500">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1501">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1502">'Blazor'</span></span>
- <span data-ttu-id="5407e-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1503">'Identity'</span></span>
- <span data-ttu-id="5407e-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1505">'Razor'</span></span>
- <span data-ttu-id="5407e-1506">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1507">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1508">'Blazor'</span></span>
- <span data-ttu-id="5407e-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1509">'Identity'</span></span>
- <span data-ttu-id="5407e-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1511">'Razor'</span></span>
- <span data-ttu-id="5407e-1512">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="5407e-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="5407e-1514">Если ключи сопоставления переключений используются при запуске приложения, конфигурация принимает значение конфигурации в ключе, предоставленном в словаре.</span><span class="sxs-lookup"><span data-stu-id="5407e-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5407e-1515">После выполнения предыдущей команды конфигурация содержит значения, показанные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5407e-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5407e-1516">Ключ</span><span class="sxs-lookup"><span data-stu-id="5407e-1516">Key</span></span>               | <span data-ttu-id="5407e-1517">Значение</span><span class="sxs-lookup"><span data-stu-id="5407e-1517">Value</span></span>    |
| ---
<span data-ttu-id="5407e-1518">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1519">'Blazor'</span></span>
- <span data-ttu-id="5407e-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1520">'Identity'</span></span>
- <span data-ttu-id="5407e-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1522">'Razor'</span></span>
- <span data-ttu-id="5407e-1523">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1524">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1525">'Blazor'</span></span>
- <span data-ttu-id="5407e-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1526">'Identity'</span></span>
- <span data-ttu-id="5407e-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1528">'Razor'</span></span>
- <span data-ttu-id="5407e-1529">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1530">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1531">'Blazor'</span></span>
- <span data-ttu-id="5407e-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1532">'Identity'</span></span>
- <span data-ttu-id="5407e-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1534">'Razor'</span></span>
- <span data-ttu-id="5407e-1535">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1536">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1537">'Blazor'</span></span>
- <span data-ttu-id="5407e-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1538">'Identity'</span></span>
- <span data-ttu-id="5407e-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1540">'Razor'</span></span>
- <span data-ttu-id="5407e-1541">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1542">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1543">'Blazor'</span></span>
- <span data-ttu-id="5407e-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1544">'Identity'</span></span>
- <span data-ttu-id="5407e-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1546">'Razor'</span></span>
- <span data-ttu-id="5407e-1547">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1548">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1549">'Blazor'</span></span>
- <span data-ttu-id="5407e-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1550">'Identity'</span></span>
- <span data-ttu-id="5407e-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1552">'Razor'</span></span>
- <span data-ttu-id="5407e-1553">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1554">--------- | --- название: автор: описание:  monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1555">'Blazor'</span></span>
- <span data-ttu-id="5407e-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1556">'Identity'</span></span>
- <span data-ttu-id="5407e-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1558">'Razor'</span></span>
- <span data-ttu-id="5407e-1559">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1560">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1561">'Blazor'</span></span>
- <span data-ttu-id="5407e-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1562">'Identity'</span></span>
- <span data-ttu-id="5407e-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1564">'Razor'</span></span>
- <span data-ttu-id="5407e-1565">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="5407e-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5407e-1567">Поставщик конфигурации переменных среды</span><span class="sxs-lookup"><span data-stu-id="5407e-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5407e-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> загружает конфигурацию из пары "ключ — значение" переменной среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-1569">Чтобы активировать конфигурацию переменных среды, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5407e-1570">[Служба приложений Azure](https://azure.microsoft.com/services/app-service/) позволяет задать переменные среды на портале Azure, который может переопределить конфигурацию приложения, используя поставщик конфигурации переменных среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5407e-1571">Дополнительные сведения см. в руководстве по [переопределению конфигурации приложения Azure с помощью портала Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5407e-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5407e-1572">`AddEnvironmentVariables` используется для загрузки переменных среды, имеющих префикс `ASPNETCORE_`, для [конфигурации узла](#host-versus-app-configuration) при инициализации нового построителя узла с [веб-узлом](xref:fundamentals/host/web-host) и вызове `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5407e-1573">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5407e-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5407e-1574">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="5407e-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5407e-1575">конфигурация приложения на основе переменных среды без префикса путем вызова `AddEnvironmentVariables` без префикса;</span><span class="sxs-lookup"><span data-stu-id="5407e-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5407e-1576">дополнительную конфигурацию из файлов *appsettings.json* и *appsettings.{Environment}.json*;</span><span class="sxs-lookup"><span data-stu-id="5407e-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5407e-1577">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5407e-1578">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-1578">Command-line arguments.</span></span>

<span data-ttu-id="5407e-1579">Поставщик конфигурации переменных среды вызывается после выполнения настройки с помощью секретов пользователя и файлов *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5407e-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5407e-1580">Вызов поставщика в этой позиции разрешает чтение переменных среды выполнения, чтобы переопределить конфигурацию, заданную секретом пользователя и файлом *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5407e-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5407e-1581">Чтобы добавить конфигурацию приложения из дополнительных переменных среды, вызовите дополнительные поставщики приложения в `ConfigureAppConfiguration`, а затем вызовите `AddEnvironmentVariables` с префиксом:</span><span class="sxs-lookup"><span data-stu-id="5407e-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="5407e-1582">Вызовите `AddEnvironmentVariables` последним, чтобы разрешить переменным среды с заданным префиксом переопределять значения от других поставщиков.</span><span class="sxs-lookup"><span data-stu-id="5407e-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="5407e-1583">**Пример**</span><span class="sxs-lookup"><span data-stu-id="5407e-1583">**Example**</span></span>

<span data-ttu-id="5407e-1584">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает вызов `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5407e-1585">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1585">Run the sample app.</span></span> <span data-ttu-id="5407e-1586">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5407e-1587">Обратите внимание, что выходные данные содержат пару "ключ — значение" для переменной среды `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5407e-1588">Значение отражает среду, в которой выполняется приложение, обычно при локальном запуске это `Development`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5407e-1589">Чтобы список переменных среды, отображаемый приложением, был коротким, приложение фильтрует переменные среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5407e-1590">См. пример файла *Pages/Index.cshtml.cs* приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5407e-1591">Чтобы просмотреть все переменные среды, доступные для приложения, измените значение `FilteredConfiguration` в *Pages/Index.cshtml.cs* на следующее:</span><span class="sxs-lookup"><span data-stu-id="5407e-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5407e-1592">Префиксы</span><span class="sxs-lookup"><span data-stu-id="5407e-1592">Prefixes</span></span>

<span data-ttu-id="5407e-1593">Переменные среды, которые загружаются в конфигурацию приложения, фильтруются при добавлении префикса к методу `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="5407e-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5407e-1594">Например, чтобы отфильтровать переменные среды по префиксу `CUSTOM_`, введите префикс поставщику конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5407e-1595">Префикс отделяется при создании пары конфигурации "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="5407e-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5407e-1596">При создании построителя узла конфигурация узла предоставляется переменными среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5407e-1597">Дополнительные сведения о префиксе, используемом для этих переменных среды, см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5407e-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5407e-1598">**Префиксы строк подключения**</span><span class="sxs-lookup"><span data-stu-id="5407e-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="5407e-1599">API конфигурации имеет специальные правила обработки для четырех строк подключения переменных среды, связанных с настройкой строк подключения Azure для среды приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5407e-1600">Если префикс не указан в `AddEnvironmentVariables`, переменные среды с префиксами, указанными в таблице, загружаются в приложение.</span><span class="sxs-lookup"><span data-stu-id="5407e-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5407e-1601">Префикс строки подключения</span><span class="sxs-lookup"><span data-stu-id="5407e-1601">Connection string prefix</span></span> | <span data-ttu-id="5407e-1602">Поставщик</span><span class="sxs-lookup"><span data-stu-id="5407e-1602">Provider</span></span> |
| ---
<span data-ttu-id="5407e-1603">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1604">'Blazor'</span></span>
- <span data-ttu-id="5407e-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1605">'Identity'</span></span>
- <span data-ttu-id="5407e-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1607">'Razor'</span></span>
- <span data-ttu-id="5407e-1608">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1609">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1610">'Blazor'</span></span>
- <span data-ttu-id="5407e-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1611">'Identity'</span></span>
- <span data-ttu-id="5407e-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1613">'Razor'</span></span>
- <span data-ttu-id="5407e-1614">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1615">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1616">'Blazor'</span></span>
- <span data-ttu-id="5407e-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1617">'Identity'</span></span>
- <span data-ttu-id="5407e-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1619">'Razor'</span></span>
- <span data-ttu-id="5407e-1620">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1621">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1622">'Blazor'</span></span>
- <span data-ttu-id="5407e-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1623">'Identity'</span></span>
- <span data-ttu-id="5407e-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1625">'Razor'</span></span>
- <span data-ttu-id="5407e-1626">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1627">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1628">'Blazor'</span></span>
- <span data-ttu-id="5407e-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1629">'Identity'</span></span>
- <span data-ttu-id="5407e-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1631">'Razor'</span></span>
- <span data-ttu-id="5407e-1632">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1633">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1634">'Blazor'</span></span>
- <span data-ttu-id="5407e-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1635">'Identity'</span></span>
- <span data-ttu-id="5407e-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1637">'Razor'</span></span>
- <span data-ttu-id="5407e-1638">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1639">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1640">'Blazor'</span></span>
- <span data-ttu-id="5407e-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1641">'Identity'</span></span>
- <span data-ttu-id="5407e-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1643">'Razor'</span></span>
- <span data-ttu-id="5407e-1644">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1645">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1646">'Blazor'</span></span>
- <span data-ttu-id="5407e-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1647">'Identity'</span></span>
- <span data-ttu-id="5407e-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1649">'Razor'</span></span>
- <span data-ttu-id="5407e-1650">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1651">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1652">'Blazor'</span></span>
- <span data-ttu-id="5407e-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1653">'Identity'</span></span>
- <span data-ttu-id="5407e-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1655">'Razor'</span></span>
- <span data-ttu-id="5407e-1656">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1657">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1658">'Blazor'</span></span>
- <span data-ttu-id="5407e-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1659">'Identity'</span></span>
- <span data-ttu-id="5407e-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1661">'Razor'</span></span>
- <span data-ttu-id="5407e-1662">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1663">------------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1664">'Blazor'</span></span>
- <span data-ttu-id="5407e-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1665">'Identity'</span></span>
- <span data-ttu-id="5407e-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1667">'Razor'</span></span>
- <span data-ttu-id="5407e-1668">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1669">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1670">'Blazor'</span></span>
- <span data-ttu-id="5407e-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1671">'Identity'</span></span>
- <span data-ttu-id="5407e-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1673">'Razor'</span></span>
- <span data-ttu-id="5407e-1674">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1675">---- | | `CUSTOMCONNSTR_` | Настраиваемый поставщик | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [База данных SQL Azure](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="5407e-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="5407e-1676">Когда переменная среды обнаруживается и загружается в конфигурацию с одним из четырех префиксов, приведенных в таблице, происходит следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5407e-1677">Ключ конфигурации создается путем удаления префикса переменных среды и добавления ключа раздела конфигурации (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="5407e-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5407e-1678">Создается новая пара "ключ — значение" конфигурации, которая представляет поставщика подключения базы данных (за исключением `CUSTOMCONNSTR_`, который не имеет указанного поставщика).</span><span class="sxs-lookup"><span data-stu-id="5407e-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5407e-1679">Ключ переменной среды</span><span class="sxs-lookup"><span data-stu-id="5407e-1679">Environment variable key</span></span> | <span data-ttu-id="5407e-1680">Преобразованный ключ конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-1680">Converted configuration key</span></span> | <span data-ttu-id="5407e-1681">Запись конфигурации поставщика</span><span class="sxs-lookup"><span data-stu-id="5407e-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="5407e-1682">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1683">'Blazor'</span></span>
- <span data-ttu-id="5407e-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1684">'Identity'</span></span>
- <span data-ttu-id="5407e-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1686">'Razor'</span></span>
- <span data-ttu-id="5407e-1687">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1688">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1689">'Blazor'</span></span>
- <span data-ttu-id="5407e-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1690">'Identity'</span></span>
- <span data-ttu-id="5407e-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1692">'Razor'</span></span>
- <span data-ttu-id="5407e-1693">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1694">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1695">'Blazor'</span></span>
- <span data-ttu-id="5407e-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1696">'Identity'</span></span>
- <span data-ttu-id="5407e-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1698">'Razor'</span></span>
- <span data-ttu-id="5407e-1699">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1700">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1701">'Blazor'</span></span>
- <span data-ttu-id="5407e-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1702">'Identity'</span></span>
- <span data-ttu-id="5407e-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1704">'Razor'</span></span>
- <span data-ttu-id="5407e-1705">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1706">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1707">'Blazor'</span></span>
- <span data-ttu-id="5407e-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1708">'Identity'</span></span>
- <span data-ttu-id="5407e-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1710">'Razor'</span></span>
- <span data-ttu-id="5407e-1711">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1712">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1713">'Blazor'</span></span>
- <span data-ttu-id="5407e-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1714">'Identity'</span></span>
- <span data-ttu-id="5407e-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1716">'Razor'</span></span>
- <span data-ttu-id="5407e-1717">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1718">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1719">'Blazor'</span></span>
- <span data-ttu-id="5407e-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1720">'Identity'</span></span>
- <span data-ttu-id="5407e-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1722">'Razor'</span></span>
- <span data-ttu-id="5407e-1723">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1724">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1725">'Blazor'</span></span>
- <span data-ttu-id="5407e-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1726">'Identity'</span></span>
- <span data-ttu-id="5407e-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1728">'Razor'</span></span>
- <span data-ttu-id="5407e-1729">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1730">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1731">'Blazor'</span></span>
- <span data-ttu-id="5407e-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1732">'Identity'</span></span>
- <span data-ttu-id="5407e-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1734">'Razor'</span></span>
- <span data-ttu-id="5407e-1735">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1736">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1737">'Blazor'</span></span>
- <span data-ttu-id="5407e-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1738">'Identity'</span></span>
- <span data-ttu-id="5407e-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1740">'Razor'</span></span>
- <span data-ttu-id="5407e-1741">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1742">------------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1743">'Blazor'</span></span>
- <span data-ttu-id="5407e-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1744">'Identity'</span></span>
- <span data-ttu-id="5407e-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1746">'Razor'</span></span>
- <span data-ttu-id="5407e-1747">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1748">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1749">'Blazor'</span></span>
- <span data-ttu-id="5407e-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1750">'Identity'</span></span>
- <span data-ttu-id="5407e-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1752">'Razor'</span></span>
- <span data-ttu-id="5407e-1753">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1754">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1755">'Blazor'</span></span>
- <span data-ttu-id="5407e-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1756">'Identity'</span></span>
- <span data-ttu-id="5407e-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1758">'Razor'</span></span>
- <span data-ttu-id="5407e-1759">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1760">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1761">'Blazor'</span></span>
- <span data-ttu-id="5407e-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1762">'Identity'</span></span>
- <span data-ttu-id="5407e-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1764">'Razor'</span></span>
- <span data-ttu-id="5407e-1765">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1766">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1767">'Blazor'</span></span>
- <span data-ttu-id="5407e-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1768">'Identity'</span></span>
- <span data-ttu-id="5407e-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1770">'Razor'</span></span>
- <span data-ttu-id="5407e-1771">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1772">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1773">'Blazor'</span></span>
- <span data-ttu-id="5407e-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1774">'Identity'</span></span>
- <span data-ttu-id="5407e-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1776">'Razor'</span></span>
- <span data-ttu-id="5407e-1777">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1778">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1779">'Blazor'</span></span>
- <span data-ttu-id="5407e-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1780">'Identity'</span></span>
- <span data-ttu-id="5407e-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1782">'Razor'</span></span>
- <span data-ttu-id="5407e-1783">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1784">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1785">'Blazor'</span></span>
- <span data-ttu-id="5407e-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1786">'Identity'</span></span>
- <span data-ttu-id="5407e-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1788">'Razor'</span></span>
- <span data-ttu-id="5407e-1789">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1790">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1791">'Blazor'</span></span>
- <span data-ttu-id="5407e-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1792">'Identity'</span></span>
- <span data-ttu-id="5407e-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1794">'Razor'</span></span>
- <span data-ttu-id="5407e-1795">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1796">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1797">'Blazor'</span></span>
- <span data-ttu-id="5407e-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1798">'Identity'</span></span>
- <span data-ttu-id="5407e-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1800">'Razor'</span></span>
- <span data-ttu-id="5407e-1801">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1802">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1803">'Blazor'</span></span>
- <span data-ttu-id="5407e-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1804">'Identity'</span></span>
- <span data-ttu-id="5407e-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1806">'Razor'</span></span>
- <span data-ttu-id="5407e-1807">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-1808">-------------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1809">'Blazor'</span></span>
- <span data-ttu-id="5407e-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1810">'Identity'</span></span>
- <span data-ttu-id="5407e-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1812">'Razor'</span></span>
- <span data-ttu-id="5407e-1813">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1814">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1815">'Blazor'</span></span>
- <span data-ttu-id="5407e-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1816">'Identity'</span></span>
- <span data-ttu-id="5407e-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1818">'Razor'</span></span>
- <span data-ttu-id="5407e-1819">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1820">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1821">'Blazor'</span></span>
- <span data-ttu-id="5407e-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1822">'Identity'</span></span>
- <span data-ttu-id="5407e-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1824">'Razor'</span></span>
- <span data-ttu-id="5407e-1825">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1826">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1827">'Blazor'</span></span>
- <span data-ttu-id="5407e-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1828">'Identity'</span></span>
- <span data-ttu-id="5407e-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1830">'Razor'</span></span>
- <span data-ttu-id="5407e-1831">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1832">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1833">'Blazor'</span></span>
- <span data-ttu-id="5407e-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1834">'Identity'</span></span>
- <span data-ttu-id="5407e-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1836">'Razor'</span></span>
- <span data-ttu-id="5407e-1837">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1838">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1839">'Blazor'</span></span>
- <span data-ttu-id="5407e-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1840">'Identity'</span></span>
- <span data-ttu-id="5407e-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1842">'Razor'</span></span>
- <span data-ttu-id="5407e-1843">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1844">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1845">'Blazor'</span></span>
- <span data-ttu-id="5407e-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1846">'Identity'</span></span>
- <span data-ttu-id="5407e-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1848">'Razor'</span></span>
- <span data-ttu-id="5407e-1849">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1850">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1851">'Blazor'</span></span>
- <span data-ttu-id="5407e-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1852">'Identity'</span></span>
- <span data-ttu-id="5407e-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1854">'Razor'</span></span>
- <span data-ttu-id="5407e-1855">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1856">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1857">'Blazor'</span></span>
- <span data-ttu-id="5407e-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1858">'Identity'</span></span>
- <span data-ttu-id="5407e-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1860">'Razor'</span></span>
- <span data-ttu-id="5407e-1861">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1862">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1863">'Blazor'</span></span>
- <span data-ttu-id="5407e-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1864">'Identity'</span></span>
- <span data-ttu-id="5407e-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1866">'Razor'</span></span>
- <span data-ttu-id="5407e-1867">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1868">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1869">'Blazor'</span></span>
- <span data-ttu-id="5407e-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1870">'Identity'</span></span>
- <span data-ttu-id="5407e-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1872">'Razor'</span></span>
- <span data-ttu-id="5407e-1873">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1874">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1875">'Blazor'</span></span>
- <span data-ttu-id="5407e-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1876">'Identity'</span></span>
- <span data-ttu-id="5407e-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1878">'Razor'</span></span>
- <span data-ttu-id="5407e-1879">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1880">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1881">'Blazor'</span></span>
- <span data-ttu-id="5407e-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1882">'Identity'</span></span>
- <span data-ttu-id="5407e-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1884">'Razor'</span></span>
- <span data-ttu-id="5407e-1885">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1886">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1887">'Blazor'</span></span>
- <span data-ttu-id="5407e-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1888">'Identity'</span></span>
- <span data-ttu-id="5407e-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1890">'Razor'</span></span>
- <span data-ttu-id="5407e-1891">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1892">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1893">'Blazor'</span></span>
- <span data-ttu-id="5407e-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1894">'Identity'</span></span>
- <span data-ttu-id="5407e-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1896">'Razor'</span></span>
- <span data-ttu-id="5407e-1897">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1898">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1899">'Blazor'</span></span>
- <span data-ttu-id="5407e-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1900">'Identity'</span></span>
- <span data-ttu-id="5407e-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1902">'Razor'</span></span>
- <span data-ttu-id="5407e-1903">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1904">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1905">'Blazor'</span></span>
- <span data-ttu-id="5407e-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1906">'Identity'</span></span>
- <span data-ttu-id="5407e-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1908">'Razor'</span></span>
- <span data-ttu-id="5407e-1909">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1910">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1911">'Blazor'</span></span>
- <span data-ttu-id="5407e-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1912">'Identity'</span></span>
- <span data-ttu-id="5407e-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1914">'Razor'</span></span>
- <span data-ttu-id="5407e-1915">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1916">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1917">'Blazor'</span></span>
- <span data-ttu-id="5407e-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1918">'Identity'</span></span>
- <span data-ttu-id="5407e-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1920">'Razor'</span></span>
- <span data-ttu-id="5407e-1921">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1922">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1923">'Blazor'</span></span>
- <span data-ttu-id="5407e-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1924">'Identity'</span></span>
- <span data-ttu-id="5407e-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1926">'Razor'</span></span>
- <span data-ttu-id="5407e-1927">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1928">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1929">'Blazor'</span></span>
- <span data-ttu-id="5407e-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1930">'Identity'</span></span>
- <span data-ttu-id="5407e-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1932">'Razor'</span></span>
- <span data-ttu-id="5407e-1933">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1934">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1935">'Blazor'</span></span>
- <span data-ttu-id="5407e-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1936">'Identity'</span></span>
- <span data-ttu-id="5407e-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1938">'Razor'</span></span>
- <span data-ttu-id="5407e-1939">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1940">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1941">'Blazor'</span></span>
- <span data-ttu-id="5407e-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1942">'Identity'</span></span>
- <span data-ttu-id="5407e-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1944">'Razor'</span></span>
- <span data-ttu-id="5407e-1945">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1946">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1947">'Blazor'</span></span>
- <span data-ttu-id="5407e-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1948">'Identity'</span></span>
- <span data-ttu-id="5407e-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1950">'Razor'</span></span>
- <span data-ttu-id="5407e-1951">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1952">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1953">'Blazor'</span></span>
- <span data-ttu-id="5407e-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1954">'Identity'</span></span>
- <span data-ttu-id="5407e-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1956">'Razor'</span></span>
- <span data-ttu-id="5407e-1957">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1958">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1959">'Blazor'</span></span>
- <span data-ttu-id="5407e-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1960">'Identity'</span></span>
- <span data-ttu-id="5407e-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1962">'Razor'</span></span>
- <span data-ttu-id="5407e-1963">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1964">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1965">'Blazor'</span></span>
- <span data-ttu-id="5407e-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1966">'Identity'</span></span>
- <span data-ttu-id="5407e-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1968">'Razor'</span></span>
- <span data-ttu-id="5407e-1969">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1970">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1971">'Blazor'</span></span>
- <span data-ttu-id="5407e-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1972">'Identity'</span></span>
- <span data-ttu-id="5407e-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1974">'Razor'</span></span>
- <span data-ttu-id="5407e-1975">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1976">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1977">'Blazor'</span></span>
- <span data-ttu-id="5407e-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1978">'Identity'</span></span>
- <span data-ttu-id="5407e-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1980">'Razor'</span></span>
- <span data-ttu-id="5407e-1981">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1982">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1983">'Blazor'</span></span>
- <span data-ttu-id="5407e-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1984">'Identity'</span></span>
- <span data-ttu-id="5407e-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1986">'Razor'</span></span>
- <span data-ttu-id="5407e-1987">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1988">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1989">'Blazor'</span></span>
- <span data-ttu-id="5407e-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1990">'Identity'</span></span>
- <span data-ttu-id="5407e-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1992">'Razor'</span></span>
- <span data-ttu-id="5407e-1993">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-1994">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1995">'Blazor'</span></span>
- <span data-ttu-id="5407e-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-1996">'Identity'</span></span>
- <span data-ttu-id="5407e-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-1998">'Razor'</span></span>
- <span data-ttu-id="5407e-1999">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2000">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2001">'Blazor'</span></span>
- <span data-ttu-id="5407e-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2002">'Identity'</span></span>
- <span data-ttu-id="5407e-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2004">'Razor'</span></span>
- <span data-ttu-id="5407e-2005">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2006">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2007">'Blazor'</span></span>
- <span data-ttu-id="5407e-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2008">'Identity'</span></span>
- <span data-ttu-id="5407e-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2010">'Razor'</span></span>
- <span data-ttu-id="5407e-2011">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2012">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2013">'Blazor'</span></span>
- <span data-ttu-id="5407e-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2014">'Identity'</span></span>
- <span data-ttu-id="5407e-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2016">'Razor'</span></span>
- <span data-ttu-id="5407e-2017">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2018">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2019">'Blazor'</span></span>
- <span data-ttu-id="5407e-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2020">'Identity'</span></span>
- <span data-ttu-id="5407e-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2022">'Razor'</span></span>
- <span data-ttu-id="5407e-2023">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2024">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2025">'Blazor'</span></span>
- <span data-ttu-id="5407e-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2026">'Identity'</span></span>
- <span data-ttu-id="5407e-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2028">'Razor'</span></span>
- <span data-ttu-id="5407e-2029">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Запись конфигурации не создана.</span><span class="sxs-lookup"><span data-stu-id="5407e-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="5407e-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-2032">Значение: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`| Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-2033">Значение: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Ключ: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5407e-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5407e-2034">Значение: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="5407e-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="5407e-2035">**Пример**</span><span class="sxs-lookup"><span data-stu-id="5407e-2035">**Example**</span></span>

<span data-ttu-id="5407e-2036">На сервере создается пользовательская переменная среды строки подключения:</span><span class="sxs-lookup"><span data-stu-id="5407e-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="5407e-2037">Имя &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="5407e-2037">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="5407e-2038">Значение &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="5407e-2038">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="5407e-2039">Если `IConfiguration` вставляется и назначается полю с именем `_config`, считайте значение:</span><span class="sxs-lookup"><span data-stu-id="5407e-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="5407e-2040">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-2040">File Configuration Provider</span></span>

<span data-ttu-id="5407e-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> является базовым классом для загрузки конфигурации из файловой системы.</span><span class="sxs-lookup"><span data-stu-id="5407e-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5407e-2042">Следующие поставщики конфигурации предназначены для определенных типов файлов:</span><span class="sxs-lookup"><span data-stu-id="5407e-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5407e-2043">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="5407e-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5407e-2044">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="5407e-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5407e-2045">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="5407e-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5407e-2046">Поставщик конфигурации INI</span><span class="sxs-lookup"><span data-stu-id="5407e-2046">INI Configuration Provider</span></span>

<span data-ttu-id="5407e-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> загружает конфигурацию из пары "ключ — значение" INI-файла во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-2048">Чтобы активировать конфигурацию INI-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5407e-2049">Двоеточие можно использовать как разделитель раздела в конфигурации файла INI.</span><span class="sxs-lookup"><span data-stu-id="5407e-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5407e-2050">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="5407e-2051">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="5407e-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="5407e-2052">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="5407e-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5407e-2053"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="5407e-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5407e-2054">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5407e-2055">Общий пример конфигурации INI-файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="5407e-2056">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5407e-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2057">section0:key0</span></span>
* <span data-ttu-id="5407e-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2058">section0:key1</span></span>
* <span data-ttu-id="5407e-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5407e-2059">section1:subsection:key</span></span>
* <span data-ttu-id="5407e-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5407e-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="5407e-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5407e-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5407e-2062">Поставщик конфигурации JSON</span><span class="sxs-lookup"><span data-stu-id="5407e-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="5407e-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> загружает конфигурацию из пары "ключ — значение" JSON-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5407e-2064">Чтобы активировать конфигурацию JSON-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5407e-2065">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="5407e-2066">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="5407e-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="5407e-2067">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="5407e-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5407e-2068"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="5407e-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5407e-2069">`AddJsonFile` автоматически вызывается дважды при инициализации нового построителя узла с `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5407e-2070">Метод вызывается для загрузки конфигурации из:</span><span class="sxs-lookup"><span data-stu-id="5407e-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5407e-2071">*appsettings.json* &ndash; первым читается этот файл.</span><span class="sxs-lookup"><span data-stu-id="5407e-2071">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5407e-2072">Версия файла среды может переопределить значения, предоставленные *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5407e-2073">*appsettings.{Environment}.json* &ndash; версия среды файла загружается на основе [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="5407e-2073">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5407e-2074">Дополнительные сведения см. в разделе [Конфигурация по умолчанию](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5407e-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5407e-2075">`CreateDefaultBuilder` также загружает следующее:</span><span class="sxs-lookup"><span data-stu-id="5407e-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5407e-2076">Переменные среды.</span><span class="sxs-lookup"><span data-stu-id="5407e-2076">Environment variables.</span></span>
* <span data-ttu-id="5407e-2077">[секреты пользователя (Менеджер секретов)](xref:security/app-secrets) в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5407e-2078">аргументы командной строки.</span><span class="sxs-lookup"><span data-stu-id="5407e-2078">Command-line arguments.</span></span>

<span data-ttu-id="5407e-2079">Сначала устанавливается поставщик конфигурации JSON-файлов.</span><span class="sxs-lookup"><span data-stu-id="5407e-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5407e-2080">Таким образом, секреты пользователя, переменные среды и аргументы командной строки переопределят конфигурацию, заданную файлами *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5407e-2081">Вызовите `ConfigureAppConfiguration` при сборке узла, чтобы указать конфигурацию приложения для файлов, отличных от *appsettings.json* и *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5407e-2082">**Пример**</span><span class="sxs-lookup"><span data-stu-id="5407e-2082">**Example**</span></span>

<span data-ttu-id="5407e-2083">Пример приложения использует преимущества статически удобного метода `CreateDefaultBuilder` для создания узла, который включает два вызова `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="5407e-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="5407e-2084">Первый вызов `AddJsonFile` загружает конфигурацию из *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="5407e-2085">Второй вызов `AddJsonFile` загружает конфигурацию из *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="5407e-2086">Для *appsettings.Development.json* в примере приложения загружается следующий файл:</span><span class="sxs-lookup"><span data-stu-id="5407e-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="5407e-2087">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2087">Run the sample app.</span></span> <span data-ttu-id="5407e-2088">Откройте в приложении браузер с адресом `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5407e-2089">Выходные данные содержат пары "ключ-значение" для конфигурации в зависимости от среды приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="5407e-2090">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Debug` при запуске приложения в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="5407e-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="5407e-2091">Запустите пример приложения еще раз в рабочей среде:</span><span class="sxs-lookup"><span data-stu-id="5407e-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="5407e-2092">Откройте файл *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="5407e-2093">В профиле `ConfigurationSample` измените значение переменной среды `ASPNETCORE_ENVIRONMENT` на `Production`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="5407e-2094">Сохраните файл и запустите приложение с помощью `dotnet run` в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="5407e-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="5407e-2095">Параметры в *appsettings.Development.json* больше не переопределяют параметры в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="5407e-2096">Уровень ведения журнала для ключа `Logging:LogLevel:Default` имеет значение `Warning`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="5407e-2097">Поставщик конфигурации XML</span><span class="sxs-lookup"><span data-stu-id="5407e-2097">XML Configuration Provider</span></span>

<span data-ttu-id="5407e-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> загружает конфигурацию из пары "ключ — значение" XML-файла в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5407e-2099">Чтобы активировать конфигурацию XML-файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5407e-2100">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="5407e-2101">Файл является обязательным или нет.</span><span class="sxs-lookup"><span data-stu-id="5407e-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="5407e-2102">Будет ли перезагружена конфигурация, если файл изменится.</span><span class="sxs-lookup"><span data-stu-id="5407e-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5407e-2103"><xref:Microsoft.Extensions.FileProviders.IFileProvider> используется для доступа к файлу.</span><span class="sxs-lookup"><span data-stu-id="5407e-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5407e-2104">Корневой узел файла конфигурации не учитывается при создании пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5407e-2105">Не указывайте в файле Document Type Definition (определение типа документа, DTD) или пространство имен.</span><span class="sxs-lookup"><span data-stu-id="5407e-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5407e-2106">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5407e-2107">XML-файлы конфигурации могут использовать имена отдельных элементов для повторяющихся разделов.</span><span class="sxs-lookup"><span data-stu-id="5407e-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="5407e-2108">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5407e-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2109">section0:key0</span></span>
* <span data-ttu-id="5407e-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2110">section0:key1</span></span>
* <span data-ttu-id="5407e-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2111">section1:key0</span></span>
* <span data-ttu-id="5407e-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2112">section1:key1</span></span>

<span data-ttu-id="5407e-2113">Повторяющиеся элементы, использующие то же имя элемента, работают, если атрибут `name` используется для различения элементов.</span><span class="sxs-lookup"><span data-stu-id="5407e-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="5407e-2114">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5407e-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="5407e-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="5407e-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="5407e-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2118">section:section1:key:key1</span></span>

<span data-ttu-id="5407e-2119">Атрибуты можно использовать для предоставления значений.</span><span class="sxs-lookup"><span data-stu-id="5407e-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5407e-2120">Предыдущий файл конфигурации загружает следующие ключи с помощью `value`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5407e-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5407e-2121">key:attribute</span></span>
* <span data-ttu-id="5407e-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5407e-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5407e-2123">Поставщик конфигурации ключа для каждого файла</span><span class="sxs-lookup"><span data-stu-id="5407e-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5407e-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> использует файлы каталога как пары "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5407e-2125">Ключ является именем файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-2125">The key is the file name.</span></span> <span data-ttu-id="5407e-2126">Значение содержит содержимое файла.</span><span class="sxs-lookup"><span data-stu-id="5407e-2126">The value contains the file's contents.</span></span> <span data-ttu-id="5407e-2127">Поставщик конфигурации ключа для каждого файла используется в сценариях размещения Docker.</span><span class="sxs-lookup"><span data-stu-id="5407e-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5407e-2128">Чтобы активировать конфигурацию ключа для каждого файла, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5407e-2129">Значение параметра `directoryPath` должно быть абсолютным путем к файлам.</span><span class="sxs-lookup"><span data-stu-id="5407e-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5407e-2130">Перегрузки позволяют указать следующее.</span><span class="sxs-lookup"><span data-stu-id="5407e-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="5407e-2131">`Action<KeyPerFileConfigurationSource>` — делегат, который настраивает источник.</span><span class="sxs-lookup"><span data-stu-id="5407e-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5407e-2132">Обязательно ли указывать каталог и путь к каталогу.</span><span class="sxs-lookup"><span data-stu-id="5407e-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5407e-2133">Двойное подчеркивание (`__`) используется в качестве разделителя ключа конфигурации в именах файлов.</span><span class="sxs-lookup"><span data-stu-id="5407e-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5407e-2134">Например, в имени файла `Logging__LogLevel__System` создается ключ конфигурации `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5407e-2135">Чтобы указать конфигурацию приложения, при сборке веб-узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5407e-2136">Поставщик конфигурации памяти</span><span class="sxs-lookup"><span data-stu-id="5407e-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="5407e-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> использует коллекцию памяти в качестве пар "ключ — значение" конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5407e-2138">Чтобы активировать конфигурацию коллекции в памяти, вызовите метод расширения <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> в экземпляре <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5407e-2139">Поставщик конфигурации может инициализироваться значением `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5407e-2140">Чтобы указать конфигурацию приложения, при сборке узла вызовите `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5407e-2141">В следующем примере создается словарь конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5407e-2142">Словарь используется с вызовом `AddInMemoryCollection` для предоставления конфигурации:</span><span class="sxs-lookup"><span data-stu-id="5407e-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5407e-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="5407e-2143">GetValue</span></span>

<span data-ttu-id="5407e-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) извлекает одно значение из конфигурации с указанным ключом и преобразует его в указанный тип, не являющийся коллекцией.</span><span class="sxs-lookup"><span data-stu-id="5407e-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5407e-2145">Перегрузка принимает значение по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5407e-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="5407e-2146">В следующем примере происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="5407e-2146">The following example:</span></span>

* <span data-ttu-id="5407e-2147">Из конфигурации извлекается строковое значение с ключом `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5407e-2148">Если `NumberKey` отсутствует в ключах конфигурации, используется значение по умолчанию `99`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5407e-2149">Значение получает тип `int`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="5407e-2150">Значение сохраняется в свойстве `NumberConfig` для использования на странице.</span><span class="sxs-lookup"><span data-stu-id="5407e-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5407e-2151">GetSection, GetChildren и Exists</span><span class="sxs-lookup"><span data-stu-id="5407e-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5407e-2152">В следующих примерах рассмотрим файл JSON.</span><span class="sxs-lookup"><span data-stu-id="5407e-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5407e-2153">Четыре ключа находятся в двух разделах, один из которых содержит пару из подразделов.</span><span class="sxs-lookup"><span data-stu-id="5407e-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="5407e-2154">Когда файл считывается в конфигурацию, для сохранения значений конфигурации создаются следующие уникальные иерархические ключи.</span><span class="sxs-lookup"><span data-stu-id="5407e-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5407e-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2155">section0:key0</span></span>
* <span data-ttu-id="5407e-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2156">section0:key1</span></span>
* <span data-ttu-id="5407e-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2157">section1:key0</span></span>
* <span data-ttu-id="5407e-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2158">section1:key1</span></span>
* <span data-ttu-id="5407e-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="5407e-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="5407e-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5407e-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="5407e-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5407e-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5407e-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="5407e-2163">GetSection</span></span>

<span data-ttu-id="5407e-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) извлекает подраздел конфигурации с указанным ключом подраздела.</span><span class="sxs-lookup"><span data-stu-id="5407e-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5407e-2165">Чтобы вернуть <xref:Microsoft.Extensions.Configuration.IConfigurationSection>, содержащий только пары "ключ — значение" в `section1`, вызовите `GetSection` и укажите имя раздела.</span><span class="sxs-lookup"><span data-stu-id="5407e-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5407e-2166">`configSection` не содержит значение, только ключ и путь.</span><span class="sxs-lookup"><span data-stu-id="5407e-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5407e-2167">Аналогично, чтобы получить значения для ключей в `section2:subsection0`, вызовите `GetSection` и укажите путь к разделу.</span><span class="sxs-lookup"><span data-stu-id="5407e-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5407e-2168">Значение `GetSection` никогда не возвращает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5407e-2169">Если соответствующий раздел не найден, возвращается пустой параметр `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5407e-2170">Когда `GetSection` возвращает соответствующий раздел, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> не заполняется.</span><span class="sxs-lookup"><span data-stu-id="5407e-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5407e-2171"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> и <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> возвращаются, если раздел существует.</span><span class="sxs-lookup"><span data-stu-id="5407e-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5407e-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5407e-2172">GetChildren</span></span>

<span data-ttu-id="5407e-2173">Вызов [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) в `section2` получает значение `IEnumerable<IConfigurationSection>`, которое включает:</span><span class="sxs-lookup"><span data-stu-id="5407e-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5407e-2174">Exists</span><span class="sxs-lookup"><span data-stu-id="5407e-2174">Exists</span></span>

<span data-ttu-id="5407e-2175">Используйте [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*), чтобы определить, существует ли раздел конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5407e-2176">Учитывая данные примера, `sectionExists` является `false`, потому что в данных конфигурации нет `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5407e-2177">Привязка к графу объектов</span><span class="sxs-lookup"><span data-stu-id="5407e-2177">Bind to an object graph</span></span>

<span data-ttu-id="5407e-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> способна связывать весь граф объекта POCO.</span><span class="sxs-lookup"><span data-stu-id="5407e-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="5407e-2179">Как и при привязке простого объекта, привязываются только открытые свойства чтения и записи.</span><span class="sxs-lookup"><span data-stu-id="5407e-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="5407e-2180">Образец содержит модель `TvShow`, в графе объектов которого находятся классы `Metadata` и `Actors` (*Модели/TvShow.cs*).</span><span class="sxs-lookup"><span data-stu-id="5407e-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="5407e-2181">В примере приложения есть файл *tvshow.xml*, содержащий данные конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="5407e-2182">Конфигурация привязана ко всему ​​графу объектов `TvShow` с помощью метода `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5407e-2183">Привязанный экземпляр присваивается свойству для подготовки к просмотру.</span><span class="sxs-lookup"><span data-stu-id="5407e-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5407e-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) привязывает и возвращает указанный тип.</span><span class="sxs-lookup"><span data-stu-id="5407e-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5407e-2185">Метод `Get<T>` может быть более удобным, чем использование `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5407e-2186">Следующий код показывает, как использовать `Get<T>` с предыдущим примером:</span><span class="sxs-lookup"><span data-stu-id="5407e-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5407e-2187">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="5407e-2187">Bind an array to a class</span></span>

<span data-ttu-id="5407e-2188">*Пример приложения демонстрирует концепции, описанные в этом разделе.*</span><span class="sxs-lookup"><span data-stu-id="5407e-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5407e-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> поддерживает массивы привязки к объектам с помощью массива индексов в ключах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5407e-2190">Любой формат массива, который предоставляет сегмент числового ключа (`:0:`, `:1:`, &hellip; `:{n}:`), способен привязать массив к массиву класса POCO.</span><span class="sxs-lookup"><span data-stu-id="5407e-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5407e-2191">Привязка предоставляется соглашением.</span><span class="sxs-lookup"><span data-stu-id="5407e-2191">Binding is provided by convention.</span></span> <span data-ttu-id="5407e-2192">Пользовательские поставщики конфигурации не обязаны реализовывать привязку массива.</span><span class="sxs-lookup"><span data-stu-id="5407e-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5407e-2193">**Обработка массива в оперативной памяти**</span><span class="sxs-lookup"><span data-stu-id="5407e-2193">**In-memory array processing**</span></span>

<span data-ttu-id="5407e-2194">Рассмотрите ключи и значения конфигурации, приведенные в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="5407e-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5407e-2195">Ключ</span><span class="sxs-lookup"><span data-stu-id="5407e-2195">Key</span></span>             | <span data-ttu-id="5407e-2196">Значение</span><span class="sxs-lookup"><span data-stu-id="5407e-2196">Value</span></span>  |
| :---
<span data-ttu-id="5407e-2197">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2198">'Blazor'</span></span>
- <span data-ttu-id="5407e-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2199">'Identity'</span></span>
- <span data-ttu-id="5407e-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2201">'Razor'</span></span>
- <span data-ttu-id="5407e-2202">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2203">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2204">'Blazor'</span></span>
- <span data-ttu-id="5407e-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2205">'Identity'</span></span>
- <span data-ttu-id="5407e-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2207">'Razor'</span></span>
- <span data-ttu-id="5407e-2208">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2209">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2210">'Blazor'</span></span>
- <span data-ttu-id="5407e-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2211">'Identity'</span></span>
- <span data-ttu-id="5407e-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2213">'Razor'</span></span>
- <span data-ttu-id="5407e-2214">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2215">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2216">'Blazor'</span></span>
- <span data-ttu-id="5407e-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2217">'Identity'</span></span>
- <span data-ttu-id="5407e-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2219">'Razor'</span></span>
- <span data-ttu-id="5407e-2220">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2221">-------: | :----: | | массив:записи:0 | значение0 | | массив:записи:1 | значение1 | | массив:записи:2 | значение2 | | массив:записи:4 | значение4 | | массив:записи:5 | значение5 |</span><span class="sxs-lookup"><span data-stu-id="5407e-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="5407e-2222">Эти ключи и значения загружаются в пример приложения с помощью поставщика конфигурации памяти.</span><span class="sxs-lookup"><span data-stu-id="5407e-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="5407e-2223">Массив пропускает значения для индекса &num;3.</span><span class="sxs-lookup"><span data-stu-id="5407e-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5407e-2224">Связующее свойство конфигурации не способно связывать нулевые значения или создавать нулевые записи в связанных объектах, что становится ясным в тот момент, когда демонстрируется результат привязки этого массива к объекту.</span><span class="sxs-lookup"><span data-stu-id="5407e-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5407e-2225">В примере приложения класс POCO доступен для хранения привязанных данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="5407e-2226">Данные конфигурации, связанные с объектом.</span><span class="sxs-lookup"><span data-stu-id="5407e-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5407e-2227">Синтаксис [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) также может использоваться для получения более компактного кода:</span><span class="sxs-lookup"><span data-stu-id="5407e-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="5407e-2228">Связанный объект, экземпляр `ArrayExample`, получает данные массива из конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5407e-2229">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5407e-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5407e-2230">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5407e-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="5407e-2231">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2232">'Blazor'</span></span>
- <span data-ttu-id="5407e-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2233">'Identity'</span></span>
- <span data-ttu-id="5407e-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2235">'Razor'</span></span>
- <span data-ttu-id="5407e-2236">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2237">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2238">'Blazor'</span></span>
- <span data-ttu-id="5407e-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2239">'Identity'</span></span>
- <span data-ttu-id="5407e-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2241">'Razor'</span></span>
- <span data-ttu-id="5407e-2242">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2243">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2244">'Blazor'</span></span>
- <span data-ttu-id="5407e-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2245">'Identity'</span></span>
- <span data-ttu-id="5407e-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2247">'Razor'</span></span>
- <span data-ttu-id="5407e-2248">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2249">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2250">'Blazor'</span></span>
- <span data-ttu-id="5407e-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2251">'Identity'</span></span>
- <span data-ttu-id="5407e-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2253">'Razor'</span></span>
- <span data-ttu-id="5407e-2254">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2255">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2256">'Blazor'</span></span>
- <span data-ttu-id="5407e-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2257">'Identity'</span></span>
- <span data-ttu-id="5407e-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2259">'Razor'</span></span>
- <span data-ttu-id="5407e-2260">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2261">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2262">'Blazor'</span></span>
- <span data-ttu-id="5407e-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2263">'Identity'</span></span>
- <span data-ttu-id="5407e-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2265">'Razor'</span></span>
- <span data-ttu-id="5407e-2266">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2267">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2268">'Blazor'</span></span>
- <span data-ttu-id="5407e-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2269">'Identity'</span></span>
- <span data-ttu-id="5407e-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2271">'Razor'</span></span>
- <span data-ttu-id="5407e-2272">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2273">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2274">'Blazor'</span></span>
- <span data-ttu-id="5407e-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2275">'Identity'</span></span>
- <span data-ttu-id="5407e-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2277">'Razor'</span></span>
- <span data-ttu-id="5407e-2278">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2279">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2280">'Blazor'</span></span>
- <span data-ttu-id="5407e-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2281">'Identity'</span></span>
- <span data-ttu-id="5407e-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2283">'Razor'</span></span>
- <span data-ttu-id="5407e-2284">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2285">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2286">'Blazor'</span></span>
- <span data-ttu-id="5407e-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2287">'Identity'</span></span>
- <span data-ttu-id="5407e-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2289">'Razor'</span></span>
- <span data-ttu-id="5407e-2290">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2291">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2292">'Blazor'</span></span>
- <span data-ttu-id="5407e-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2293">'Identity'</span></span>
- <span data-ttu-id="5407e-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2295">'Razor'</span></span>
- <span data-ttu-id="5407e-2296">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2297">-------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2298">'Blazor'</span></span>
- <span data-ttu-id="5407e-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2299">'Identity'</span></span>
- <span data-ttu-id="5407e-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2301">'Razor'</span></span>
- <span data-ttu-id="5407e-2302">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2303">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2304">'Blazor'</span></span>
- <span data-ttu-id="5407e-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2305">'Identity'</span></span>
- <span data-ttu-id="5407e-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2307">'Razor'</span></span>
- <span data-ttu-id="5407e-2308">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2309">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2310">'Blazor'</span></span>
- <span data-ttu-id="5407e-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2311">'Identity'</span></span>
- <span data-ttu-id="5407e-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2313">'Razor'</span></span>
- <span data-ttu-id="5407e-2314">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2315">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2316">'Blazor'</span></span>
- <span data-ttu-id="5407e-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2317">'Identity'</span></span>
- <span data-ttu-id="5407e-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2319">'Razor'</span></span>
- <span data-ttu-id="5407e-2320">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2321">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2322">'Blazor'</span></span>
- <span data-ttu-id="5407e-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2323">'Identity'</span></span>
- <span data-ttu-id="5407e-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2325">'Razor'</span></span>
- <span data-ttu-id="5407e-2326">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2327">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2328">'Blazor'</span></span>
- <span data-ttu-id="5407e-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2329">'Identity'</span></span>
- <span data-ttu-id="5407e-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2331">'Razor'</span></span>
- <span data-ttu-id="5407e-2332">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2333">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2334">'Blazor'</span></span>
- <span data-ttu-id="5407e-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2335">'Identity'</span></span>
- <span data-ttu-id="5407e-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2337">'Razor'</span></span>
- <span data-ttu-id="5407e-2338">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2339">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2340">'Blazor'</span></span>
- <span data-ttu-id="5407e-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2341">'Identity'</span></span>
- <span data-ttu-id="5407e-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2343">'Razor'</span></span>
- <span data-ttu-id="5407e-2344">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2345">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2346">'Blazor'</span></span>
- <span data-ttu-id="5407e-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2347">'Identity'</span></span>
- <span data-ttu-id="5407e-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2349">'Razor'</span></span>
- <span data-ttu-id="5407e-2350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2351">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2352">'Blazor'</span></span>
- <span data-ttu-id="5407e-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2353">'Identity'</span></span>
- <span data-ttu-id="5407e-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2355">'Razor'</span></span>
- <span data-ttu-id="5407e-2356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2357">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2358">'Blazor'</span></span>
- <span data-ttu-id="5407e-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2359">'Identity'</span></span>
- <span data-ttu-id="5407e-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2361">'Razor'</span></span>
- <span data-ttu-id="5407e-2362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2363">-------------: | | 0                            | значение0                       | | 1                            | значение1                       | | 2                            | значение2                       | | 3                            | значение4                       | | 4                            | значение5                       |</span><span class="sxs-lookup"><span data-stu-id="5407e-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="5407e-2364">Индекс &num;3 в связанном объекте содержит данные конфигурации для конфигурационного ключа `array:4` и его значения `value4`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5407e-2365">При привязке данных конфигурации, содержащих массив индексов, в ключах конфигурации эти индексы просто используются для выполнения итерации данных конфигурации при создании объекта.</span><span class="sxs-lookup"><span data-stu-id="5407e-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5407e-2366">Когда массив ключей конфигурации пропускает один или несколько индексов, в данных конфигурации не может быть сохранено нулевое значение и в связанном объекте не создается нулевая запись.</span><span class="sxs-lookup"><span data-stu-id="5407e-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5407e-2367">Отсутствующий элемент конфигурации для индекса &num;3 может быть предоставлен перед привязкой к экземпляру `ArrayExample` любым поставщиком конфигурации, который создает правильную пару "ключ — значение" в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5407e-2368">Если в образец включен дополнительный поставщик конфигурации JSON с отсутствующей парой "ключ — значение", то `ArrayExample.Entries` подойдет полному массиву конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5407e-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5407e-2370">В `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="5407e-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5407e-2371">Пары "ключ — значение", показанные в таблице, загружаются в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5407e-2372">Ключ</span><span class="sxs-lookup"><span data-stu-id="5407e-2372">Key</span></span>             | <span data-ttu-id="5407e-2373">Значение</span><span class="sxs-lookup"><span data-stu-id="5407e-2373">Value</span></span>  |
| :---
<span data-ttu-id="5407e-2374">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2375">'Blazor'</span></span>
- <span data-ttu-id="5407e-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2376">'Identity'</span></span>
- <span data-ttu-id="5407e-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2378">'Razor'</span></span>
- <span data-ttu-id="5407e-2379">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2380">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2381">'Blazor'</span></span>
- <span data-ttu-id="5407e-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2382">'Identity'</span></span>
- <span data-ttu-id="5407e-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2384">'Razor'</span></span>
- <span data-ttu-id="5407e-2385">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2386">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2387">'Blazor'</span></span>
- <span data-ttu-id="5407e-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2388">'Identity'</span></span>
- <span data-ttu-id="5407e-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2390">'Razor'</span></span>
- <span data-ttu-id="5407e-2391">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2392">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2393">'Blazor'</span></span>
- <span data-ttu-id="5407e-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2394">'Identity'</span></span>
- <span data-ttu-id="5407e-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2396">'Razor'</span></span>
- <span data-ttu-id="5407e-2397">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2398">-------: | :----: | | массив:записи:3 | значение3 |</span><span class="sxs-lookup"><span data-stu-id="5407e-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="5407e-2399">Если экземпляр класса `ArrayExample` связан после того, как поставщик конфигурации JSON включает запись для индекса &num;3, то массив `ArrayExample.Entries` включит значение.</span><span class="sxs-lookup"><span data-stu-id="5407e-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5407e-2400">Индекс `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5407e-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5407e-2401">Значение `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="5407e-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="5407e-2402">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2403">'Blazor'</span></span>
- <span data-ttu-id="5407e-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2404">'Identity'</span></span>
- <span data-ttu-id="5407e-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2406">'Razor'</span></span>
- <span data-ttu-id="5407e-2407">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2408">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2409">'Blazor'</span></span>
- <span data-ttu-id="5407e-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2410">'Identity'</span></span>
- <span data-ttu-id="5407e-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2412">'Razor'</span></span>
- <span data-ttu-id="5407e-2413">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2414">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2415">'Blazor'</span></span>
- <span data-ttu-id="5407e-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2416">'Identity'</span></span>
- <span data-ttu-id="5407e-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2418">'Razor'</span></span>
- <span data-ttu-id="5407e-2419">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2420">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2421">'Blazor'</span></span>
- <span data-ttu-id="5407e-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2422">'Identity'</span></span>
- <span data-ttu-id="5407e-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2424">'Razor'</span></span>
- <span data-ttu-id="5407e-2425">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2426">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2427">'Blazor'</span></span>
- <span data-ttu-id="5407e-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2428">'Identity'</span></span>
- <span data-ttu-id="5407e-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2430">'Razor'</span></span>
- <span data-ttu-id="5407e-2431">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2432">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2433">'Blazor'</span></span>
- <span data-ttu-id="5407e-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2434">'Identity'</span></span>
- <span data-ttu-id="5407e-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2436">'Razor'</span></span>
- <span data-ttu-id="5407e-2437">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2438">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2439">'Blazor'</span></span>
- <span data-ttu-id="5407e-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2440">'Identity'</span></span>
- <span data-ttu-id="5407e-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2442">'Razor'</span></span>
- <span data-ttu-id="5407e-2443">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2444">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2445">'Blazor'</span></span>
- <span data-ttu-id="5407e-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2446">'Identity'</span></span>
- <span data-ttu-id="5407e-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2448">'Razor'</span></span>
- <span data-ttu-id="5407e-2449">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2450">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2451">'Blazor'</span></span>
- <span data-ttu-id="5407e-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2452">'Identity'</span></span>
- <span data-ttu-id="5407e-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2454">'Razor'</span></span>
- <span data-ttu-id="5407e-2455">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2456">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2457">'Blazor'</span></span>
- <span data-ttu-id="5407e-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2458">'Identity'</span></span>
- <span data-ttu-id="5407e-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2460">'Razor'</span></span>
- <span data-ttu-id="5407e-2461">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2462">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2463">'Blazor'</span></span>
- <span data-ttu-id="5407e-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2464">'Identity'</span></span>
- <span data-ttu-id="5407e-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2466">'Razor'</span></span>
- <span data-ttu-id="5407e-2467">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2468">-------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2469">'Blazor'</span></span>
- <span data-ttu-id="5407e-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2470">'Identity'</span></span>
- <span data-ttu-id="5407e-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2472">'Razor'</span></span>
- <span data-ttu-id="5407e-2473">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2474">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2475">'Blazor'</span></span>
- <span data-ttu-id="5407e-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2476">'Identity'</span></span>
- <span data-ttu-id="5407e-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2478">'Razor'</span></span>
- <span data-ttu-id="5407e-2479">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2480">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2481">'Blazor'</span></span>
- <span data-ttu-id="5407e-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2482">'Identity'</span></span>
- <span data-ttu-id="5407e-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2484">'Razor'</span></span>
- <span data-ttu-id="5407e-2485">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2486">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2487">'Blazor'</span></span>
- <span data-ttu-id="5407e-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2488">'Identity'</span></span>
- <span data-ttu-id="5407e-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2490">'Razor'</span></span>
- <span data-ttu-id="5407e-2491">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2492">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2493">'Blazor'</span></span>
- <span data-ttu-id="5407e-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2494">'Identity'</span></span>
- <span data-ttu-id="5407e-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2496">'Razor'</span></span>
- <span data-ttu-id="5407e-2497">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2498">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2499">'Blazor'</span></span>
- <span data-ttu-id="5407e-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2500">'Identity'</span></span>
- <span data-ttu-id="5407e-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2502">'Razor'</span></span>
- <span data-ttu-id="5407e-2503">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2504">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2505">'Blazor'</span></span>
- <span data-ttu-id="5407e-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2506">'Identity'</span></span>
- <span data-ttu-id="5407e-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2508">'Razor'</span></span>
- <span data-ttu-id="5407e-2509">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2510">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2511">'Blazor'</span></span>
- <span data-ttu-id="5407e-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2512">'Identity'</span></span>
- <span data-ttu-id="5407e-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2514">'Razor'</span></span>
- <span data-ttu-id="5407e-2515">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2516">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2517">'Blazor'</span></span>
- <span data-ttu-id="5407e-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2518">'Identity'</span></span>
- <span data-ttu-id="5407e-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2520">'Razor'</span></span>
- <span data-ttu-id="5407e-2521">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2522">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2523">'Blazor'</span></span>
- <span data-ttu-id="5407e-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2524">'Identity'</span></span>
- <span data-ttu-id="5407e-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2526">'Razor'</span></span>
- <span data-ttu-id="5407e-2527">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2528">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2529">'Blazor'</span></span>
- <span data-ttu-id="5407e-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2530">'Identity'</span></span>
- <span data-ttu-id="5407e-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2532">'Razor'</span></span>
- <span data-ttu-id="5407e-2533">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2534">-------------: | | 0                            | значение0                       | | 1                            | значение1                       | | 2                            | значение2                       | | 3                            | значение3                       | | 4                            | значение4                       | | 5                            | значение5                       |</span><span class="sxs-lookup"><span data-stu-id="5407e-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="5407e-2535">**Обработка массива JSON**</span><span class="sxs-lookup"><span data-stu-id="5407e-2535">**JSON array processing**</span></span>

<span data-ttu-id="5407e-2536">Если JSON-файл содержит массив, ключи конфигурации будут созданы для элементов массива с индексом раздела, начиная с нуля.</span><span class="sxs-lookup"><span data-stu-id="5407e-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5407e-2537">В следующем файле конфигурации `subsection` — это массив.</span><span class="sxs-lookup"><span data-stu-id="5407e-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="5407e-2538">Поставщик конфигурации JSON считывает данные конфигурации в следующие пары "ключ — значение".</span><span class="sxs-lookup"><span data-stu-id="5407e-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5407e-2539">Ключ</span><span class="sxs-lookup"><span data-stu-id="5407e-2539">Key</span></span>                     | <span data-ttu-id="5407e-2540">Значение</span><span class="sxs-lookup"><span data-stu-id="5407e-2540">Value</span></span>  |
| ---
<span data-ttu-id="5407e-2541">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2542">'Blazor'</span></span>
- <span data-ttu-id="5407e-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2543">'Identity'</span></span>
- <span data-ttu-id="5407e-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2545">'Razor'</span></span>
- <span data-ttu-id="5407e-2546">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2547">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2548">'Blazor'</span></span>
- <span data-ttu-id="5407e-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2549">'Identity'</span></span>
- <span data-ttu-id="5407e-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2551">'Razor'</span></span>
- <span data-ttu-id="5407e-2552">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2553">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2554">'Blazor'</span></span>
- <span data-ttu-id="5407e-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2555">'Identity'</span></span>
- <span data-ttu-id="5407e-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2557">'Razor'</span></span>
- <span data-ttu-id="5407e-2558">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2559">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2560">'Blazor'</span></span>
- <span data-ttu-id="5407e-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2561">'Identity'</span></span>
- <span data-ttu-id="5407e-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2563">'Razor'</span></span>
- <span data-ttu-id="5407e-2564">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2565">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2566">'Blazor'</span></span>
- <span data-ttu-id="5407e-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2567">'Identity'</span></span>
- <span data-ttu-id="5407e-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2569">'Razor'</span></span>
- <span data-ttu-id="5407e-2570">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2571">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2572">'Blazor'</span></span>
- <span data-ttu-id="5407e-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2573">'Identity'</span></span>
- <span data-ttu-id="5407e-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2575">'Razor'</span></span>
- <span data-ttu-id="5407e-2576">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2577">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2578">'Blazor'</span></span>
- <span data-ttu-id="5407e-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2579">'Identity'</span></span>
- <span data-ttu-id="5407e-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2581">'Razor'</span></span>
- <span data-ttu-id="5407e-2582">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2583">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2584">'Blazor'</span></span>
- <span data-ttu-id="5407e-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2585">'Identity'</span></span>
- <span data-ttu-id="5407e-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2587">'Razor'</span></span>
- <span data-ttu-id="5407e-2588">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2589">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2590">'Blazor'</span></span>
- <span data-ttu-id="5407e-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2591">'Identity'</span></span>
- <span data-ttu-id="5407e-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2593">'Razor'</span></span>
- <span data-ttu-id="5407e-2594">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2595">------------ | :----: | | json_array:ключ          | значениеA | | json_array:подраздел:0 | значениеB | | json_array:подраздел:1 | зC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="5407e-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="5407e-2596">В примере приложения для привязки пар "ключ — значение" конфигурации доступен следующий класс POCO.</span><span class="sxs-lookup"><span data-stu-id="5407e-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="5407e-2597">После выполнения привязки `JsonArrayExample.Key` содержит значение `valueA`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5407e-2598">Подраздел значения хранится в свойстве массива POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5407e-2599">Индекс `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="5407e-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5407e-2600">Значение `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="5407e-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="5407e-2601">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2602">'Blazor'</span></span>
- <span data-ttu-id="5407e-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2603">'Identity'</span></span>
- <span data-ttu-id="5407e-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2605">'Razor'</span></span>
- <span data-ttu-id="5407e-2606">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2607">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2608">'Blazor'</span></span>
- <span data-ttu-id="5407e-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2609">'Identity'</span></span>
- <span data-ttu-id="5407e-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2611">'Razor'</span></span>
- <span data-ttu-id="5407e-2612">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2613">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2614">'Blazor'</span></span>
- <span data-ttu-id="5407e-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2615">'Identity'</span></span>
- <span data-ttu-id="5407e-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2617">'Razor'</span></span>
- <span data-ttu-id="5407e-2618">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2619">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2620">'Blazor'</span></span>
- <span data-ttu-id="5407e-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2621">'Identity'</span></span>
- <span data-ttu-id="5407e-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2623">'Razor'</span></span>
- <span data-ttu-id="5407e-2624">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2625">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2626">'Blazor'</span></span>
- <span data-ttu-id="5407e-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2627">'Identity'</span></span>
- <span data-ttu-id="5407e-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2629">'Razor'</span></span>
- <span data-ttu-id="5407e-2630">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2631">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2632">'Blazor'</span></span>
- <span data-ttu-id="5407e-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2633">'Identity'</span></span>
- <span data-ttu-id="5407e-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2635">'Razor'</span></span>
- <span data-ttu-id="5407e-2636">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2637">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2638">'Blazor'</span></span>
- <span data-ttu-id="5407e-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2639">'Identity'</span></span>
- <span data-ttu-id="5407e-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2641">'Razor'</span></span>
- <span data-ttu-id="5407e-2642">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2643">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2644">'Blazor'</span></span>
- <span data-ttu-id="5407e-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2645">'Identity'</span></span>
- <span data-ttu-id="5407e-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2647">'Razor'</span></span>
- <span data-ttu-id="5407e-2648">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2649">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2650">'Blazor'</span></span>
- <span data-ttu-id="5407e-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2651">'Identity'</span></span>
- <span data-ttu-id="5407e-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2653">'Razor'</span></span>
- <span data-ttu-id="5407e-2654">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2655">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2656">'Blazor'</span></span>
- <span data-ttu-id="5407e-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2657">'Identity'</span></span>
- <span data-ttu-id="5407e-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2659">'Razor'</span></span>
- <span data-ttu-id="5407e-2660">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2661">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2662">'Blazor'</span></span>
- <span data-ttu-id="5407e-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2663">'Identity'</span></span>
- <span data-ttu-id="5407e-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2665">'Razor'</span></span>
- <span data-ttu-id="5407e-2666">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2667">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2668">'Blazor'</span></span>
- <span data-ttu-id="5407e-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2669">'Identity'</span></span>
- <span data-ttu-id="5407e-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2671">'Razor'</span></span>
- <span data-ttu-id="5407e-2672">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2673">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2674">'Blazor'</span></span>
- <span data-ttu-id="5407e-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2675">'Identity'</span></span>
- <span data-ttu-id="5407e-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2677">'Razor'</span></span>
- <span data-ttu-id="5407e-2678">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2679">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2680">'Blazor'</span></span>
- <span data-ttu-id="5407e-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2681">'Identity'</span></span>
- <span data-ttu-id="5407e-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2683">'Razor'</span></span>
- <span data-ttu-id="5407e-2684">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2685">-----------------: | :--- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2686">'Blazor'</span></span>
- <span data-ttu-id="5407e-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2687">'Identity'</span></span>
- <span data-ttu-id="5407e-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2689">'Razor'</span></span>
- <span data-ttu-id="5407e-2690">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2691">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2692">'Blazor'</span></span>
- <span data-ttu-id="5407e-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2693">'Identity'</span></span>
- <span data-ttu-id="5407e-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2695">'Razor'</span></span>
- <span data-ttu-id="5407e-2696">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2697">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2698">'Blazor'</span></span>
- <span data-ttu-id="5407e-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2699">'Identity'</span></span>
- <span data-ttu-id="5407e-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2701">'Razor'</span></span>
- <span data-ttu-id="5407e-2702">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2703">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2704">'Blazor'</span></span>
- <span data-ttu-id="5407e-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2705">'Identity'</span></span>
- <span data-ttu-id="5407e-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2707">'Razor'</span></span>
- <span data-ttu-id="5407e-2708">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2709">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2710">'Blazor'</span></span>
- <span data-ttu-id="5407e-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2711">'Identity'</span></span>
- <span data-ttu-id="5407e-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2713">'Razor'</span></span>
- <span data-ttu-id="5407e-2714">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2715">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2716">'Blazor'</span></span>
- <span data-ttu-id="5407e-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2717">'Identity'</span></span>
- <span data-ttu-id="5407e-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2719">'Razor'</span></span>
- <span data-ttu-id="5407e-2720">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2721">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2722">'Blazor'</span></span>
- <span data-ttu-id="5407e-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2723">'Identity'</span></span>
- <span data-ttu-id="5407e-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2725">'Razor'</span></span>
- <span data-ttu-id="5407e-2726">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2727">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2728">'Blazor'</span></span>
- <span data-ttu-id="5407e-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2729">'Identity'</span></span>
- <span data-ttu-id="5407e-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2731">'Razor'</span></span>
- <span data-ttu-id="5407e-2732">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2733">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2734">'Blazor'</span></span>
- <span data-ttu-id="5407e-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2735">'Identity'</span></span>
- <span data-ttu-id="5407e-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2737">'Razor'</span></span>
- <span data-ttu-id="5407e-2738">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2739">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2740">'Blazor'</span></span>
- <span data-ttu-id="5407e-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2741">'Identity'</span></span>
- <span data-ttu-id="5407e-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2743">'Razor'</span></span>
- <span data-ttu-id="5407e-2744">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2745">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2746">'Blazor'</span></span>
- <span data-ttu-id="5407e-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2747">'Identity'</span></span>
- <span data-ttu-id="5407e-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2749">'Razor'</span></span>
- <span data-ttu-id="5407e-2750">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2751">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2752">'Blazor'</span></span>
- <span data-ttu-id="5407e-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2753">'Identity'</span></span>
- <span data-ttu-id="5407e-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2755">'Razor'</span></span>
- <span data-ttu-id="5407e-2756">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2757">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2758">'Blazor'</span></span>
- <span data-ttu-id="5407e-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2759">'Identity'</span></span>
- <span data-ttu-id="5407e-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2761">'Razor'</span></span>
- <span data-ttu-id="5407e-2762">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5407e-2763">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5407e-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5407e-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2764">'Blazor'</span></span>
- <span data-ttu-id="5407e-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5407e-2765">'Identity'</span></span>
- <span data-ttu-id="5407e-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5407e-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="5407e-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5407e-2767">'Razor'</span></span>
- <span data-ttu-id="5407e-2768">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="5407e-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="5407e-2769">-----------------: | | 0                                   | значениеB                              | | 1                                   | значениеC                              | | 2                                   | значениеD                              |</span><span class="sxs-lookup"><span data-stu-id="5407e-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="5407e-2770">Поставщик пользовательской конфигурации</span><span class="sxs-lookup"><span data-stu-id="5407e-2770">Custom configuration provider</span></span>

<span data-ttu-id="5407e-2771">Пример приложения демонстрирует, как создать базовый поставщик конфигурации, который считывает пары "ключ — значение" конфигурации из базы данных, используя [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="5407e-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5407e-2772">Поставщик имеет следующие характеристики.</span><span class="sxs-lookup"><span data-stu-id="5407e-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5407e-2773">База данных в памяти EF используется для демонстрационных целей.</span><span class="sxs-lookup"><span data-stu-id="5407e-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5407e-2774">Чтобы использовать базу данных, для которой требуется строка подключения, выполните вторичный `ConfigurationBuilder`, чтобы предоставить строку подключения от другого поставщика конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5407e-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5407e-2775">Поставщик считывает таблицу базы данных в конфигурации при запуске.</span><span class="sxs-lookup"><span data-stu-id="5407e-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5407e-2776">Поставщик не запрашивает базу данных для каждого ключа.</span><span class="sxs-lookup"><span data-stu-id="5407e-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5407e-2777">Функция перезагрузки на изменение не реализована, поэтому обновление базы данных после запуска приложения не влияет на конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="5407e-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5407e-2778">Определите сущность `EFConfigurationValue` для хранения значений конфигурации в базе данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5407e-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5407e-2780">Добавьте `EFConfigurationContext` в хранилище и обратитесь к настроенным значениям.</span><span class="sxs-lookup"><span data-stu-id="5407e-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5407e-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5407e-2782">Создайте класс, реализующий <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5407e-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5407e-2784">Создайте пользовательский поставщик конфигурации путем наследования от <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5407e-2785">Поставщик конфигурации инициализирует пустую базу данных.</span><span class="sxs-lookup"><span data-stu-id="5407e-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="5407e-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5407e-2787">Метод расширения `AddEFConfiguration` позволяет добавить источник конфигурации к `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5407e-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5407e-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5407e-2789">В следующем коде показано, как использовать пользовательский `EFConfigurationProvider` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="5407e-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5407e-2790">Доступ к конфигурации во время запуска</span><span class="sxs-lookup"><span data-stu-id="5407e-2790">Access configuration during startup</span></span>

<span data-ttu-id="5407e-2791">Внесите значение `IConfiguration` в конструктор `Startup` для доступа к значениям конфигурации в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5407e-2792">Чтобы получить доступ к конфигурации в `Startup.Configure`, либо добавьте значение `IConfiguration` непосредственно в метод, либо используйте экземпляр из конструктора.</span><span class="sxs-lookup"><span data-stu-id="5407e-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="5407e-2793">Дополнительные сведения см. в руководстве по [доступу к конфигурации с использованием удобных методов](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5407e-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5407e-2794">Настройте доступ на странице Razor Pages или в представлении MVC</span><span class="sxs-lookup"><span data-stu-id="5407e-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5407e-2795">Для доступа к параметрам конфигурации на странице Razor Pages или в представлении MVC добавьте [использование директивы](xref:mvc/views/razor#using) ([Справочник по C#: использование директивы](/dotnet/csharp/language-reference/keywords/using-directive)) для [пространства имен Microsoft.Extensions.Configuration](xref:Microsoft.Extensions.Configuration) и вставьте <xref:Microsoft.Extensions.Configuration.IConfiguration> на страницу или в представление.</span><span class="sxs-lookup"><span data-stu-id="5407e-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5407e-2796">На странице Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="5407e-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="5407e-2797">В представлении MVC</span><span class="sxs-lookup"><span data-stu-id="5407e-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5407e-2798">Добавление конфигурации из внешней сборки</span><span class="sxs-lookup"><span data-stu-id="5407e-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="5407e-2799">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> позволяет при запуске добавлять в приложение улучшения из внешней сборки вне приложения класса `Startup`.</span><span class="sxs-lookup"><span data-stu-id="5407e-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5407e-2800">Для получения дополнительной информации см. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5407e-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5407e-2801">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5407e-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
