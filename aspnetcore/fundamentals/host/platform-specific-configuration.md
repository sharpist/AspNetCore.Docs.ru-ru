---
<span data-ttu-id="f0259-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="f0259-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f0259-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f0259-102">'Blazor'</span></span>
- <span data-ttu-id="f0259-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f0259-103">'Identity'</span></span>
- <span data-ttu-id="f0259-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f0259-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f0259-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f0259-105">'Razor'</span></span>
- <span data-ttu-id="f0259-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="f0259-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="f0259-107">Использование начальных сборок размещения в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0259-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="f0259-108">Автор: [Павел Крымец](https://github.com/pakrym) (Pavel Krymets)</span><span class="sxs-lookup"><span data-stu-id="f0259-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0259-109">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f0259-110">Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f0259-111">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f0259-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f0259-112">Атрибут HostingStartup</span><span class="sxs-lookup"><span data-stu-id="f0259-112">HostingStartup attribute</span></span>

<span data-ttu-id="f0259-113">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f0259-114">Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-115">Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0259-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f0259-116">Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0259-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="f0259-117">В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0259-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f0259-118">Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0259-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0259-119">Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f0259-120">Обнаружение загруженных начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f0259-121">Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f0259-122">В журнал вносятся ошибки, возникающие при загрузке сборок.</span><span class="sxs-lookup"><span data-stu-id="f0259-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f0259-123">Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.</span><span class="sxs-lookup"><span data-stu-id="f0259-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f0259-124">Отключение автоматической загрузки начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f0259-125">Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="f0259-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f0259-126">Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f0259-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="f0259-127">Параметр конфигурации узла "Запретить размещение при запуске".</span><span class="sxs-lookup"><span data-stu-id="f0259-127">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="f0259-128">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="f0259-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="f0259-129">Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f0259-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="f0259-130">Параметр конфигурации узла "Исключаемые сборки размещения при запуске".</span><span class="sxs-lookup"><span data-stu-id="f0259-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="f0259-131">Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f0259-132">Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.</span><span class="sxs-lookup"><span data-stu-id="f0259-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f0259-133">При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f0259-134">Проект</span><span class="sxs-lookup"><span data-stu-id="f0259-134">Project</span></span>

<span data-ttu-id="f0259-135">Создайте размещение при запуске для любого из указанных ниже типов проектов:</span><span class="sxs-lookup"><span data-stu-id="f0259-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f0259-136">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="f0259-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="f0259-137">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="f0259-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f0259-138">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="f0259-138">Class library</span></span>

<span data-ttu-id="f0259-139">Расширение размещения при запуске можно указать в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="f0259-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f0259-140">Библиотека содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0259-141">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f0259-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f0259-142">Библиотека классов:</span><span class="sxs-lookup"><span data-stu-id="f0259-142">The class library:</span></span>

* <span data-ttu-id="f0259-143">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-144">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f0259-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f0259-145">Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f0259-146">Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f0259-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0259-148">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:</span><span class="sxs-lookup"><span data-stu-id="f0259-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f0259-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f0259-150">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f0259-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f0259-151">Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее.</span><span class="sxs-lookup"><span data-stu-id="f0259-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f0259-152">Пакет:</span><span class="sxs-lookup"><span data-stu-id="f0259-152">The package:</span></span>

* <span data-ttu-id="f0259-153">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-154">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f0259-155">Включает атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0259-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0259-157">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:</span><span class="sxs-lookup"><span data-stu-id="f0259-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f0259-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f0259-159">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="f0259-159">Console app without an entry point</span></span>

<span data-ttu-id="f0259-160">*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f0259-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f0259-161">Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-162">При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f0259-163">В этом процессе используется консольное приложение без точки входа, так как:</span><span class="sxs-lookup"><span data-stu-id="f0259-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f0259-164">Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f0259-165">Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.</span><span class="sxs-lookup"><span data-stu-id="f0259-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f0259-166">Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f0259-167">В ходе создания динамического размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f0259-168">Начальная сборка размещения создается из консольного приложения без точки входа, которое:</span><span class="sxs-lookup"><span data-stu-id="f0259-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f0259-169">содержит класс с реализацией `IHostingStartup`;</span><span class="sxs-lookup"><span data-stu-id="f0259-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f0259-170">содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f0259-171">Консольное приложение публикуется для получения зависимостей начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="f0259-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f0259-172">После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f0259-173">Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f0259-174">Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f0259-175">Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.</span><span class="sxs-lookup"><span data-stu-id="f0259-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f0259-176">Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="f0259-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="f0259-177">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="f0259-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f0259-178">В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0259-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0259-179">Класс реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-180">Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f0259-181">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f0259-182">При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:</span><span class="sxs-lookup"><span data-stu-id="f0259-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f0259-183">Показана только часть файла.</span><span class="sxs-lookup"><span data-stu-id="f0259-183">Only part of the file is shown.</span></span> <span data-ttu-id="f0259-184">Имя сборки в примере — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0259-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f0259-185">Конфигурация, предоставляемая размещением при запуске</span><span class="sxs-lookup"><span data-stu-id="f0259-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f0259-186">Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:</span><span class="sxs-lookup"><span data-stu-id="f0259-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f0259-187">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="f0259-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0259-188">При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f0259-189">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="f0259-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0259-190">При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f0259-191">Указание начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f0259-192">Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f0259-193">Переменная среды — это список сборок, разделенный точками с запятой.</span><span class="sxs-lookup"><span data-stu-id="f0259-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f0259-194">Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-195">Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:</span><span class="sxs-lookup"><span data-stu-id="f0259-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f0259-196">Начальную сборку размещения также можно задать с помощью параметра конфигурации узла "Начальные сборки размещения".</span><span class="sxs-lookup"><span data-stu-id="f0259-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="f0259-197">При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.</span><span class="sxs-lookup"><span data-stu-id="f0259-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f0259-198">Активация</span><span class="sxs-lookup"><span data-stu-id="f0259-198">Activation</span></span>

<span data-ttu-id="f0259-199">Ниже приведены варианты активации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f0259-200">[Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="f0259-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f0259-201">Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="f0259-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f0259-202">Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f0259-203">Ссылки во время компиляции, необходимые для активации</span><span class="sxs-lookup"><span data-stu-id="f0259-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f0259-204">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="f0259-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f0259-205">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="f0259-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f0259-206">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="f0259-206">Runtime store</span></span>

<span data-ttu-id="f0259-207">Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f0259-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f0259-208">Ссылка на сборку во время компиляции не требуется расширенному приложению.</span><span class="sxs-lookup"><span data-stu-id="f0259-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f0259-209">После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="f0259-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f0259-210">В примере приложения (проект *RuntimeStore*) используется такая команда:</span><span class="sxs-lookup"><span data-stu-id="f0259-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f0259-211">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0259-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f0259-212">**Изменение и размещение файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="f0259-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f0259-213">Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="f0259-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f0259-214">`additionalDeps` предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="f0259-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f0259-215">Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f0259-216">Обнаруживать и скачивать начальную сборку размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f0259-217">Рекомендуемые действия при создании файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f0259-218">Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="f0259-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f0259-219">Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f0259-220">В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.</span><span class="sxs-lookup"><span data-stu-id="f0259-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="f0259-221">Поместите файл *.deps.json* в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="f0259-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f0259-222">`{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f0259-223">`{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f0259-224">`{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="f0259-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f0259-225">`{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.</span><span class="sxs-lookup"><span data-stu-id="f0259-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f0259-226">В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="f0259-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f0259-227">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f0259-228">В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="f0259-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f0259-229">Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f0259-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f0259-230">**Развертывание**</span><span class="sxs-lookup"><span data-stu-id="f0259-230">**Deployment**</span></span>

<span data-ttu-id="f0259-231">Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:</span><span class="sxs-lookup"><span data-stu-id="f0259-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f0259-232">Хранилище среды выполнения размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f0259-233">Файл зависимостей размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f0259-234">Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f0259-235">Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.</span><span class="sxs-lookup"><span data-stu-id="f0259-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f0259-236">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="f0259-236">NuGet package</span></span>

<span data-ttu-id="f0259-237">Расширение размещения при запуске можно указать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="f0259-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f0259-238">Пакет содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-239">Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="f0259-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f0259-240">Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f0259-241">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="f0259-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0259-242">Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f0259-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f0259-243">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f0259-244">Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:</span><span class="sxs-lookup"><span data-stu-id="f0259-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f0259-245">Создание пакета NuGet с помощью кроссплатформенных средств</span><span class="sxs-lookup"><span data-stu-id="f0259-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f0259-246">Публикация пакетов</span><span class="sxs-lookup"><span data-stu-id="f0259-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f0259-247">Хранилище пакетов среды выполнения</span><span class="sxs-lookup"><span data-stu-id="f0259-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f0259-248">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="f0259-248">Project bin folder</span></span>

<span data-ttu-id="f0259-249">Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f0259-250">Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="f0259-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f0259-251">Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f0259-252">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="f0259-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0259-253">Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:</span><span class="sxs-lookup"><span data-stu-id="f0259-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f0259-254">Проект, который будет использовать ее.</span><span class="sxs-lookup"><span data-stu-id="f0259-254">The consuming project.</span></span>
  * <span data-ttu-id="f0259-255">Расположение, доступное использующему проекту.</span><span class="sxs-lookup"><span data-stu-id="f0259-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f0259-256">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f0259-257">При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="f0259-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f0259-258">Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="f0259-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f0259-259">Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0259-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f0259-260">Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0259-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f0259-261">Пример кода</span><span class="sxs-lookup"><span data-stu-id="f0259-261">Sample code</span></span>

<span data-ttu-id="f0259-262">В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f0259-263">В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:</span><span class="sxs-lookup"><span data-stu-id="f0259-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f0259-264">Пакет NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f0259-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f0259-265">Библиотека классов (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f0259-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f0259-266">Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="f0259-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f0259-267">Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:</span><span class="sxs-lookup"><span data-stu-id="f0259-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f0259-268">Зарегистрированные службы</span><span class="sxs-lookup"><span data-stu-id="f0259-268">Registered services</span></span>
  * <span data-ttu-id="f0259-269">Адрес (схема, узел, базовый путь, путь, строка запроса)</span><span class="sxs-lookup"><span data-stu-id="f0259-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f0259-270">Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)</span><span class="sxs-lookup"><span data-stu-id="f0259-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f0259-271">Заголовки запросов</span><span class="sxs-lookup"><span data-stu-id="f0259-271">Request headers</span></span>
  * <span data-ttu-id="f0259-272">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f0259-272">Environment variables</span></span>

<span data-ttu-id="f0259-273">Для выполнения образца:</span><span class="sxs-lookup"><span data-stu-id="f0259-273">To run the sample:</span></span>

<span data-ttu-id="f0259-274">**Активация из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="f0259-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f0259-275">Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="f0259-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f0259-276">Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0259-277">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-277">Compile and run the app.</span></span> <span data-ttu-id="f0259-278">Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f0259-279">Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета.</span><span class="sxs-lookup"><span data-stu-id="f0259-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f0259-280">Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0259-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f0259-281">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f0259-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0259-282">Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша.</span><span class="sxs-lookup"><span data-stu-id="f0259-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f0259-283">Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="f0259-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f0259-284">**Активации из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="f0259-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="f0259-285">Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="f0259-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f0259-286">Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0259-287">Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f0259-288">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-288">Compile and run the app.</span></span> <span data-ttu-id="f0259-289">`<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f0259-290">Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0259-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f0259-291">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f0259-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0259-292">**Активация из сборки среды выполнения, развернутой в хранилище**</span><span class="sxs-lookup"><span data-stu-id="f0259-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f0259-293">В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f0259-294">PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1).</span><span class="sxs-lookup"><span data-stu-id="f0259-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f0259-295">Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f0259-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f0259-296">Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="f0259-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f0259-297">Скрипт выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="f0259-297">The script:</span></span>
   * <span data-ttu-id="f0259-298">Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="f0259-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f0259-299">Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*.</span><span class="sxs-lookup"><span data-stu-id="f0259-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f0259-300">Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="f0259-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f0259-301">При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта.</span><span class="sxs-lookup"><span data-stu-id="f0259-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f0259-302">Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка.</span><span class="sxs-lookup"><span data-stu-id="f0259-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f0259-303">Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="f0259-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f0259-304">Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="f0259-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f0259-305">Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы.</span><span class="sxs-lookup"><span data-stu-id="f0259-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f0259-306">Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f0259-307">Размещает файл *deploy.ps1* в папке для *развертывания*.</span><span class="sxs-lookup"><span data-stu-id="f0259-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f0259-308">Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*.</span><span class="sxs-lookup"><span data-stu-id="f0259-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f0259-309">Скрипт присоединяет:</span><span class="sxs-lookup"><span data-stu-id="f0259-309">The script appends:</span></span>
   * <span data-ttu-id="f0259-310">`StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f0259-311">Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f0259-312">Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0259-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f0259-313">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-313">Run the sample app.</span></span>
1. <span data-ttu-id="f0259-314">Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f0259-315">Запросите конечную точку `/diag` для просмотра диагностических сведений.</span><span class="sxs-lookup"><span data-stu-id="f0259-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0259-316">Реализация <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (размещение при запуске) позволяет добавлять в приложение улучшения из внешней сборки при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="f0259-317">Например, внешняя библиотека может использовать реализацию размещения при запуске, чтобы доставить дополнительные поставщики конфигурации или службы для приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="f0259-318">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f0259-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="f0259-319">Атрибут HostingStartup</span><span class="sxs-lookup"><span data-stu-id="f0259-319">HostingStartup attribute</span></span>

<span data-ttu-id="f0259-320">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) указывает на наличие начальной сборки размещения, которая будет активирована во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="f0259-321">Входная сборка или сборка, содержащая класс `Startup`, автоматически сканируется на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-322">Список сборок, в котором будет выполняться поиск атрибута `HostingStartup`, загружается во время выполнения из конфигурации в [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0259-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="f0259-323">Список сборок, которые необходимо исключить из обнаружения, загружается из [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="f0259-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="f0259-324">Дополнительные сведения см. в руководстве по [настройке Размещение начальных сборок](xref:fundamentals/host/web-host#hosting-startup-assemblies) и [Веб-узел: исключаемые начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f0259-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="f0259-325">В следующем примере пространство имен для начальной сборки размещения — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0259-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="f0259-326">Класс, содержащий код запуска размещения, — `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0259-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0259-327">Атрибут `HostingStartup` обычно находится в файле класса реализации начальной сборки размещения `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="f0259-328">Обнаружение загруженных начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="f0259-329">Для обнаружения загруженных сборок размещения при запуске включите ведение журнала и проверьте журналы приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="f0259-330">В журнал вносятся ошибки, возникающие при загрузке сборок.</span><span class="sxs-lookup"><span data-stu-id="f0259-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="f0259-331">Загруженные начальные сборки размещения регистрируются на уровне отладки, также регистрируются все ошибки.</span><span class="sxs-lookup"><span data-stu-id="f0259-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="f0259-332">Отключение автоматической загрузки начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="f0259-333">Чтобы отключить автоматическую загрузку начальных сборок размещения, используйте один из следующих подходов:</span><span class="sxs-lookup"><span data-stu-id="f0259-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="f0259-334">Чтобы предотвратить загрузку всех начальных сборок размещения, установите значение `true` или `1` для одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f0259-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="f0259-335">Параметр конфигурации узла [Запретить размещение при запуске](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="f0259-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="f0259-336">Переменная среды `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="f0259-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="f0259-337">Чтобы предотвратить загрузку конкретных сборок размещения, установите строку, содержащую разделенный точками с запятой список сборок размещения, которые необходимо исключить при запуске, в качестве значения одного из следующих параметров:</span><span class="sxs-lookup"><span data-stu-id="f0259-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="f0259-338">Параметр конфигурации узла [Исключаемые сборки размещения при запуске](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f0259-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="f0259-339">Переменная среды `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="f0259-340">Если заданы параметры конфигурации узла и переменная среды, на поведение влияют параметры узла.</span><span class="sxs-lookup"><span data-stu-id="f0259-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="f0259-341">При отключении сборок размещения при запуске с использованием параметра узла или переменной среды сборка отключается на глобальном уровне, и также могут быть отключены некоторые характеристики приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="f0259-342">Проект</span><span class="sxs-lookup"><span data-stu-id="f0259-342">Project</span></span>

<span data-ttu-id="f0259-343">Создайте размещение при запуске для любого из указанных ниже типов проектов:</span><span class="sxs-lookup"><span data-stu-id="f0259-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="f0259-344">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="f0259-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="f0259-345">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="f0259-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="f0259-346">Библиотека классов</span><span class="sxs-lookup"><span data-stu-id="f0259-346">Class library</span></span>

<span data-ttu-id="f0259-347">Расширение размещения при запуске можно указать в библиотеке классов.</span><span class="sxs-lookup"><span data-stu-id="f0259-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="f0259-348">Библиотека содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0259-349">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) включает приложение Razor Pages *HostingStartupApp* и библиотеку классов *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="f0259-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="f0259-350">Библиотека классов:</span><span class="sxs-lookup"><span data-stu-id="f0259-350">The class library:</span></span>

* <span data-ttu-id="f0259-351">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-352">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения с помощью поставщика конфигурации, размещаемой в памяти ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="f0259-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="f0259-353">Включает атрибут `HostingStartup`, определяющий пространство имен и класс размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="f0259-354">Метод `ServiceKeyInjection` класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="f0259-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0259-356">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения библиотеки класса:</span><span class="sxs-lookup"><span data-stu-id="f0259-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="f0259-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="f0259-358">[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) также включает проект пакета NuGet, предоставляющий отдельное размещение при запуске *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="f0259-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="f0259-359">Характеристики пакета аналогичны характеристикам библиотеки классов, приведенным ранее.</span><span class="sxs-lookup"><span data-stu-id="f0259-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="f0259-360">Пакет:</span><span class="sxs-lookup"><span data-stu-id="f0259-360">The package:</span></span>

* <span data-ttu-id="f0259-361">Содержит класс размещения при запуске `ServiceKeyInjection`, который реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-362">`ServiceKeyInjection` добавляет пару строк службы в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="f0259-363">Включает атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="f0259-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="f0259-365">Страница индекса приложения считывает и отображает значения конфигурации для двух ключей, устанавливаемых начальной сборкой размещения пакета:</span><span class="sxs-lookup"><span data-stu-id="f0259-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="f0259-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0259-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="f0259-367">Консольное приложение без точки входа</span><span class="sxs-lookup"><span data-stu-id="f0259-367">Console app without an entry point</span></span>

<span data-ttu-id="f0259-368">*Этот подход может использоваться только для приложений .NET Core, но не для приложений .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="f0259-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="f0259-369">Расширение динамического размещения при запуске, для активации которого не требуется ссылка во время компиляции, может быть реализовано в консольном приложении без точки входа, содержащем атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-370">При публикации консольного приложения создается начальная сборка размещения, которая может использоваться в хранилище среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="f0259-371">В этом процессе используется консольное приложение без точки входа, так как:</span><span class="sxs-lookup"><span data-stu-id="f0259-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="f0259-372">Файл зависимостей необходим для функционирования размещения при запуске в начальной сборке размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="f0259-373">Файл зависимостей является ресурсом исполняемого приложения, который создается путем публикации приложения, а не библиотеки.</span><span class="sxs-lookup"><span data-stu-id="f0259-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="f0259-374">Библиотеку невозможно добавить непосредственно в [хранилище пакетов среды выполнения](/dotnet/core/deploying/runtime-store), для которого требуется запускаемый проект для общей среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="f0259-375">В ходе создания динамического размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="f0259-376">Начальная сборка размещения создается из консольного приложения без точки входа, которое:</span><span class="sxs-lookup"><span data-stu-id="f0259-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="f0259-377">содержит класс с реализацией `IHostingStartup`;</span><span class="sxs-lookup"><span data-stu-id="f0259-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="f0259-378">содержит атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) для определения класса реализации `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="f0259-379">Консольное приложение публикуется для получения зависимостей начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="f0259-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="f0259-380">После публикации консольного приложения неиспользуемые зависимости исключаются из файла зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="f0259-381">Файл зависимостей изменяется, чтобы задать расположение среды выполнения начальной сборки размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="f0259-382">Начальная сборка размещения и соответствующий файл зависимостей размещаются в хранилище пакетов среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="f0259-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="f0259-383">Чтобы можно было обнаружить начальную сборку размещения и ее файл зависимостей, они указываются в паре переменных среды.</span><span class="sxs-lookup"><span data-stu-id="f0259-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="f0259-384">Консольное приложение ссылается на пакет [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):</span><span class="sxs-lookup"><span data-stu-id="f0259-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="f0259-385">Атрибут [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) определяет класс как реализацию `IHostingStartup` для загрузки и выполнения при построении <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span><span class="sxs-lookup"><span data-stu-id="f0259-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="f0259-386">В следующем примере используется пространство имен `StartupEnhancement` и класс `StartupEnhancementHostingStartup`:</span><span class="sxs-lookup"><span data-stu-id="f0259-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="f0259-387">Класс реализует `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="f0259-388">Метод класса <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> использует <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> для добавления улучшений в приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="f0259-389">`IHostingStartup.Configure` в начальной сборке размещения вызывается средой выполнения до `Startup.Configure` в пользовательском коде, что позволяет пользовательскому коду перезаписать конфигурацию, предоставленную начальной сборкой размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="f0259-390">При создании проекта `IHostingStartup` файл зависимостей ( *.deps.json*) задает расположение `runtime` для сборки в папке *bin*:</span><span class="sxs-lookup"><span data-stu-id="f0259-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="f0259-391">Показана только часть файла.</span><span class="sxs-lookup"><span data-stu-id="f0259-391">Only part of the file is shown.</span></span> <span data-ttu-id="f0259-392">Имя сборки в примере — `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="f0259-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="f0259-393">Конфигурация, предоставляемая размещением при запуске</span><span class="sxs-lookup"><span data-stu-id="f0259-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="f0259-394">Существует два подхода к подготовке конфигурации в зависимости от того, какой конфигурацией вы хотите отдать приоритет — конфигурации размещения при запуске или конфигурации приложения:</span><span class="sxs-lookup"><span data-stu-id="f0259-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="f0259-395">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> для загрузки конфигурации после выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="f0259-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0259-396">При таком подходе конфигурация размещения при запуске будет иметь приоритет над конфигурацией приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="f0259-397">Задайте конфигурацию приложению, используя <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> для загрузки конфигурации до выполнения делегатов приложения <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>.</span><span class="sxs-lookup"><span data-stu-id="f0259-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="f0259-398">При таком подходе конфигурация приложения будет иметь приоритет над конфигурацией размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="f0259-399">Указание начальных сборок размещения</span><span class="sxs-lookup"><span data-stu-id="f0259-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="f0259-400">Для библиотеки класса или размещения при запуске с помощью консольного приложения укажите имя начальной сборки размещения в переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="f0259-401">Переменная среды — это список сборок, разделенный точками с запятой.</span><span class="sxs-lookup"><span data-stu-id="f0259-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="f0259-402">Только начальные сборки размещения проверяются на наличие атрибута `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-403">Для примера приложения *HostingStartupApp* необходимо установить следующее значение для переменной среды, чтобы обнаружить сборки размещения при запуске, описанные ранее:</span><span class="sxs-lookup"><span data-stu-id="f0259-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="f0259-404">Начальную сборку размещения также можно задать с помощью параметра конфигурации узла [Начальные сборки размещения](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="f0259-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="f0259-405">При наличии нескольких начальных сборок размещения их методы <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> выполняются в порядке расположения сборок.</span><span class="sxs-lookup"><span data-stu-id="f0259-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="f0259-406">Активация</span><span class="sxs-lookup"><span data-stu-id="f0259-406">Activation</span></span>

