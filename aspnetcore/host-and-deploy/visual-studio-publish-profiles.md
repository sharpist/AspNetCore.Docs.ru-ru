---
<span data-ttu-id="81549-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-102">'Blazor'</span></span>
- <span data-ttu-id="81549-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-103">'Identity'</span></span>
- <span data-ttu-id="81549-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-105">'Razor'</span></span>
- <span data-ttu-id="81549-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-106">'SignalR' uid:</span></span> 

---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="81549-107">Профили публикации Visual Studio (.pubxml) для развертывания приложений ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81549-107">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="81549-108">Авторы: [Саид Ибрагим Хашими](https://github.com/sayedihashimi) (Sayed Ibrahim Hashimi) и [Рик Андерсон](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81549-108">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81549-109">Этот документ посвящен использованию Visual Studio 2019 или более поздней версии для создания и применения профилей публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-109">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="81549-110">Профили публикации, созданные с помощью Visual Studio, можно применять в MSBuild и Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81549-110">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="81549-111">Инструкции по публикации в Azure см. в разделе <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="81549-111">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="81549-112">Команда `dotnet new mvc` создает файл проекта со следующим [элементом \<Project>](/visualstudio/msbuild/project-element-msbuild) на верхнем уровне:</span><span class="sxs-lookup"><span data-stu-id="81549-112">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="81549-113">В описанном выше элементе `<Project>` атрибут `Sdk` импортирует [свойства](/visualstudio/msbuild/msbuild-properties) и [целевые объекты](/visualstudio/msbuild/msbuild-targets) для MSBuild из файлов *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\ SDK.props* и *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, соответственно.</span><span class="sxs-lookup"><span data-stu-id="81549-113">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="81549-114">По умолчанию `$(MSBuildSDKsPath)` (в Visual Studio 2019 Enterprise) находится в папке *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="81549-114">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="81549-115">`Microsoft.NET.Sdk.Web` ([Веб-пакет SDK](xref:razor-pages/web-sdk)) зависит от других пакетов SDK, включая `Microsoft.NET.Sdk` ([пакет SDK для .NET Core](/dotnet/core/project-sdk/msbuild-props)) и `Microsoft.NET.Sdk.Razor` ([пакет SDK для Razor](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="81549-115">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="81549-116">Импортируются свойства и целевые объекты MSBuild, связанные с каждым из зависимых пакетов SDK.</span><span class="sxs-lookup"><span data-stu-id="81549-116">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="81549-117">Целевые объекты публикации импортируют подходящий набор целевых объектов в зависимости от используемых методов публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-117">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="81549-118">Когда MSBuild или Visual Studio загружает проект, выполняются следующие обобщенные действия:</span><span class="sxs-lookup"><span data-stu-id="81549-118">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="81549-119">сборка проекта;</span><span class="sxs-lookup"><span data-stu-id="81549-119">Build project</span></span>
* <span data-ttu-id="81549-120">вычисление файлов для публикации;</span><span class="sxs-lookup"><span data-stu-id="81549-120">Compute files to publish</span></span>
* <span data-ttu-id="81549-121">публикация файлов в месте назначения;</span><span class="sxs-lookup"><span data-stu-id="81549-121">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="81549-122">вычисление элементов проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-122">Compute project items</span></span>

