---
title: Миграция с ASP.NET Core 1.x на 2.0
author: scottaddie
description: В этой статье описываются предварительные требования и стандартные этапы миграции проекта ASP.NET Core 1.x в ASP.NET Core 2.0.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057119"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="88ecd-103">Миграция с ASP.NET Core 1.x на 2.0</span><span class="sxs-lookup"><span data-stu-id="88ecd-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="88ecd-104">Автор: [Скотт Адди](https://github.com/scottaddie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="88ecd-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="88ecd-105">В этой статье поэтапно рассматривается обновление существующего проекта ASP.NET Core 1.x до версии ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="88ecd-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="88ecd-106">Миграция приложения в ASP.NET Core 2.0 позволяет воспользоваться [множеством новых функций и улучшений производительности](xref:aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="88ecd-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="88ecd-107">Существующие приложения ASP.NET Core 1.x основаны на шаблонах проектов для определенной версии.</span><span class="sxs-lookup"><span data-stu-id="88ecd-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="88ecd-108">По мере развития платформы ASP.NET Core совершенствуются шаблоны проектов и содержащийся в них начальный код.</span><span class="sxs-lookup"><span data-stu-id="88ecd-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="88ecd-109">Помимо обновления платформы ASP.NET Core, необходимо также обновить код приложения.</span><span class="sxs-lookup"><span data-stu-id="88ecd-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="88ecd-110">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="88ecd-110">Prerequisites</span></span>

<span data-ttu-id="88ecd-111">См. [Начало работы с ASP.NET Core](xref:getting-started).</span><span class="sxs-lookup"><span data-stu-id="88ecd-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="88ecd-112">Обновление моникера целевой платформы (TFM)</span><span class="sxs-lookup"><span data-stu-id="88ecd-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="88ecd-113">Проекты, предназначенные для .NET Core, должны использовать [моникер целевой платформы](/dotnet/standard/frameworks) версии не ниже .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="88ecd-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="88ecd-114">Найдите в файле *CSPROJ* узел `<TargetFramework>` и замените его содержимое на `netcoreapp2.0`:</span><span class="sxs-lookup"><span data-stu-id="88ecd-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="88ecd-115">Проекты, предназначенные для .NET Framework, должны использовать моникер целевой платформы версии не ниже .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="88ecd-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="88ecd-116">Найдите в файле *CSPROJ* узел `<TargetFramework>` и замените его содержимое на `net461`:</span><span class="sxs-lookup"><span data-stu-id="88ecd-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="88ecd-117">.NET Core 2.0 обеспечивает гораздо большую контактную зону по сравнению с .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="88ecd-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="88ecd-118">Если вы используете .NET Framework только из-за отсутствия нужных API в .NET Core 1.x, скорее всего, .NET Core 2.0 удовлетворит ваши потребности.</span><span class="sxs-lookup"><span data-stu-id="88ecd-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="88ecd-119">Если файл проекта содержит `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, см. [эту проблему на GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span><span class="sxs-lookup"><span data-stu-id="88ecd-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="88ecd-120">Обновление версии пакета SDK для .NET Core в файле global.json</span><span class="sxs-lookup"><span data-stu-id="88ecd-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="88ecd-121">Если ваше решение использует файл [global.json](/dotnet/core/tools/global-json) для указания целевой версии пакета SDK для .NET Core, измените значение свойства `version` так, чтобы использовалась версия 2.0, установленная на компьютере:</span><span class="sxs-lookup"><span data-stu-id="88ecd-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="88ecd-122">Обновление ссылок на пакеты</span><span class="sxs-lookup"><span data-stu-id="88ecd-122">Update package references</span></span>

<span data-ttu-id="88ecd-123">В файле *CSPROJ* в проекте версии 1.x перечислены все проекты NuGet, используемые проектом.</span><span class="sxs-lookup"><span data-stu-id="88ecd-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="88ecd-124">В проекте ASP.NET Core 2.0, предназначенном для .NET Core 2.0, коллекция пакетов в файле *CSPROJ* заменяется ссылкой на один [метапакет](xref:fundamentals/metapackage):</span><span class="sxs-lookup"><span data-stu-id="88ecd-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="88ecd-125">В метапакет входят все компоненты ASP.NET Core 2.0 и Entity Framework Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="88ecd-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="88ecd-126">В проектах ASP.NET Core 2.0, предназначенных для .NET Framework, по-прежнему должны использоваться ссылки на отдельные пакеты NuGet.</span><span class="sxs-lookup"><span data-stu-id="88ecd-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="88ecd-127">Измените значение атрибута `Version` каждого узла `<PackageReference />` на 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="88ecd-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="88ecd-128">Например, вот список узлов `<PackageReference />`, используемых в типичном проекте ASP.NET Core 2.0, предназначенном для .NET Framework:</span><span class="sxs-lookup"><span data-stu-id="88ecd-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="88ecd-129">Обновление средств CLI для .NET Core</span><span class="sxs-lookup"><span data-stu-id="88ecd-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="88ecd-130">Измените значение атрибута *каждого узла* в файле `Version`CSPROJ`<DotNetCliToolReference />` на 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="88ecd-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="88ecd-131">Например, вот список средств CLI, используемых в типичном проекте ASP.NET Core 2.0, предназначенном для .NET Core 2.0:</span><span class="sxs-lookup"><span data-stu-id="88ecd-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="88ecd-132">Переименование свойства PackageTargetFallback</span><span class="sxs-lookup"><span data-stu-id="88ecd-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="88ecd-133">В проектах версии 1.x в файлах *CSPROJ* использовался узел `PackageTargetFallback` и переменная:</span><span class="sxs-lookup"><span data-stu-id="88ecd-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="88ecd-134">Переименуйте этот узел и переменную в `AssetTargetFallback`:</span><span class="sxs-lookup"><span data-stu-id="88ecd-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="88ecd-135">Обновление метода Main в файле Program.cs</span><span class="sxs-lookup"><span data-stu-id="88ecd-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="88ecd-136">В проектах версии 1.x метод `Main` в файле *Program.cs* выглядел так:</span><span class="sxs-lookup"><span data-stu-id="88ecd-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="88ecd-137">В проектах версии 2.0 метод `Main` в файле *Program.cs* упрощен:</span><span class="sxs-lookup"><span data-stu-id="88ecd-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="88ecd-138">Внедрение нового шаблона версии 2.0 настоятельно рекомендуется; оно является обязательным для использования [миграций Entity Framework (EF) Core](xref:data/ef-mvc/migrations) и ряда других функций продукта.</span><span class="sxs-lookup"><span data-stu-id="88ecd-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="88ecd-139">Например, при выполнении команды `Update-Database` из консоли диспетчера пакетов или команды `dotnet ef database update` в командной строке (для проектов, преобразованных в ASP.NET Core 2.0) возникает следующая ошибка:</span><span class="sxs-lookup"><span data-stu-id="88ecd-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="88ecd-140">Все поставщики конфигурации</span><span class="sxs-lookup"><span data-stu-id="88ecd-140">Add configuration providers</span></span>

<span data-ttu-id="88ecd-141">В проектах 1.x поставщики конфигурации добавлялись в приложение с помощью конструктора `Startup`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="88ecd-142">Для этого нужно было создать экземпляр `ConfigurationBuilder`, загрузить применимые поставщики (переменные сред, параметры приложений и т. д.) и инициализировать член `IConfigurationRoot`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="88ecd-143">В примере выше происходит загрузка элемента `Configuration` с параметрами конфигурации из файла *appsettings.json* и любого файла *appsettings.\<EnvironmentName\>.json* , соответствующего свойству `IHostingEnvironment.EnvironmentName`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="88ecd-144">Эти файлы располагаются по тому же пути, что и *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="88ecd-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="88ecd-145">В проектах 2.0 стереотипный код конфигурации из проектов 1.x запускается "за кулисами".</span><span class="sxs-lookup"><span data-stu-id="88ecd-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="88ecd-146">Например, переменные сред и параметры приложений загружаются при запуске.</span><span class="sxs-lookup"><span data-stu-id="88ecd-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="88ecd-147">Эквивалентный код *Startup.cs* сокращается до инициализации `IConfiguration` с внедрением экземпляра.</span><span class="sxs-lookup"><span data-stu-id="88ecd-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="88ecd-148">Чтобы удалить добавляемых `WebHostBuilder.CreateDefaultBuilder` поставщиков по умолчанию, вызовите метод `Clear` для свойства `IConfigurationBuilder.Sources` внутри `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="88ecd-149">Чтобы снова добавить поставщиков, используйте метод `ConfigureAppConfiguration` в *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="88ecd-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="88ecd-150">Конфигурацию, используемую в предыдущем фрагменте кода методом `CreateDefaultBuilder`, можно посмотреть [здесь](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="88ecd-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="88ecd-151">Дополнительные сведения см. в разделе [Конфигурация в ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="88ecd-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="88ecd-152">Перенос кода инициализации базы данных</span><span class="sxs-lookup"><span data-stu-id="88ecd-152">Move database initialization code</span></span>

<span data-ttu-id="88ecd-153">В проектах 1.x, где используется EF Core 1.x, такая команда, как `dotnet ef migrations add`, делает следующее.</span><span class="sxs-lookup"><span data-stu-id="88ecd-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="88ecd-154">Создает экземпляр `Startup`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="88ecd-155">Вызывает метод `ConfigureServices`, чтобы зарегистрировать все службы с использованием вставки зависимостей (включая типы `DbContext`).</span><span class="sxs-lookup"><span data-stu-id="88ecd-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="88ecd-156">Выполняет свои требуемые задачи.</span><span class="sxs-lookup"><span data-stu-id="88ecd-156">Performs its requisite tasks</span></span>

<span data-ttu-id="88ecd-157">В проектах 2.0, где используется EF Core 2.0, вызывается `Program.BuildWebHost`, чтобы получить службы приложений.</span><span class="sxs-lookup"><span data-stu-id="88ecd-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="88ecd-158">В отличие от 1.x, это также приводит к вызову `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="88ecd-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="88ecd-159">Если ваше приложение 1.x вызвало код инициализации базы данных в своем методе `Configure`, могут возникнуть непредвиденные проблемы.</span><span class="sxs-lookup"><span data-stu-id="88ecd-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="88ecd-160">Например, если база данных еще не существует, код заполнения запускается до выполнения команды миграции EF Core.</span><span class="sxs-lookup"><span data-stu-id="88ecd-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="88ecd-161">Из-за этой проблемы, если база данных еще не существует, команда `dotnet ef migrations list` не срабатывает.</span><span class="sxs-lookup"><span data-stu-id="88ecd-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="88ecd-162">В качестве примера из версии 1.x возьмем следующий код инициализации заполнения в методе `Configure` из *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="88ecd-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="88ecd-163">В проектах 2.0 поместите вызов `SeedData.Initialize` в метод `Main` из *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="88ecd-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="88ecd-164">Начиная с версии 2.0, делать в `BuildWebHost` что-либо помимо сборки и настройки веб-узла не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="88ecd-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="88ecd-165">Все, что касается работы приложения, должно обрабатываться вне `BuildWebHost` &mdash; обычно в методе `Main` из *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="88ecd-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a><span data-ttu-id="88ecd-166">Проверка параметра компиляции представлений Razor</span><span class="sxs-lookup"><span data-stu-id="88ecd-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="88ecd-167">Сокращение времени запуска приложений и уменьшение размеров публикуемых пакетов крайне важны.</span><span class="sxs-lookup"><span data-stu-id="88ecd-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="88ecd-168">По этой причине в ASP.NET Core 2.0 по умолчанию включена [компиляция представлений Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="88ecd-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="88ecd-169">Присваивать свойству `MvcRazorCompileOnPublish` значение true больше не нужно.</span><span class="sxs-lookup"><span data-stu-id="88ecd-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="88ecd-170">Если вы не собираетесь отключать компиляцию представлений, это свойство можно удалить из файла *CSPROJ*.</span><span class="sxs-lookup"><span data-stu-id="88ecd-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="88ecd-171">Если проект предназначен для .NET Framework, необходимо по-прежнему явно указывать ссылку на пакет NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) в файле *CSPROJ* :</span><span class="sxs-lookup"><span data-stu-id="88ecd-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="88ecd-172">Использование подсветки функций Application Insights</span><span class="sxs-lookup"><span data-stu-id="88ecd-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="88ecd-173">Простота настройки инструментария для обеспечения производительности приложений имеет большое значение.</span><span class="sxs-lookup"><span data-stu-id="88ecd-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="88ecd-174">Теперь вам доступны новые функции подготовки [Application Insights](/azure/application-insights/app-insights-overview) в составе средств Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="88ecd-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="88ecd-175">При создании проектов ASP.NET Core 1.1 в Visual Studio 2017 служба Application Insights добавлялась по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="88ecd-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="88ecd-176">Если вы не используете пакет SDK для Application Insights напрямую вне файлов *Program.cs* и *Startup.cs* , выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="88ecd-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs* , follow these steps:</span></span>

1. <span data-ttu-id="88ecd-177">Для работы с .NET Core удалите из файла *CSPROJ* следующий узел `<PackageReference />`:</span><span class="sxs-lookup"><span data-stu-id="88ecd-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="88ecd-178">Для работы с .NET Core удалите вызов метода расширения `UseApplicationInsights` из файла *Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="88ecd-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs* :</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="88ecd-179">Удалите вызов API на стороне клиента Application Insights из файла *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="88ecd-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="88ecd-180">Этот вызов включает две строки кода:</span><span class="sxs-lookup"><span data-stu-id="88ecd-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="88ecd-181">Если вы используете пакет SDK для Application Insights напрямую, продолжайте делать это.</span><span class="sxs-lookup"><span data-stu-id="88ecd-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="88ecd-182">[Метапакет](xref:fundamentals/metapackage) версии 2.0 включает в себя последнюю версию Application Insights, поэтому при попытке сослаться на более старую версию возникает ошибка понижения уровня пакета.</span><span class="sxs-lookup"><span data-stu-id="88ecd-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a><span data-ttu-id="88ecd-183">Внедрение улучшений проверки подлинности или Identity</span><span class="sxs-lookup"><span data-stu-id="88ecd-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="88ecd-184">В ASP.NET Core 2.0 реализована новая модель аутентификации и внесен ряд важных изменений в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="88ecd-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="88ecd-185">Если при создании проекта вы включили отдельные учетные записи пользователей либо вручную добавили проверку подлинности или Identity, см. сведения на странице [Миграция на другой метод проверки подлинности и Identity в ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span><span class="sxs-lookup"><span data-stu-id="88ecd-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88ecd-186">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="88ecd-186">Additional resources</span></span>

* [<span data-ttu-id="88ecd-187">Критические изменения в ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="88ecd-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