<span data-ttu-id="f0259-407">Ниже приведены варианты активации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="f0259-408">[Хранилище среды выполнения.](#runtime-store) Для активации не требуется ссылка во время компиляции.</span><span class="sxs-lookup"><span data-stu-id="f0259-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="f0259-409">Пример приложения помещает начальную сборку размещения и файлы зависимостей в папку *deployment*, чтобы облегчить развертывание размещения при запуске в среде с несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="f0259-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="f0259-410">Папка *deployment* также включает сценарий PowerShell, который создает или изменяет переменные среды в системе развертывания, чтобы включить размещение при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="f0259-411">Ссылки во время компиляции, необходимые для активации</span><span class="sxs-lookup"><span data-stu-id="f0259-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="f0259-412">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="f0259-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="f0259-413">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="f0259-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="f0259-414">Хранилище среды выполнения</span><span class="sxs-lookup"><span data-stu-id="f0259-414">Runtime store</span></span>

<span data-ttu-id="f0259-415">Реализация размещения при запуске помещается в [хранилище среды выполнения](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="f0259-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="f0259-416">Ссылка на сборку во время компиляции не требуется расширенному приложению.</span><span class="sxs-lookup"><span data-stu-id="f0259-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="f0259-417">После начальной сборки размещения создается хранилище среды выполнения с помощью файла манифеста проекта и команды [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="f0259-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="f0259-418">В примере приложения (проект *RuntimeStore*) используется такая команда:</span><span class="sxs-lookup"><span data-stu-id="f0259-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="f0259-419">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение хранилища среды выполнения добавляется к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0259-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="f0259-420">**Изменение и размещение файла зависимостей размещения при запуске**</span><span class="sxs-lookup"><span data-stu-id="f0259-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="f0259-421">Чтобы активировать расширение без ссылки на пакет расширения, укажите дополнительные зависимости в среде выполнения с помощью `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="f0259-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="f0259-422">`additionalDeps` предоставляет следующие возможности:</span><span class="sxs-lookup"><span data-stu-id="f0259-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="f0259-423">Расширять граф библиотеки приложения, предоставляя набор дополнительных файлов *.deps.json* для объединения с собственным файлом *.deps.json* приложения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="f0259-424">Обнаруживать и скачивать начальную сборку размещения.</span><span class="sxs-lookup"><span data-stu-id="f0259-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="f0259-425">Рекомендуемые действия при создании файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="f0259-426">Выполните команду `dotnet publish` для файла манифеста хранилища среды выполнения, ссылка на который создавалась в предыдущем разделе.</span><span class="sxs-lookup"><span data-stu-id="f0259-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="f0259-427">Удалите ссылку на манифест из библиотек и раздела `runtime` итогового файла *deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="f0259-428">В примере проекта свойство `store.manifest/1.0.0` удаляется из разделов `targets` и `libraries`.</span><span class="sxs-lookup"><span data-stu-id="f0259-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="f0259-429">Поместите файл *.deps.json* в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="f0259-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="f0259-430">`{ADDITIONAL DEPENDENCIES PATH}`. Расположение, добавляемое к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="f0259-431">`{SHARED FRAMEWORK NAME}`. Общая платформа, требуемая для этого файла дополнительных зависимостей.</span><span class="sxs-lookup"><span data-stu-id="f0259-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="f0259-432">`{SHARED FRAMEWORK VERSION}`. Минимальная версия общей платформы.</span><span class="sxs-lookup"><span data-stu-id="f0259-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="f0259-433">`{ENHANCEMENT ASSEMBLY NAME}`. Имя сборки расширения.</span><span class="sxs-lookup"><span data-stu-id="f0259-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="f0259-434">В примере приложения (проект *RuntimeStore*) файл дополнительных зависимостей помещается в следующее расположение:</span><span class="sxs-lookup"><span data-stu-id="f0259-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="f0259-435">Чтобы среда выполнения могла обнаружить хранилище среды выполнения, расположение файла дополнительных зависимостей добавляется к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="f0259-436">В примере приложения (проект *RuntimeStore*) для сборки хранилища среды выполнения и создания файла дополнительных зависимостей используется скрипт [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="f0259-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="f0259-437">Примеры установки переменных среды для различных операционных систем см. в разделе [Использование нескольких сред](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f0259-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="f0259-438">**Развертывание**</span><span class="sxs-lookup"><span data-stu-id="f0259-438">**Deployment**</span></span>

<span data-ttu-id="f0259-439">Чтобы упростить развертывание размещения при запуске в среде с несколькими компьютерами, пример приложения создает в опубликованном результате папку *deployment*. Эта папка содержит:</span><span class="sxs-lookup"><span data-stu-id="f0259-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="f0259-440">Хранилище среды выполнения размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="f0259-441">Файл зависимостей размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="f0259-442">Скрипт PowerShell, который создает или изменяет `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` и `DOTNET_ADDITIONAL_DEPS` для поддержки активации размещения при запуске.</span><span class="sxs-lookup"><span data-stu-id="f0259-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="f0259-443">Запустите сценарий из командной строки PowerShell с правами администратора в системе развертывания.</span><span class="sxs-lookup"><span data-stu-id="f0259-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="f0259-444">Пакет NuGet</span><span class="sxs-lookup"><span data-stu-id="f0259-444">NuGet package</span></span>

<span data-ttu-id="f0259-445">Расширение размещения при запуске можно указать в пакете NuGet.</span><span class="sxs-lookup"><span data-stu-id="f0259-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="f0259-446">Пакет содержит атрибут `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="f0259-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="f0259-447">Типы размещения при запуске, предоставляемые пакетом, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="f0259-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="f0259-448">Файл проекта расширенного приложения создает ссылку на пакет для размещения при запуске в файле проекта приложения (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="f0259-449">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="f0259-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0259-450">Этот подход применяется к пакету начальной сборки размещения, опубликованному на [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="f0259-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="f0259-451">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="f0259-452">Дополнительные сведения о пакетах NuGet и хранилище среды выполнения см. в разделах:</span><span class="sxs-lookup"><span data-stu-id="f0259-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="f0259-453">Создание пакета NuGet с помощью кроссплатформенных средств</span><span class="sxs-lookup"><span data-stu-id="f0259-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="f0259-454">Публикация пакетов</span><span class="sxs-lookup"><span data-stu-id="f0259-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="f0259-455">Хранилище пакетов среды выполнения</span><span class="sxs-lookup"><span data-stu-id="f0259-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="f0259-456">Папка bin проекта</span><span class="sxs-lookup"><span data-stu-id="f0259-456">Project bin folder</span></span>

<span data-ttu-id="f0259-457">Расширение размещения при запуске может быть представлено сборкой, разворачиваемой в папке *bin* расширенного приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="f0259-458">Типы размещения при запуске, предоставляемые сборкой, становятся доступными для приложения с использованием одного из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="f0259-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="f0259-459">Файл проекта расширенного приложения создает ссылку сборки на размещение при запуске (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="f0259-460">Со ссылкой во время компиляции начальная сборка размещения и все ее зависимости включаются в файл зависимостей приложения ( *.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="f0259-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="f0259-461">Этот подход применяется при вызове сценария развертывания для создания ссылки времени компиляции на сборку запуска размещения (*DLL*-файл) и перемещения сборки в одно из следующих мест:</span><span class="sxs-lookup"><span data-stu-id="f0259-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="f0259-462">Проект, который будет использовать ее.</span><span class="sxs-lookup"><span data-stu-id="f0259-462">The consuming project.</span></span>
  * <span data-ttu-id="f0259-463">Расположение, доступное использующему проекту.</span><span class="sxs-lookup"><span data-stu-id="f0259-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="f0259-464">Файл зависимостей размещения при запуске становится доступным для расширенного приложения, как описано в разделе [Хранилище среды выполнения](#runtime-store) (без ссылок во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="f0259-465">При нацеливании на .NET Framework сборка загружается в контексте загрузки по умолчанию, что для .NET Framework означает, что сборка находится в одном из следующих расположений:</span><span class="sxs-lookup"><span data-stu-id="f0259-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="f0259-466">Базовый путь приложения. Папка *bin*, в которой находится исполняемый файл приложения ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="f0259-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="f0259-467">Глобальный кэш сборок. В глобальном кэше сборок сохраняются сборки, которые могут использоваться несколькими приложениями .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0259-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="f0259-468">Дополнительные сведения см. в разделе [Практическое руководство. Установка сборки в глобальный кэш сборок](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) в документации по .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f0259-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="f0259-469">Пример кода</span><span class="sxs-lookup"><span data-stu-id="f0259-469">Sample code</span></span>

<span data-ttu-id="f0259-470">В [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([инструкции по скачиванию примера](xref:index#how-to-download-a-sample)) показаны сценарии реализации размещения при запуске:</span><span class="sxs-lookup"><span data-stu-id="f0259-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="f0259-471">В каждой из двух сборок начального размещения (библиотеки классов) устанавливаются две пары "ключ-значение", хранящиеся в памяти:</span><span class="sxs-lookup"><span data-stu-id="f0259-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="f0259-472">Пакет NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="f0259-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="f0259-473">Библиотека классов (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="f0259-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="f0259-474">Размещения при запуске активируется из сборки среды выполнения, развертываемой из хранилища (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="f0259-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="f0259-475">Эта сборка добавляет два вида ПО промежуточного слоя, которые предоставляют диагностические сведения о следующих показателях:</span><span class="sxs-lookup"><span data-stu-id="f0259-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="f0259-476">Зарегистрированные службы</span><span class="sxs-lookup"><span data-stu-id="f0259-476">Registered services</span></span>
  * <span data-ttu-id="f0259-477">Адрес (схема, узел, базовый путь, путь, строка запроса)</span><span class="sxs-lookup"><span data-stu-id="f0259-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="f0259-478">Подключение (удаленный IP-адрес, удаленный порт, локальный IP-адрес, локальный порт, сертификат клиента)</span><span class="sxs-lookup"><span data-stu-id="f0259-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="f0259-479">Заголовки запросов</span><span class="sxs-lookup"><span data-stu-id="f0259-479">Request headers</span></span>
  * <span data-ttu-id="f0259-480">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="f0259-480">Environment variables</span></span>

<span data-ttu-id="f0259-481">Для выполнения образца:</span><span class="sxs-lookup"><span data-stu-id="f0259-481">To run the sample:</span></span>

<span data-ttu-id="f0259-482">**Активация из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="f0259-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="f0259-483">Скомпилируйте пакет *HostingStartupPackage* с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="f0259-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="f0259-484">Добавьте имя сборки пакета *HostingStartupPackage* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0259-485">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-485">Compile and run the app.</span></span> <span data-ttu-id="f0259-486">Ссылка на пакет появится в расширенном приложении (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="f0259-487">Объект `<PropertyGroup>` в файле проекта приложения определяет выходные данные проекта пакета ( *../ HostingStartupPackage/bin/Debug*) в качестве источника пакета.</span><span class="sxs-lookup"><span data-stu-id="f0259-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="f0259-488">Это позволяет приложению использовать пакет без отправки пакета на сайт [nuget.org](https://www.nuget.org/). Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0259-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f0259-489">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода пакета `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f0259-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0259-490">Если вы внесли изменения в проект *HostingStartupPackage* и перекомпилировали его, очистите локальные кэши пакета NuGet, чтобы убедиться, что *HostingStartupApp* получает обновленный пакет, а не устаревший пакет из локального кэша.</span><span class="sxs-lookup"><span data-stu-id="f0259-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="f0259-491">Чтобы очистить локальные кэши NuGet, выполните следующую команду [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):</span><span class="sxs-lookup"><span data-stu-id="f0259-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="f0259-492">**Активации из библиотеки классов**</span><span class="sxs-lookup"><span data-stu-id="f0259-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="f0259-493">Скомпилируйте библиотеку классов *HostingStartupLibrary* с помощью команды [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="f0259-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="f0259-494">Добавьте имя сборки библиотеки классов *HostingStartupLibrary* в переменную среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="f0259-495">Разверните сборку библиотеки классов в папку *bin* приложения. Для этого скопируйте файл *HostingStartupLibrary.dll* из выходной папки компиляции библиотеки в папку *bin/Debug* приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="f0259-496">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="f0259-496">Compile and run the app.</span></span> <span data-ttu-id="f0259-497">`<ItemGroup>` в файле проекта приложения ссылается на сборку библиотеки класса ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (ссылка во время компиляции).</span><span class="sxs-lookup"><span data-stu-id="f0259-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="f0259-498">Дополнительные сведения см. в примечаниях в файле проекта HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="f0259-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="f0259-499">Обратите внимание, что ключевые значения конфигурации службы, отображаемой на индексной странице, соответствуют значениям, установленным с помощью метода библиотеки класса `ServiceKeyInjection.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f0259-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="f0259-500">**Активация из сборки среды выполнения, развернутой в хранилище**</span><span class="sxs-lookup"><span data-stu-id="f0259-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="f0259-501">В проекте *StartupDiagnostics* используется [PowerShell](/powershell/scripting/powershell-scripting) для изменения файла *StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="f0259-502">PowerShell устанавливается по умолчанию в Windows начиная с Windows 7 с пакетом обновления 1 (SP1) и Windows Server 2008 R2 с пакетом обновления 1 (SP1).</span><span class="sxs-lookup"><span data-stu-id="f0259-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="f0259-503">Для установки PowerShell на других платформах см. раздел [Установка разных версий PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="f0259-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="f0259-504">Выполните сценарий *build.ps1*, находящийся в папке *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="f0259-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="f0259-505">Скрипт выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="f0259-505">The script:</span></span>
   * <span data-ttu-id="f0259-506">Генерирует пакет `StartupDiagnostics` в папке *obj\packages*.</span><span class="sxs-lookup"><span data-stu-id="f0259-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="f0259-507">Создает хранилище среды выполнения для `StartupDiagnostics` в папке *store*.</span><span class="sxs-lookup"><span data-stu-id="f0259-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="f0259-508">Для размещения при запуске, развернутом в Windows, команда `dotnet store` в сценарии использует [идентификатор среды выполнения (RID)](/dotnet/core/rid-catalog) `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="f0259-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="f0259-509">При выполнении размещения при запуске для другой среды выполнения укажите соответствующий RID в строке 37 скрипта.</span><span class="sxs-lookup"><span data-stu-id="f0259-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="f0259-510">Хранилище среды выполнения для `StartupDiagnostics` позже будет перемещено в хранилище среды выполнения пользователя или системы на компьютере, где будет израсходована сборка.</span><span class="sxs-lookup"><span data-stu-id="f0259-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="f0259-511">Пользовательское место установки хранилища среды выполнения для сборки `StartupDiagnostics` — *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="f0259-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="f0259-512">Генерирует `additionalDeps` для `StartupDiagnostics` в папке *additionalDeps*.</span><span class="sxs-lookup"><span data-stu-id="f0259-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="f0259-513">Дополнительные зависимости позднее будут перенесены в дополнительные зависимости пользователя или системы.</span><span class="sxs-lookup"><span data-stu-id="f0259-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="f0259-514">Расположением установки дополнительных зависимостей пользователя `StartupDiagnostics` является *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="f0259-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="f0259-515">Размещает файл *deploy.ps1* в папке для *развертывания*.</span><span class="sxs-lookup"><span data-stu-id="f0259-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="f0259-516">Выполните сценарий *deploy.ps1*, находящийся в папке *deployment*.</span><span class="sxs-lookup"><span data-stu-id="f0259-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="f0259-517">Скрипт присоединяет:</span><span class="sxs-lookup"><span data-stu-id="f0259-517">The script appends:</span></span>
   * <span data-ttu-id="f0259-518">`StartupDiagnostics` к переменной среды `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="f0259-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="f0259-519">Путь размещения начальных зависимостей (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="f0259-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="f0259-520">Путь хранилища среды выполнения (в папке *развертывания* проекта "RuntimeStore") к переменной среды `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="f0259-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="f0259-521">Выполните пример приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-521">Run the sample app.</span></span>
1. <span data-ttu-id="f0259-522">Запросите конечную точку `/services` для просмотра зарегистрированных служб приложения.</span><span class="sxs-lookup"><span data-stu-id="f0259-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="f0259-523">Запросите конечную точку `/diag` для просмотра диагностических сведений.</span><span class="sxs-lookup"><span data-stu-id="f0259-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