<span data-ttu-id="81549-123">После загрузки проекта вычисляются [элементы (файлы) проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items).</span><span class="sxs-lookup"><span data-stu-id="81549-123">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="81549-124">Порядок обработки файла определяется типом элемента.</span><span class="sxs-lookup"><span data-stu-id="81549-124">The item type determines how the file is processed.</span></span> <span data-ttu-id="81549-125">По умолчанию файлы с расширением *.cs* включаются в список элементов `Compile`.</span><span class="sxs-lookup"><span data-stu-id="81549-125">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="81549-126">Файлы в списке элементов `Compile` компилируются.</span><span class="sxs-lookup"><span data-stu-id="81549-126">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="81549-127">Список элементов `Content` содержит файлы, предназначенные для публикации, а также результаты сборки.</span><span class="sxs-lookup"><span data-stu-id="81549-127">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="81549-128">По умолчанию в список элементов `Content` включаются файлы, соответствующие шаблонам `wwwroot\**`, `**\*.config` и `**\*.json`.</span><span class="sxs-lookup"><span data-stu-id="81549-128">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="81549-129">Например, [стандартная маска](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` соответствует всем файлам в папке *wwwroot* и всех ее вложенных папках.</span><span class="sxs-lookup"><span data-stu-id="81549-129">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81549-130">[Веб-пакет SDK](xref:razor-pages/web-sdk) импортирует [пакет SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="81549-130">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="81549-131">Это означает, что в список элементов `Content` дополнительно включаются файлы, соответствующие шаблонам `**\*.cshtml` и `**\*.razor`.</span><span class="sxs-lookup"><span data-stu-id="81549-131">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="81549-132">[Веб-пакет SDK](xref:razor-pages/web-sdk) импортирует [пакет SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="81549-132">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="81549-133">Это означает, что в список элементов `Content` дополнительно включаются файлы, соответствующие шаблону `**\*.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="81549-133">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="81549-134">Чтобы явным образом добавить файл в список публикации, поместите его в *CSPROJ*-файл, как показано в разделе [включения файлов](#include-files).</span><span class="sxs-lookup"><span data-stu-id="81549-134">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="81549-135">При нажатии кнопки **Публикация** в Visual Studio или при публикации из командной строки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="81549-135">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="81549-136">Вычисляются свойства и (или) элементы (файлы, требующие сборки).</span><span class="sxs-lookup"><span data-stu-id="81549-136">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="81549-137">**Только Visual Studio**: пакеты NuGet восстанавливаются.</span><span class="sxs-lookup"><span data-stu-id="81549-137">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="81549-138">(Восстановление выполняется пользователем в интерфейсе командной строки.)</span><span class="sxs-lookup"><span data-stu-id="81549-138">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="81549-139">Выполняется сборка проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-139">The project builds.</span></span>
* <span data-ttu-id="81549-140">Вычисляются публикуемые элементы (файлы, требующие публикации).</span><span class="sxs-lookup"><span data-stu-id="81549-140">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="81549-141">Публикуется проект (вычисляемые файлы копируются в место назначения публикации.)</span><span class="sxs-lookup"><span data-stu-id="81549-141">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="81549-142">Когда проект ASP.NET Core ссылается на `Microsoft.NET.Sdk.Web` в файле проекта, файл *app_offline.htm* помещается в корневой каталог веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="81549-142">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="81549-143">Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-143">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="81549-144">Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="81549-144">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="81549-145">Простая публикация из командной строки</span><span class="sxs-lookup"><span data-stu-id="81549-145">Basic command-line publishing</span></span>

<span data-ttu-id="81549-146">Публикация из командной строки работает на всех платформах, поддерживаемых .NET Core, и не требует наличия Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81549-146">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="81549-147">В приведенных ниже примерах команда .NET Core CLI [dotnet publish](/dotnet/core/tools/dotnet-publish) выполняется из папки проекта (где хранится *CSPROJ*-файл).</span><span class="sxs-lookup"><span data-stu-id="81549-147">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="81549-148">Если папка проекта не является текущим рабочим каталогом, явным образом передайте путь к файлу проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-148">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="81549-149">Пример:</span><span class="sxs-lookup"><span data-stu-id="81549-149">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="81549-150">Для создания и публикации веб-приложения выполните следующие команды.</span><span class="sxs-lookup"><span data-stu-id="81549-150">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="81549-151">Команда `dotnet publish` создает подобные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="81549-151">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="81549-152">По умолчанию папка публикации имеет формат *bin\Debug\\{МОНИКЕР ЦЕЛЕВОЙ ПЛАТФОРМЫ}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="81549-152">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="81549-153">Например, *bin\Debug\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="81549-153">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="81549-154">Следующая команда определяет сборку `Release` и папку для публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-154">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="81549-155">Команда `dotnet publish` вызывает метод MSBuild, который, в свою очередь, вызывает целевой объект `Publish`.</span><span class="sxs-lookup"><span data-stu-id="81549-155">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="81549-156">Все параметры, передаваемые в `dotnet publish`, передаются далее в MSBuild.</span><span class="sxs-lookup"><span data-stu-id="81549-156">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="81549-157">Параметры `-c` и `-o` сопоставляются со свойствами MSBuild `Configuration` и `OutputPath` соответственно.</span><span class="sxs-lookup"><span data-stu-id="81549-157">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="81549-158">Свойства MSBuild можно передавать в любом из следующих форматов.</span><span class="sxs-lookup"><span data-stu-id="81549-158">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="81549-159">Например, следующая команда публикует сборку `Release` в общую сетевую папку.</span><span class="sxs-lookup"><span data-stu-id="81549-159">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="81549-160">Общая сетевая папка указывается с помощью символов косой черты ( *//r8/* ) и работает на всех поддерживаемых платформах .NET Core.</span><span class="sxs-lookup"><span data-stu-id="81549-160">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="81549-161">Убедитесь, что приложение, публикуемое для развертывания, не запущено.</span><span class="sxs-lookup"><span data-stu-id="81549-161">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="81549-162">Во время выполнения приложения файлы в папке *publish* блокируются.</span><span class="sxs-lookup"><span data-stu-id="81549-162">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="81549-163">Заблокированные файлы скопировать нельзя, так что развертывание в этом случае не произойдет.</span><span class="sxs-lookup"><span data-stu-id="81549-163">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="81549-164">Профили публикации</span><span class="sxs-lookup"><span data-stu-id="81549-164">Publish profiles</span></span>

<span data-ttu-id="81549-165">В этом разделе для создания профиля публикации используется Visual Studio 2019 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="81549-165">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="81549-166">После создания профиля публикацию можно выполнять из Visual Studio или из командной строки.</span><span class="sxs-lookup"><span data-stu-id="81549-166">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="81549-167">Профили публикации упрощают процесс публикации. Вы можете создать любое количество профилей.</span><span class="sxs-lookup"><span data-stu-id="81549-167">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="81549-168">Создайте профиль публикации в Visual Studio, выбрав один из следующих методов:</span><span class="sxs-lookup"><span data-stu-id="81549-168">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="81549-169">Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать**.</span><span class="sxs-lookup"><span data-stu-id="81549-169">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="81549-170">Выберите команду **Опубликовать {имя проекта}** в меню **Сборка**.</span><span class="sxs-lookup"><span data-stu-id="81549-170">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="81549-171">Откроется вкладка **Публикация** на странице возможностей приложения.</span><span class="sxs-lookup"><span data-stu-id="81549-171">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="81549-172">Если в проекте нет профиля публикации, откроется страница **Выберите целевой объект публикации**.</span><span class="sxs-lookup"><span data-stu-id="81549-172">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="81549-173">Вам будет предложено выбрать один из следующих целевых объектов публикации:</span><span class="sxs-lookup"><span data-stu-id="81549-173">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="81549-174">Служба приложений Azure</span><span class="sxs-lookup"><span data-stu-id="81549-174">Azure App Service</span></span>
* <span data-ttu-id="81549-175">Служба приложений Azure в Linux</span><span class="sxs-lookup"><span data-stu-id="81549-175">Azure App Service on Linux</span></span>
* <span data-ttu-id="81549-176">Виртуальные машины Azure</span><span class="sxs-lookup"><span data-stu-id="81549-176">Azure Virtual Machines</span></span>
* <span data-ttu-id="81549-177">Папка</span><span class="sxs-lookup"><span data-stu-id="81549-177">Folder</span></span>
* <span data-ttu-id="81549-178">IIS, FTP, веб-развертывание (для любого веб-сервера)</span><span class="sxs-lookup"><span data-stu-id="81549-178">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="81549-179">Профиль импорта</span><span class="sxs-lookup"><span data-stu-id="81549-179">Import Profile</span></span>

<span data-ttu-id="81549-180">Чтобы определить наиболее подходящий целевой объект публикации, см. раздел [Какие варианты публикации мне подойдут](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="81549-180">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="81549-181">Если вы выбрали **Папка** в качестве целевого объекта, укажите путь к папке, в которой будут храниться опубликованные ресурсы.</span><span class="sxs-lookup"><span data-stu-id="81549-181">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="81549-182">Путь к папке по умолчанию: *bin\\{КОНФИГУРАЦИЯ ПРОЕКТА}\\{МОНИКЕР ЦЕЛЕВОЙ ПЛАТФОРМЫ}\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="81549-182">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="81549-183">Например, *bin\Release\netcoreapp2.2\publish\\* .</span><span class="sxs-lookup"><span data-stu-id="81549-183">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="81549-184">Нажмите кнопку **Создать профиль**, чтобы завершить процесс.</span><span class="sxs-lookup"><span data-stu-id="81549-184">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="81549-185">Когда профиль публикации будет создан, изменения отразятся на вкладке **Публикация**.</span><span class="sxs-lookup"><span data-stu-id="81549-185">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="81549-186">Новый профиль появится в раскрывающемся списке.</span><span class="sxs-lookup"><span data-stu-id="81549-186">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="81549-187">Под раскрывающимся списком щелкните **Создать новый профиль**, чтобы создать еще один профиль.</span><span class="sxs-lookup"><span data-stu-id="81549-187">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="81549-188">Инструмент публикации Visual Studio создает файл MSBuild *Properties/PublishProfiles/{имя_профиля}.pubxml* с описанием профиля публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-188">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="81549-189">*PUBXML*-файл:</span><span class="sxs-lookup"><span data-stu-id="81549-189">The *.pubxml* file:</span></span>

* <span data-ttu-id="81549-190">Содержит настройки конфигурации публикации и используется в процессе публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-190">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="81549-191">Может меняться для настройки процесса сборки и публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-191">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="81549-192">Если публикация выполняется в целевой объект Azure, файл с расширением *.pubxml* содержит идентификатор подписки Azure.</span><span class="sxs-lookup"><span data-stu-id="81549-192">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="81549-193">При таком типе целевого объекта мы не рекомендуем добавлять этот файл в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="81549-193">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="81549-194">Если публикация выполняется в целевой объект, не относящийся к Azure, файл с расширением *.pubxml* можно безопасно вернуть в систему.</span><span class="sxs-lookup"><span data-stu-id="81549-194">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="81549-195">Конфиденциальные данные (например, пароль публикации) шифруются на уровне пользователя или компьютера.</span><span class="sxs-lookup"><span data-stu-id="81549-195">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="81549-196">Они сохраняются в файле *Properties/PublishProfiles/{имя_профиля}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="81549-196">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="81549-197">Так как этот файл может содержать конфиденциальные данные, его не следует возвращать в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="81549-197">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="81549-198">Общие сведения о публикации веб-приложения ASP.NET Core см. в статье <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="81549-198">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="81549-199">Открытый исходный код задач MSBuild и целевых объектов, необходимых для публикации веб-приложения ASP.NET Core, размещен в [репозитории aspnet/websdk](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="81549-199">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="81549-200">Следующие команды могут использовать профили публикации папки, MSDeploy и [Kudu](https://github.com/projectkudu/kudu/wiki).</span><span class="sxs-lookup"><span data-stu-id="81549-200">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="81549-201">Так как в MSDeploy отсутствует кроссплатформенная поддержка, следующие параметры MSDeploy поддерживаются только в Windows.</span><span class="sxs-lookup"><span data-stu-id="81549-201">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="81549-202">**Папка (работает на всех платформах):**</span><span class="sxs-lookup"><span data-stu-id="81549-202">**Folder (works cross-platform):**</span></span>

<!--

NOTE: Add back the following 'dotnet publish' folder publish example after https://github.com/aspnet/websdk/issues/888 is resolved.

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

-->

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="81549-203">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="81549-203">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="81549-204">**Пакет MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="81549-204">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="81549-205">В предшествующих примерах:</span><span class="sxs-lookup"><span data-stu-id="81549-205">In the preceding examples:</span></span>

* <span data-ttu-id="81549-206">`dotnet publish` и `dotnet build`поддерживают API-интерфейсы Kudu для публикации в Azure с любой платформы.</span><span class="sxs-lookup"><span data-stu-id="81549-206">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="81549-207">Публикация с помощью Visual Studio поддерживает API Kudu, но для кроссплатформенной публикации в Azure их поддерживает WebSDK.</span><span class="sxs-lookup"><span data-stu-id="81549-207">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="81549-208">Не передавайте `DeployOnBuild` команде `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="81549-208">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="81549-209">Дополнительные сведения см. в [документации по Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="81549-209">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="81549-210">Добавьте в папку проекта *Properties/PublishProfiles* профиль публикации со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="81549-210">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="81549-211">Пример публикации папки</span><span class="sxs-lookup"><span data-stu-id="81549-211">Folder publish example</span></span>

<span data-ttu-id="81549-212">Для публикации с использованием профиля *FolderProfile* используйте одну из указанных ниже команд.</span><span class="sxs-lookup"><span data-stu-id="81549-212">When publishing with a profile named *FolderProfile*, use either of the following commands:</span></span>

<!--

NOTE: Temporarily removed until https://github.com/aspnet/websdk/issues/888 is resolved.

* `dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`

-->

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="81549-213">Команда .NET Core CLI [dotnet build](/dotnet/core/tools/dotnet-build) вызывает `msbuild` для сборки и публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-213">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="81549-214">Команды `dotnet build` и `msbuild` эквивалентны, если они передаются в профиле папки.</span><span class="sxs-lookup"><span data-stu-id="81549-214">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="81549-215">При прямом вызове `msbuild` на платформе Windows используется версия MSBuild для .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="81549-215">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="81549-216">Вызов `dotnet build` не в профиле папки:</span><span class="sxs-lookup"><span data-stu-id="81549-216">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="81549-217">Вызывает `msbuild`, который использует MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="81549-217">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="81549-218">Приводит к сбою (даже при работе в Windows).</span><span class="sxs-lookup"><span data-stu-id="81549-218">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="81549-219">Для публикации профилей, отличных от профиля папки, вызывайте `msbuild` напрямую.</span><span class="sxs-lookup"><span data-stu-id="81549-219">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="81549-220">Следующий профиль публикации папки был создан в Visual Studio и публикуется в общей сетевой папке:</span><span class="sxs-lookup"><span data-stu-id="81549-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="81549-221">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="81549-221">In the preceding example:</span></span>

* <span data-ttu-id="81549-222">Свойство `<ExcludeApp_Data>` присутствует просто для того, чтобы удовлетворить потребность в схеме XML.</span><span class="sxs-lookup"><span data-stu-id="81549-222">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="81549-223">Свойство `<ExcludeApp_Data>` не влияет на процесс публикации даже при наличии папки *App_Data* в корневом каталоге проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-223">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="81549-224">Папка *App_Data* не получает специальной обработки, как в проектах ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="81549-224">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>

<!--

NOTE: Temporarily removed from 'Using the .NET Core CLI' below until https://github.com/aspnet/websdk/issues/888 is resolved.

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

-->

* <span data-ttu-id="81549-225">Свойству `<LastUsedBuildConfiguration>` задано значение `Release`.</span><span class="sxs-lookup"><span data-stu-id="81549-225">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="81549-226">При публикации из Visual Studio значение `<LastUsedBuildConfiguration>` указывается на основе значения, которое использовалось при запуске процесса публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-226">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="81549-227">`<LastUsedBuildConfiguration>` имеет особое значение и его не следует переопределять в импортируемом файле MSBuild.</span><span class="sxs-lookup"><span data-stu-id="81549-227">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="81549-228">Тем не менее, это свойство можно переопределить в командной строке с помощью одного из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="81549-228">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="81549-229">С использованием .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="81549-229">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="81549-230">С использованием MSBuild</span><span class="sxs-lookup"><span data-stu-id="81549-230">Using MSBuild:</span></span>

    ```console
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="81549-231">Дополнительные сведения см. в статье [MSBuild: настройка свойства конфигурации](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="81549-231">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="81549-232">Публикация конечной точки MSDeploy из командной строки</span><span class="sxs-lookup"><span data-stu-id="81549-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="81549-233">В следующем примере используется веб-приложение ASP.NET Core, созданное с помощью Visual Studio, с именем *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="81549-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="81549-234">Профиль публикации приложений Azure добавляется с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81549-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="81549-235">Дополнительные сведения о том, как создать профиль, см. в разделе [Профили публикации](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="81549-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="81549-236">Чтобы развернуть приложение с помощью профиля публикации, выполните команду `msbuild` из **командной строки разработчика** Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81549-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="81549-237">Командная строка доступна в папке *Visual Studio* в меню **Пуск** на панели задач Windows.</span><span class="sxs-lookup"><span data-stu-id="81549-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="81549-238">Для удобства можно добавить командную строку в меню **Сервис** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="81549-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="81549-239">Дополнительные сведения см. в статье [Командная строка разработчика для Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="81549-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="81549-240">MSBuild использует следующий синтаксис команд.</span><span class="sxs-lookup"><span data-stu-id="81549-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="81549-241">{PATH}: путь к файлу проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="81549-241">{PATH}: Path to the app's project file.</span></span>
* <span data-ttu-id="81549-242">{PROFILE}: имя профиля публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-242">{PROFILE}: Name of the publish profile.</span></span>
* <span data-ttu-id="81549-243">{USERNAME}: имя пользователя MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="81549-243">{USERNAME}: MSDeploy username.</span></span> <span data-ttu-id="81549-244">{USERNAME} можно найти в профиле публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="81549-245">{PASSWORD}: пароль MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="81549-245">{PASSWORD}: MSDeploy password.</span></span> <span data-ttu-id="81549-246">Получите {PASSWORD} из файла *{PROFILE}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="81549-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="81549-247">Файл с расширением *.PublishSettings* можно скачать одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="81549-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="81549-248">**Обозреватель решений**: Выберите **Представление** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="81549-248">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="81549-249">Подключитесь к подписке Azure.</span><span class="sxs-lookup"><span data-stu-id="81549-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="81549-250">Откройте **Службы приложений**.</span><span class="sxs-lookup"><span data-stu-id="81549-250">Open **App Services**.</span></span> <span data-ttu-id="81549-251">Щелкните правой кнопкой приложение.</span><span class="sxs-lookup"><span data-stu-id="81549-251">Right-click the app.</span></span> <span data-ttu-id="81549-252">Выберите **Загрузить профиль публикации**.</span><span class="sxs-lookup"><span data-stu-id="81549-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="81549-253">Портал Azure: щелкните **Скачать профиль публикации** на панели **Обзор** веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="81549-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="81549-254">В следующем примере используется профиль публикации с именем *AzureWebApp — веб-развертывание*:</span><span class="sxs-lookup"><span data-stu-id="81549-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="81549-255">Профиль публикации можно также использовать с командой .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) из командной оболочки Windows:</span><span class="sxs-lookup"><span data-stu-id="81549-255">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="81549-256">Команда `dotnet msbuild` доступна на всех платформах и может компилировать приложения ASP.NET Core в macOS и Linux.</span><span class="sxs-lookup"><span data-stu-id="81549-256">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="81549-257">Однако MSBuild в macOS и Linux не может развертывать приложения в Azure или других конечных точках MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="81549-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="81549-258">Указание среды</span><span class="sxs-lookup"><span data-stu-id="81549-258">Set the environment</span></span>

<span data-ttu-id="81549-259">Включите свойство `<EnvironmentName>` в профиле публикации (*PUBXML*) или файле проекта, чтобы задать [среду](xref:fundamentals/environments) приложения:</span><span class="sxs-lookup"><span data-stu-id="81549-259">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="81549-260">Если вам нужно преобразовать *web.config* (например, задать переменные среды на основе конфигурации, профиля или среды), см. раздел <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="81549-260">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="81549-261">Исключение файлов</span><span class="sxs-lookup"><span data-stu-id="81549-261">Exclude files</span></span>

<span data-ttu-id="81549-262">При публикации веб-приложений ASP.NET Core включаются следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="81549-262">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="81549-263">Артефакты сборки</span><span class="sxs-lookup"><span data-stu-id="81549-263">Build artifacts</span></span>
* <span data-ttu-id="81549-264">Файлы и папки, соответствующие следующим стандартным маскам:</span><span class="sxs-lookup"><span data-stu-id="81549-264">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="81549-265">`**\*.config` (например, *web.config*);</span><span class="sxs-lookup"><span data-stu-id="81549-265">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="81549-266">`**\*.json` (например, *appsettings.json*).</span><span class="sxs-lookup"><span data-stu-id="81549-266">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="81549-267">MSBuild поддерживает [стандартные маски](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="81549-267">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="81549-268">Например, представленный ниже элемент `<Content>` запретит копирование текстовых файлов ( *.txt*) из папки *wwwroot\content* и всех ее подпапок.</span><span class="sxs-lookup"><span data-stu-id="81549-268">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="81549-269">Предложенную выше разметку можно добавить в профиль публикации или в файл *CSPROJ*.</span><span class="sxs-lookup"><span data-stu-id="81549-269">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="81549-270">При добавлении в файл *.csproj* правило добавляется во все профили публикации в проекте.</span><span class="sxs-lookup"><span data-stu-id="81549-270">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="81549-271">Следующий элемент `<MsDeploySkipRules>` исключает все файлы из папки *wwwroot\content*.</span><span class="sxs-lookup"><span data-stu-id="81549-271">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="81549-272">Элемент `<MsDeploySkipRules>` не удаляет *пропускаемые* целевые объекты с сайта развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-272">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="81549-273">Целевые файлы и папки, указанные в элементе `<Content>`, будут удалены с сайта развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-273">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="81549-274">Допустим, что в развернутом веб-приложении есть следующие файлы.</span><span class="sxs-lookup"><span data-stu-id="81549-274">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="81549-275">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="81549-275">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="81549-276">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="81549-276">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="81549-277">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="81549-277">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="81549-278">Если вы добавите указанные ниже элементы `<MsDeploySkipRules>`, эти файлы не будут удалены с сайта развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-278">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="81549-279">Приведенные выше элементы `<MsDeploySkipRules>` запрещают развертывание *пропускаемых* файлов.</span><span class="sxs-lookup"><span data-stu-id="81549-279">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="81549-280">Эти файлы не будут удалены, если они уже развернуты.</span><span class="sxs-lookup"><span data-stu-id="81549-280">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="81549-281">Следующий элемент `<Content>` удаляет целевые файлы на сайте развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-281">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="81549-282">Применение указанного выше элемента `<Content>` при развертывании из командной строки дает следующий результат:</span><span class="sxs-lookup"><span data-stu-id="81549-282">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="81549-283">Включаемые файлы</span><span class="sxs-lookup"><span data-stu-id="81549-283">Include files</span></span>

<span data-ttu-id="81549-284">В следующих разделах описываются различные подходы для включения файла во время публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-284">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="81549-285">В разделе [Включение общего файла](#general-file-inclusion) используется элемент `DotNetPublishFiles`, предоставляемый файлом целевых объектов публикации в [веб-пакете SDK](xref:razor-pages/web-sdk).</span><span class="sxs-lookup"><span data-stu-id="81549-285">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="81549-286">В разделе [Включение выборочного файла](#selective-file-inclusion) используется элемент `ResolvedFileToPublish`, предоставляемый файлом целевых объектов публикации в [пакете SDK для .NET Core](/dotnet/core/project-sdk/msbuild-props).</span><span class="sxs-lookup"><span data-stu-id="81549-286">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="81549-287">Поскольку веб-пакет SDK зависит от пакета SDK для .NET Core, можно использовать любой из этих элементов в проекте ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81549-287">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="81549-288">Включение общих файлов</span><span class="sxs-lookup"><span data-stu-id="81549-288">General file inclusion</span></span>

<span data-ttu-id="81549-289">Элемент `<ItemGroup>` в приведенном ниже примере демонстрирует копирование папки, расположенной за пределами папки проекта, в папку опубликованного сайта.</span><span class="sxs-lookup"><span data-stu-id="81549-289">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="81549-290">Файлы, добавленные в следующий элемент разметки `<ItemGroup>`, включены по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="81549-290">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="81549-291">Предыдущая разметка:</span><span class="sxs-lookup"><span data-stu-id="81549-291">The preceding markup:</span></span>

* <span data-ttu-id="81549-292">может размещаться в файле *.csproj* или в профиле публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-292">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="81549-293">Если добавить ее в файл *CSPROJ*, она будет включаться в каждый профиль публикации этого проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-293">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="81549-294">Объявляет элемент `_CustomFiles` для хранения файлов, соответствующих стандартной маске атрибута `Include`.</span><span class="sxs-lookup"><span data-stu-id="81549-294">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="81549-295">Папка *образов*, указанная в шаблоне, находится вне каталога проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-295">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="81549-296">[Зарезервированное свойство](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties) с именем `$(MSBuildProjectDirectory)` разрешается в абсолютный путь файла проекта.</span><span class="sxs-lookup"><span data-stu-id="81549-296">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="81549-297">Предоставляет список файлов элементу `DotNetPublishFiles`.</span><span class="sxs-lookup"><span data-stu-id="81549-297">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="81549-298">По умолчанию элемент `<DestinationRelativePath>` в элементе пуст.</span><span class="sxs-lookup"><span data-stu-id="81549-298">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="81549-299">Значение по умолчанию переопределяется в разметке и использует [стандартные метаданные элементов](/visualstudio/msbuild/msbuild-well-known-item-metadata), например `%(RecursiveDir)`.</span><span class="sxs-lookup"><span data-stu-id="81549-299">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="81549-300">Внутренний текст представляет папку *wwwroot/images* опубликованного сайта.</span><span class="sxs-lookup"><span data-stu-id="81549-300">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="81549-301">Включение выборочных файлов</span><span class="sxs-lookup"><span data-stu-id="81549-301">Selective file inclusion</span></span>

<span data-ttu-id="81549-302">Выделенная разметка в следующем примере показывает:</span><span class="sxs-lookup"><span data-stu-id="81549-302">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="81549-303">Копирование файла, расположенного вне проекта, в папку *wwwroot* опубликованного сайта.</span><span class="sxs-lookup"><span data-stu-id="81549-303">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="81549-304">Имя файла *ReadMe2.md* сохраняется.</span><span class="sxs-lookup"><span data-stu-id="81549-304">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="81549-305">Исключение папки *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="81549-305">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="81549-306">Исключение *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="81549-306">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="81549-307">В предыдущем примере используется элемент `ResolvedFileToPublish`, поведение по умолчанию которого — всегда копировать файлы, предоставленные в атрибуте `Include` опубликованному сайту.</span><span class="sxs-lookup"><span data-stu-id="81549-307">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="81549-308">Переопределите поведение по умолчанию, включив дочерний элемент `<CopyToPublishDirectory>` с внутренним текстом `Never` или `PreserveNewest`.</span><span class="sxs-lookup"><span data-stu-id="81549-308">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="81549-309">Пример:</span><span class="sxs-lookup"><span data-stu-id="81549-309">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="81549-310">Дополнительные примеры развертывания см. в [файле Readme из репозитория веб-пакета SDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="81549-310">For more deployment samples, see the [Web SDK repository Readme](https://github.com/aspnet/websdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="81549-311">Выполнение целевого объекта до или после публикации</span><span class="sxs-lookup"><span data-stu-id="81549-311">Run a target before or after publishing</span></span>

<span data-ttu-id="81549-312">Встроенные целевые объекты `BeforePublish` и `AfterPublish` позволяют выполнить целевой объект до или после его публикации.</span><span class="sxs-lookup"><span data-stu-id="81549-312">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="81549-313">Добавьте следующие элементы в профиль публикации, чтобы сохранять сообщения в консоли, возвращаемые до и после публикации:</span><span class="sxs-lookup"><span data-stu-id="81549-313">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="81549-314">Публикация на сервере с использованием сертификата без доверия</span><span class="sxs-lookup"><span data-stu-id="81549-314">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="81549-315">Добавьте свойство `<AllowUntrustedCertificate>` со значением `True` в профиль публикации:</span><span class="sxs-lookup"><span data-stu-id="81549-315">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="81549-316">Служба Kudu</span><span class="sxs-lookup"><span data-stu-id="81549-316">The Kudu service</span></span>

<span data-ttu-id="81549-317">Чтобы просмотреть файлы в развертывании веб-приложения службы приложений Azure, используйте [службу Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="81549-317">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="81549-318">Добавьте к имени веб-приложения маркер `scm`.</span><span class="sxs-lookup"><span data-stu-id="81549-318">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="81549-319">Пример:</span><span class="sxs-lookup"><span data-stu-id="81549-319">For example:</span></span>

| <span data-ttu-id="81549-320">URL-адрес</span><span class="sxs-lookup"><span data-stu-id="81549-320">URL</span></span>                                    | <span data-ttu-id="81549-321">Результат</span><span class="sxs-lookup"><span data-stu-id="81549-321">Result</span></span>       |
| ---
<span data-ttu-id="81549-322">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-323">'Blazor'</span></span>
- <span data-ttu-id="81549-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-324">'Identity'</span></span>
- <span data-ttu-id="81549-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-326">'Razor'</span></span>
- <span data-ttu-id="81549-327">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-328">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-329">'Blazor'</span></span>
- <span data-ttu-id="81549-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-330">'Identity'</span></span>
- <span data-ttu-id="81549-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-332">'Razor'</span></span>
- <span data-ttu-id="81549-333">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-334">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-335">'Blazor'</span></span>
- <span data-ttu-id="81549-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-336">'Identity'</span></span>
- <span data-ttu-id="81549-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-338">'Razor'</span></span>
- <span data-ttu-id="81549-339">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-340">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-341">'Blazor'</span></span>
- <span data-ttu-id="81549-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-342">'Identity'</span></span>
- <span data-ttu-id="81549-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-344">'Razor'</span></span>
- <span data-ttu-id="81549-345">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-346">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-347">'Blazor'</span></span>
- <span data-ttu-id="81549-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-348">'Identity'</span></span>
- <span data-ttu-id="81549-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-350">'Razor'</span></span>
- <span data-ttu-id="81549-351">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-352">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-353">'Blazor'</span></span>
- <span data-ttu-id="81549-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-354">'Identity'</span></span>
- <span data-ttu-id="81549-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-356">'Razor'</span></span>
- <span data-ttu-id="81549-357">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-358">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-359">'Blazor'</span></span>
- <span data-ttu-id="81549-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-360">'Identity'</span></span>
- <span data-ttu-id="81549-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-362">'Razor'</span></span>
- <span data-ttu-id="81549-363">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-364">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-365">'Blazor'</span></span>
- <span data-ttu-id="81549-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-366">'Identity'</span></span>
- <span data-ttu-id="81549-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-368">'Razor'</span></span>
- <span data-ttu-id="81549-369">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-370">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-371">'Blazor'</span></span>
- <span data-ttu-id="81549-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-372">'Identity'</span></span>
- <span data-ttu-id="81549-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-374">'Razor'</span></span>
- <span data-ttu-id="81549-375">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-375">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-376">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-376">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-377">'Blazor'</span></span>
- <span data-ttu-id="81549-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-378">'Identity'</span></span>
- <span data-ttu-id="81549-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-380">'Razor'</span></span>
- <span data-ttu-id="81549-381">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-382">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-382">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-383">'Blazor'</span></span>
- <span data-ttu-id="81549-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-384">'Identity'</span></span>
- <span data-ttu-id="81549-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-386">'Razor'</span></span>
- <span data-ttu-id="81549-387">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-388">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-388">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-389">'Blazor'</span></span>
- <span data-ttu-id="81549-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-390">'Identity'</span></span>
- <span data-ttu-id="81549-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-392">'Razor'</span></span>
- <span data-ttu-id="81549-393">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-394">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-394">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-395">'Blazor'</span></span>
- <span data-ttu-id="81549-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-396">'Identity'</span></span>
- <span data-ttu-id="81549-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-398">'Razor'</span></span>
- <span data-ttu-id="81549-399">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-400">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-400">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-401">'Blazor'</span></span>
- <span data-ttu-id="81549-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-402">'Identity'</span></span>
- <span data-ttu-id="81549-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-404">'Razor'</span></span>
- <span data-ttu-id="81549-405">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-406">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-407">'Blazor'</span></span>
- <span data-ttu-id="81549-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-408">'Identity'</span></span>
- <span data-ttu-id="81549-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-410">'Razor'</span></span>
- <span data-ttu-id="81549-411">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-412">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-413">'Blazor'</span></span>
- <span data-ttu-id="81549-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-414">'Identity'</span></span>
- <span data-ttu-id="81549-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-416">'Razor'</span></span>
- <span data-ttu-id="81549-417">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-418">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-418">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-419">'Blazor'</span></span>
- <span data-ttu-id="81549-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-420">'Identity'</span></span>
- <span data-ttu-id="81549-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-422">'Razor'</span></span>
- <span data-ttu-id="81549-423">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-423">'SignalR' uid:</span></span> 

<span data-ttu-id="81549-424">------------------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-424">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-425">'Blazor'</span></span>
- <span data-ttu-id="81549-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-426">'Identity'</span></span>
- <span data-ttu-id="81549-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-428">'Razor'</span></span>
- <span data-ttu-id="81549-429">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-430">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-431">'Blazor'</span></span>
- <span data-ttu-id="81549-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-432">'Identity'</span></span>
- <span data-ttu-id="81549-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-434">'Razor'</span></span>
- <span data-ttu-id="81549-435">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-436">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-436">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-437">'Blazor'</span></span>
- <span data-ttu-id="81549-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-438">'Identity'</span></span>
- <span data-ttu-id="81549-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-440">'Razor'</span></span>
- <span data-ttu-id="81549-441">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="81549-442">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="81549-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="81549-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81549-443">'Blazor'</span></span>
- <span data-ttu-id="81549-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81549-444">'Identity'</span></span>
- <span data-ttu-id="81549-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81549-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="81549-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81549-446">'Razor'</span></span>
- <span data-ttu-id="81549-447">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="81549-447">'SignalR' uid:</span></span> 

<span data-ttu-id="81549-448">------ | | `http://mysite.azurewebsites.net/`     | Веб-приложение      | | `http://mysite.scm.azurewebsites.net/` | Служба Kudu |</span><span class="sxs-lookup"><span data-stu-id="81549-448">------ | | `http://mysite.azurewebsites.net/`     | Web App      | | `http://mysite.scm.azurewebsites.net/` | Kudu service |</span></span>

<span data-ttu-id="81549-449">Для просмотра, редактирования, удаления и (или) добавления файлов выберите в меню пункт [Консоль отладки](https://github.com/projectkudu/kudu/wiki/Kudu-console).</span><span class="sxs-lookup"><span data-stu-id="81549-449">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81549-450">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="81549-450">Additional resources</span></span>

* <span data-ttu-id="81549-451">[Веб-развертывание](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) упрощает развертывание веб-приложений и веб-сайтов на серверах IIS.</span><span class="sxs-lookup"><span data-stu-id="81549-451">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="81549-452">[Репозиторий веб-пакета SDK на GitHub](https://github.com/aspnet/websdk/issues) проблемы с файлами и возможности запросов для развертывания.</span><span class="sxs-lookup"><span data-stu-id="81549-452">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="81549-453">Публикация веб-приложения ASP.NET в виртуальную машину Azure из Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81549-453">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
