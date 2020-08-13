---
title: Поставщики файлов в ASP.NET Core
author: rick-anderson
description: Сведения о том, как ASP.NET Core абстрагирует доступ к файловой системе с помощью поставщиков файлов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: e2441a56fc8b05ba16dd8e368382501ac6b268a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017445"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="822ad-103">Поставщики файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="822ad-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="822ad-104">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="822ad-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="822ad-105">ASP.NET Core абстрагирует доступ к файловой системе с помощью поставщиков файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="822ad-106">В рамках платформы ASP.NET Core используются поставщики файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="822ad-107">Пример:</span><span class="sxs-lookup"><span data-stu-id="822ad-107">For example:</span></span>

* <span data-ttu-id="822ad-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> предоставляет [корневой каталог содержимого](xref:fundamentals/index#content-root) и [корневой каталог документов](xref:fundamentals/index#web-root) приложения в виде типов `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="822ad-109">[ПО промежуточного слоя для статических файлов](xref:fundamentals/static-files) использует поставщики файлов для поиска статических файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="822ad-110">[Razor](xref:mvc/views/razor) использует поставщики файлов для поиска страниц и представлений.</span><span class="sxs-lookup"><span data-stu-id="822ad-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="822ad-111">Инструменты .NET Core используют поставщики файлов и стандартные маски, чтобы указать файлы для публикации.</span><span class="sxs-lookup"><span data-stu-id="822ad-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="822ad-112">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="822ad-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="822ad-113">Интерфейсы поставщика файлов</span><span class="sxs-lookup"><span data-stu-id="822ad-113">File Provider interfaces</span></span>

<span data-ttu-id="822ad-114">Основной интерфейс — <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="822ad-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="822ad-115">`IFileProvider` предоставляет методы для следующих действий:</span><span class="sxs-lookup"><span data-stu-id="822ad-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="822ad-116">получение сведений о файле (<xref:Microsoft.Extensions.FileProviders.IFileInfo>);</span><span class="sxs-lookup"><span data-stu-id="822ad-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="822ad-117">получение сведений о каталоге (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>);</span><span class="sxs-lookup"><span data-stu-id="822ad-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="822ad-118">настройка уведомлений об изменениях (с помощью <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="822ad-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="822ad-119">`IFileInfo` предоставляет методы и свойства для работы с файлами:</span><span class="sxs-lookup"><span data-stu-id="822ad-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="822ad-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (в байтах);</span><span class="sxs-lookup"><span data-stu-id="822ad-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="822ad-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> (дата).</span><span class="sxs-lookup"><span data-stu-id="822ad-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="822ad-122">Считывать данные из файла можно с помощью метода <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="822ad-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="822ad-123">Пример приложения *FileProviderSample* демонстрирует, как настроить поставщик файлов в `Startup.ConfigureServices`, чтобы использовать его в приложении путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="822ad-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="822ad-124">Реализации поставщиков файлов</span><span class="sxs-lookup"><span data-stu-id="822ad-124">File Provider implementations</span></span>

<span data-ttu-id="822ad-125">В следующей таблице перечислены реализации `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="822ad-126">Реализация</span><span class="sxs-lookup"><span data-stu-id="822ad-126">Implementation</span></span> | <span data-ttu-id="822ad-127">Описание</span><span class="sxs-lookup"><span data-stu-id="822ad-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="822ad-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="822ad-129">Используется для предоставления комбинированного доступа к файлам и каталогам из одного или нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="822ad-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="822ad-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="822ad-131">Используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="822ad-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="822ad-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="822ad-133">Используется для доступа к физическим файлам системы.</span><span class="sxs-lookup"><span data-stu-id="822ad-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="822ad-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-134">PhysicalFileProvider</span></span>

<span data-ttu-id="822ad-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> предоставляет доступ к физической файловой системе.</span><span class="sxs-lookup"><span data-stu-id="822ad-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="822ad-136">`PhysicalFileProvider` использует тип <xref:System.IO.File?displayProperty=fullName> (для физического поставщика), ограничивая все пути каталогом и его дочерними элементами.</span><span class="sxs-lookup"><span data-stu-id="822ad-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="822ad-137">Такая привязка к области защищает от доступа к файловой системе за пределами указанного каталога и его дочерних элементов.</span><span class="sxs-lookup"><span data-stu-id="822ad-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="822ad-138">Самый распространенный подход к созданию и использованию `PhysicalFileProvider` — запросить `IFileProvider` в конструкторе путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="822ad-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="822ad-139">При непосредственном создании экземпляра этого поставщика нужно указать абсолютный путь к каталогу, который станет базовым путем для всех запросов, выполненных с помощью этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="822ad-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="822ad-140">Стандартные маски не поддерживаются в пути к каталогу.</span><span class="sxs-lookup"><span data-stu-id="822ad-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="822ad-141">Следующий код демонстрирует, как использовать `PhysicalFileProvider` для получения содержимого каталога и сведений о файлах:</span><span class="sxs-lookup"><span data-stu-id="822ad-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="822ad-142">В предшествующем примере используются следующие типы:</span><span class="sxs-lookup"><span data-stu-id="822ad-142">Types in the preceding example:</span></span>

* <span data-ttu-id="822ad-143">`provider` является `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="822ad-144">`contents` является `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="822ad-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="822ad-145">`fileInfo` является `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="822ad-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="822ad-146">С помощью поставщика файлов вы можете выполнить итерацию по каталогу, указанному в параметре `applicationRoot`, или вызвать `GetFileInfo` для получения сведений о файлах.</span><span class="sxs-lookup"><span data-stu-id="822ad-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="822ad-147">Стандартные маски невозможно передать методу `GetFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="822ad-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="822ad-148">Поставщик файлов не имеет доступ к каталогам за пределами `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="822ad-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="822ad-149">Пример приложения *FileProviderSample* создает поставщик в методе `Startup.ConfigureServices` с помощью <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="822ad-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="822ad-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="822ad-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="822ad-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="822ad-152">`ManifestEmbeddedFileProvider` использует манифест, скомпилированный в сборку, для воссоздания исходных путей для внедренных файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="822ad-153">Чтобы создать манифест для внедренных файлов, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="822ad-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="822ad-154">Добавьте в проект пакет NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded).</span><span class="sxs-lookup"><span data-stu-id="822ad-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="822ad-155">Задайте для свойства `<GenerateEmbeddedFilesManifest>` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="822ad-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="822ad-156">Укажите файлы для внедрения с [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="822ad-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="822ad-157">Используйте [стандартные маски](#glob-patterns) для указания одного или нескольких файлов, которые вы хотите внедрить в сборку.</span><span class="sxs-lookup"><span data-stu-id="822ad-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="822ad-158">Пример приложения *FileProviderSample* создает `ManifestEmbeddedFileProvider` и передает в соответствующий конструктор текущую выполняемую сборку.</span><span class="sxs-lookup"><span data-stu-id="822ad-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="822ad-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="822ad-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="822ad-160">Дополнительные перегрузки позволяют сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="822ad-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="822ad-161">указать относительный путь к файлу;</span><span class="sxs-lookup"><span data-stu-id="822ad-161">Specify a relative file path.</span></span>
* <span data-ttu-id="822ad-162">ограничить файлы по дате последнего изменения;</span><span class="sxs-lookup"><span data-stu-id="822ad-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="822ad-163">указать имя внедренного ресурса, содержащего внедренный файл манифеста.</span><span class="sxs-lookup"><span data-stu-id="822ad-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="822ad-164">Перегрузка</span><span class="sxs-lookup"><span data-stu-id="822ad-164">Overload</span></span> | <span data-ttu-id="822ad-165">Описание</span><span class="sxs-lookup"><span data-stu-id="822ad-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="822ad-166">Принимает необязательный параметр `root` со значением относительного пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="822ad-167">Укажите `root`, чтобы ограничить вызовы к <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> только ресурсами по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="822ad-168">Принимает необязательный параметр `root` со значением относительного пути и параметр `lastModified` со значением даты (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="822ad-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="822ad-169">Параметр `lastModified` ограничивает дату последнего изменения для экземпляров <xref:Microsoft.Extensions.FileProviders.IFileInfo>, возвращаемых функцией <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="822ad-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="822ad-170">Принимает необязательный параметр `root` со значением относительного пути, дату `lastModified` и параметры `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="822ad-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="822ad-171">`manifestName` здесь представляет имя встроенного ресурса, содержащего манифест.</span><span class="sxs-lookup"><span data-stu-id="822ad-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="822ad-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-172">CompositeFileProvider</span></span>

<span data-ttu-id="822ad-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> объединяет экземпляры `IFileProvider`, предоставляя единый интерфейс для работы с файлами от нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="822ad-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="822ad-174">При создании `CompositeFileProvider` передайте в соответствующий конструктор один или несколько экземпляров `IFileProvider`:</span><span class="sxs-lookup"><span data-stu-id="822ad-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="822ad-175">В примере приложения *FileProviderSample* `PhysicalFileProvider` и `ManifestEmbeddedFileProvider` предоставляют файлы для `CompositeFileProvider` с регистрацией в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="822ad-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="822ad-176">Следующий код находится в методе `Startup.ConfigureServices` проекта:</span><span class="sxs-lookup"><span data-stu-id="822ad-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="822ad-177">Отслеживание изменений</span><span class="sxs-lookup"><span data-stu-id="822ad-177">Watch for changes</span></span>

<span data-ttu-id="822ad-178">Метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> позволяет просматривать один или несколько файлов или каталогов на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="822ad-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="822ad-179">Метод `Watch`:</span><span class="sxs-lookup"><span data-stu-id="822ad-179">The `Watch` method:</span></span>

* <span data-ttu-id="822ad-180">Принимает строку пути, которая может использовать [стандартные маски](#glob-patterns) для указания нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="822ad-181">Возвращает значение типа <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="822ad-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="822ad-182">Итоговый токен изменений предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="822ad-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="822ad-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>. свойство, которое можно проверить, чтобы определить, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="822ad-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="822ad-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>. вызывается при обнаружении изменений по указанной строке пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="822ad-185">Каждый токен изменения выполняет соответствующий обратный вызов только в ответ на отдельное изменение.</span><span class="sxs-lookup"><span data-stu-id="822ad-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="822ad-186">Чтобы реализовать непрерывный мониторинг, используйте <xref:System.Threading.Tasks.TaskCompletionSource`1>, как показано ниже, или повторно создавайте экземпляры `IChangeToken` в ответ на изменения.</span><span class="sxs-lookup"><span data-stu-id="822ad-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="822ad-187">Пример приложения *WatchConsole* записывает сообщение при каждом изменении файла *TXT* в каталоге *TextFiles*:</span><span class="sxs-lookup"><span data-stu-id="822ad-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="822ad-188">Некоторые файловые системы, такие как контейнеры Docker и сетевые папки, не могут надежно отправлять уведомления об изменениях.</span><span class="sxs-lookup"><span data-stu-id="822ad-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="822ad-189">Задайте для переменной среды `DOTNET_USE_POLLING_FILE_WATCHER` значение `1` или `true`, чтобы опрашивать файловую систему на предмет изменений каждые 4 секунды (это значение нельзя изменить).</span><span class="sxs-lookup"><span data-stu-id="822ad-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="822ad-190">Стандартные маски</span><span class="sxs-lookup"><span data-stu-id="822ad-190">Glob patterns</span></span>

<span data-ttu-id="822ad-191">В путях файловой системы используются шаблоны подстановочных знаков, которые называются *стандартными масками*.</span><span class="sxs-lookup"><span data-stu-id="822ad-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="822ad-192">Эти маски позволяют указывать группы файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="822ad-193">Поддерживаются два подстановочных знака — `*` и `**`.</span><span class="sxs-lookup"><span data-stu-id="822ad-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="822ad-194">Совпадает с любым элементом на текущем уровне папок, любым именем или расширением файла.</span><span class="sxs-lookup"><span data-stu-id="822ad-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="822ad-195">Совпадения завершаются символами `/` и `.` в пути файла.</span><span class="sxs-lookup"><span data-stu-id="822ad-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="822ad-196">Совпадает со всем содержимым на разных уровнях каталогов.</span><span class="sxs-lookup"><span data-stu-id="822ad-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="822ad-197">Может использоваться для рекурсивного сопоставления множества файлов в иерархии каталогов.</span><span class="sxs-lookup"><span data-stu-id="822ad-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="822ad-198">Следующая таблица содержит типичные примеры стандартных масок.</span><span class="sxs-lookup"><span data-stu-id="822ad-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="822ad-199">Шаблон</span><span class="sxs-lookup"><span data-stu-id="822ad-199">Pattern</span></span>  |<span data-ttu-id="822ad-200">Описание</span><span class="sxs-lookup"><span data-stu-id="822ad-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="822ad-201">Соответствует конкретному файлу в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="822ad-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="822ad-202">Соответствует всем файлам с расширением *.txt* в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="822ad-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="822ad-203">Соответствует всем файлам *appsettings.json* в любом каталоге, расположенном ровно на один уровень ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="822ad-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="822ad-204">Соответствует всем файлам с расширением *TXT*, находящимся на любом уровне ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="822ad-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="822ad-205">ASP.NET Core абстрагирует доступ к файловой системе с помощью поставщиков файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="822ad-206">В рамках платформы ASP.NET Core используются поставщики файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="822ad-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет [корневой каталог содержимого](xref:fundamentals/index#content-root) и [корневой каталог документов](xref:fundamentals/index#web-root) приложения в виде типов `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="822ad-208">[ПО промежуточного слоя для статических файлов](xref:fundamentals/static-files) использует поставщики файлов для поиска статических файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="822ad-209">[Razor](xref:mvc/views/razor) использует поставщики файлов для поиска страниц и представлений.</span><span class="sxs-lookup"><span data-stu-id="822ad-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="822ad-210">Инструменты .NET Core используют поставщики файлов и стандартные маски, чтобы указать файлы для публикации.</span><span class="sxs-lookup"><span data-stu-id="822ad-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="822ad-211">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="822ad-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="822ad-212">Интерфейсы поставщика файлов</span><span class="sxs-lookup"><span data-stu-id="822ad-212">File Provider interfaces</span></span>

<span data-ttu-id="822ad-213">Основной интерфейс — <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="822ad-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="822ad-214">`IFileProvider` предоставляет методы для следующих действий:</span><span class="sxs-lookup"><span data-stu-id="822ad-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="822ad-215">получение сведений о файле (<xref:Microsoft.Extensions.FileProviders.IFileInfo>);</span><span class="sxs-lookup"><span data-stu-id="822ad-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="822ad-216">получение сведений о каталоге (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>);</span><span class="sxs-lookup"><span data-stu-id="822ad-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="822ad-217">настройка уведомлений об изменениях (с помощью <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="822ad-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="822ad-218">`IFileInfo` предоставляет методы и свойства для работы с файлами:</span><span class="sxs-lookup"><span data-stu-id="822ad-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="822ad-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (в байтах);</span><span class="sxs-lookup"><span data-stu-id="822ad-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="822ad-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> (дата).</span><span class="sxs-lookup"><span data-stu-id="822ad-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="822ad-221">Данные из файла можно считывать с помощью метода [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="822ad-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="822ad-222">Пример приложения демонстрирует, как настроить в `Startup.ConfigureServices` поставщике файлов, чтобы использовать его в приложении путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="822ad-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="822ad-223">Реализации поставщиков файлов</span><span class="sxs-lookup"><span data-stu-id="822ad-223">File Provider implementations</span></span>

<span data-ttu-id="822ad-224">Доступны три реализации `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="822ad-225">Реализация</span><span class="sxs-lookup"><span data-stu-id="822ad-225">Implementation</span></span> | <span data-ttu-id="822ad-226">Описание</span><span class="sxs-lookup"><span data-stu-id="822ad-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="822ad-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="822ad-228">Физический поставщик используется для доступа к физическим файлам системы.</span><span class="sxs-lookup"><span data-stu-id="822ad-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="822ad-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="822ad-230">Поставщик внедренных манифестов используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="822ad-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="822ad-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="822ad-232">Составной поставщик используется для предоставления комбинированного доступа к файлам и каталогам из одного или нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="822ad-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="822ad-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-233">PhysicalFileProvider</span></span>

<span data-ttu-id="822ad-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> предоставляет доступ к физической файловой системе.</span><span class="sxs-lookup"><span data-stu-id="822ad-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="822ad-235">`PhysicalFileProvider` использует тип <xref:System.IO.File?displayProperty=fullName> (для физического поставщика), ограничивая все пути каталогом и его дочерними элементами.</span><span class="sxs-lookup"><span data-stu-id="822ad-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="822ad-236">Такая привязка к области защищает от доступа к файловой системе за пределами указанного каталога и его дочерних элементов.</span><span class="sxs-lookup"><span data-stu-id="822ad-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="822ad-237">Самый распространенный подход к созданию и использованию `PhysicalFileProvider` — запросить `IFileProvider` в конструкторе путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="822ad-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="822ad-238">При непосредственном создании экземпляра этого поставщика нужно указать путь к каталогу, который станет базовым путем для всех запросов, выполненных с помощью этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="822ad-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="822ad-239">Следующий пример кода демонстрирует, как создать и использовать `PhysicalFileProvider` для получения содержимого каталога и сведений о файлах:</span><span class="sxs-lookup"><span data-stu-id="822ad-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="822ad-240">В предшествующем примере используются следующие типы:</span><span class="sxs-lookup"><span data-stu-id="822ad-240">Types in the preceding example:</span></span>

* <span data-ttu-id="822ad-241">`provider` является `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="822ad-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="822ad-242">`contents` является `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="822ad-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="822ad-243">`fileInfo` является `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="822ad-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="822ad-244">С помощью поставщика файлов вы можете выполнить итерацию по каталогу, указанному в параметре `applicationRoot`, или вызвать `GetFileInfo` для получения сведений о файлах.</span><span class="sxs-lookup"><span data-stu-id="822ad-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="822ad-245">Поставщик файлов не имеет доступ к каталогам за пределами `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="822ad-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="822ad-246">Этот пример приложения создает поставщик для приложения в классе `Startup.ConfigureServices`, используя [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="822ad-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="822ad-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="822ad-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="822ad-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="822ad-249">`ManifestEmbeddedFileProvider` использует манифест, скомпилированный в сборку, для воссоздания исходных путей для внедренных файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="822ad-250">Чтобы создать манифест для внедренных файлов, задайте для свойства `<GenerateEmbeddedFilesManifest>` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="822ad-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="822ad-251">Выберите файлы для внедрения с помощью [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="822ad-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="822ad-252">Используйте [стандартные маски](#glob-patterns) для указания одного или нескольких файлов, которые вы хотите внедрить в сборку.</span><span class="sxs-lookup"><span data-stu-id="822ad-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="822ad-253">Наш пример приложения создает `ManifestEmbeddedFileProvider` и передает в соответствующий конструктор текущую выполняемую сборку.</span><span class="sxs-lookup"><span data-stu-id="822ad-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="822ad-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="822ad-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="822ad-255">Дополнительные перегрузки позволяют сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="822ad-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="822ad-256">указать относительный путь к файлу;</span><span class="sxs-lookup"><span data-stu-id="822ad-256">Specify a relative file path.</span></span>
* <span data-ttu-id="822ad-257">ограничить файлы по дате последнего изменения;</span><span class="sxs-lookup"><span data-stu-id="822ad-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="822ad-258">указать имя внедренного ресурса, содержащего внедренный файл манифеста.</span><span class="sxs-lookup"><span data-stu-id="822ad-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="822ad-259">Перегрузка</span><span class="sxs-lookup"><span data-stu-id="822ad-259">Overload</span></span> | <span data-ttu-id="822ad-260">Описание</span><span class="sxs-lookup"><span data-stu-id="822ad-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="822ad-261">Принимает необязательный параметр `root` со значением относительного пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="822ad-262">Укажите `root`, чтобы ограничить вызовы к <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> только ресурсами по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="822ad-263">Принимает необязательный параметр `root` со значением относительного пути и параметр `lastModified` со значением даты (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="822ad-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="822ad-264">Параметр `lastModified` ограничивает дату последнего изменения для экземпляров <xref:Microsoft.Extensions.FileProviders.IFileInfo>, возвращаемых функцией <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="822ad-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="822ad-265">Принимает необязательный параметр `root` со значением относительного пути, дату `lastModified` и параметры `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="822ad-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="822ad-266">`manifestName` здесь представляет имя встроенного ресурса, содержащего манифест.</span><span class="sxs-lookup"><span data-stu-id="822ad-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="822ad-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="822ad-267">CompositeFileProvider</span></span>

<span data-ttu-id="822ad-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> объединяет экземпляры `IFileProvider`, предоставляя единый интерфейс для работы с файлами от нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="822ad-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="822ad-269">При создании `CompositeFileProvider` передайте в соответствующий конструктор один или несколько экземпляров `IFileProvider`:</span><span class="sxs-lookup"><span data-stu-id="822ad-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="822ad-270">В нашем примере приложения `PhysicalFileProvider` и `ManifestEmbeddedFileProvider` предоставляют файлы для `CompositeFileProvider` с регистрацией в контейнере служб приложения:</span><span class="sxs-lookup"><span data-stu-id="822ad-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="822ad-271">Отслеживание изменений</span><span class="sxs-lookup"><span data-stu-id="822ad-271">Watch for changes</span></span>

<span data-ttu-id="822ad-272">Метод [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) позволяет контролировать изменения в одном или нескольких файлах или каталогах.</span><span class="sxs-lookup"><span data-stu-id="822ad-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="822ad-273">`Watch` принимает строку пути, которая можно использовать [стандартные маски](#glob-patterns) для указания нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="822ad-274">`Watch` возвращает <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="822ad-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="822ad-275">Этот токен изменений предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="822ad-275">The change token exposes:</span></span>

* <span data-ttu-id="822ad-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>. свойство, которое можно проверить, чтобы определить, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="822ad-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="822ad-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>. вызывается при обнаружении изменений по указанной строке пути.</span><span class="sxs-lookup"><span data-stu-id="822ad-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="822ad-278">Каждый токен изменения выполняет соответствующий обратный вызов только в ответ на отдельное изменение.</span><span class="sxs-lookup"><span data-stu-id="822ad-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="822ad-279">Чтобы реализовать непрерывный мониторинг, используйте <xref:System.Threading.Tasks.TaskCompletionSource`1>, как показано ниже, или повторно создавайте экземпляры `IChangeToken` в ответ на изменения.</span><span class="sxs-lookup"><span data-stu-id="822ad-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="822ad-280">В нашем примере консольное приложение *WatchConsole* будет отображать сообщение при изменении текстового файла:</span><span class="sxs-lookup"><span data-stu-id="822ad-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="822ad-281">Некоторые файловые системы, такие как контейнеры Docker и сетевые папки, не могут надежно отправлять уведомления об изменениях.</span><span class="sxs-lookup"><span data-stu-id="822ad-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="822ad-282">Задайте для переменной среды `DOTNET_USE_POLLING_FILE_WATCHER` значение `1` или `true`, чтобы опрашивать файловую систему на предмет изменений каждые 4 секунды (это значение нельзя изменить).</span><span class="sxs-lookup"><span data-stu-id="822ad-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="822ad-283">Стандартные маски</span><span class="sxs-lookup"><span data-stu-id="822ad-283">Glob patterns</span></span>

<span data-ttu-id="822ad-284">В путях файловой системы используются шаблоны подстановочных знаков, которые называются *стандартными масками*.</span><span class="sxs-lookup"><span data-stu-id="822ad-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="822ad-285">Эти маски позволяют указывать группы файлов.</span><span class="sxs-lookup"><span data-stu-id="822ad-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="822ad-286">Поддерживаются два подстановочных знака — `*` и `**`.</span><span class="sxs-lookup"><span data-stu-id="822ad-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="822ad-287">Совпадает с любым элементом на текущем уровне папок, любым именем или расширением файла.</span><span class="sxs-lookup"><span data-stu-id="822ad-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="822ad-288">Совпадения завершаются символами `/` и `.` в пути файла.</span><span class="sxs-lookup"><span data-stu-id="822ad-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="822ad-289">Совпадает со всем содержимым на разных уровнях каталогов.</span><span class="sxs-lookup"><span data-stu-id="822ad-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="822ad-290">Может использоваться для рекурсивного сопоставления множества файлов в иерархии каталогов.</span><span class="sxs-lookup"><span data-stu-id="822ad-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="822ad-291">**Примеры стандартных масок**</span><span class="sxs-lookup"><span data-stu-id="822ad-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="822ad-292">Соответствует конкретному файлу в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="822ad-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="822ad-293">Соответствует всем файлам с расширением *.txt* в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="822ad-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="822ad-294">Соответствует всем файлам `appsettings.json` в любом каталоге, расположенном ровно на один уровень ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="822ad-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="822ad-295">Соответствует всем файлам с расширением *.txt*, находящимся на любом уровне ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="822ad-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
