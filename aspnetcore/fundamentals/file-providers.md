---
<span data-ttu-id="18551-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-102">'Blazor'</span></span>
- <span data-ttu-id="18551-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-103">'Identity'</span></span>
- <span data-ttu-id="18551-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-105">'Razor'</span></span>
- <span data-ttu-id="18551-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="18551-107">Поставщики файлов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18551-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="18551-108">Автор: [Стив Смит](https://ardalis.com/) (Steve Smith)</span><span class="sxs-lookup"><span data-stu-id="18551-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18551-109">ASP.NET Core абстрагирует доступ к файловой системе с помощью поставщиков файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="18551-110">В рамках платформы ASP.NET Core используются поставщики файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="18551-111">Пример:</span><span class="sxs-lookup"><span data-stu-id="18551-111">For example:</span></span>

* <span data-ttu-id="18551-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> предоставляет [корневой каталог содержимого](xref:fundamentals/index#content-root) и [корневой каталог документов](xref:fundamentals/index#web-root) приложения в виде типов `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="18551-113">[ПО промежуточного слоя для статических файлов](xref:fundamentals/static-files) использует поставщики файлов для поиска статических файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="18551-114">[Razor](xref:mvc/views/razor) использует поставщики файлов для поиска страниц и представлений.</span><span class="sxs-lookup"><span data-stu-id="18551-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="18551-115">Инструменты .NET Core используют поставщики файлов и стандартные маски, чтобы указать файлы для публикации.</span><span class="sxs-lookup"><span data-stu-id="18551-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="18551-116">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18551-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="18551-117">Интерфейсы поставщика файлов</span><span class="sxs-lookup"><span data-stu-id="18551-117">File Provider interfaces</span></span>

<span data-ttu-id="18551-118">Основной интерфейс — <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="18551-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="18551-119">`IFileProvider` предоставляет методы для следующих действий:</span><span class="sxs-lookup"><span data-stu-id="18551-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="18551-120">получение сведений о файле (<xref:Microsoft.Extensions.FileProviders.IFileInfo>);</span><span class="sxs-lookup"><span data-stu-id="18551-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="18551-121">получение сведений о каталоге (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>);</span><span class="sxs-lookup"><span data-stu-id="18551-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="18551-122">настройка уведомлений об изменениях (с помощью <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="18551-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="18551-123">`IFileInfo` предоставляет методы и свойства для работы с файлами:</span><span class="sxs-lookup"><span data-stu-id="18551-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="18551-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (в байтах);</span><span class="sxs-lookup"><span data-stu-id="18551-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="18551-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> (дата).</span><span class="sxs-lookup"><span data-stu-id="18551-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="18551-126">Считывать данные из файла можно с помощью метода <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="18551-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="18551-127">Пример приложения *FileProviderSample* демонстрирует, как настроить поставщик файлов в `Startup.ConfigureServices`, чтобы использовать его в приложении путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18551-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="18551-128">Реализации поставщиков файлов</span><span class="sxs-lookup"><span data-stu-id="18551-128">File Provider implementations</span></span>

<span data-ttu-id="18551-129">В следующей таблице перечислены реализации `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="18551-130">Реализация</span><span class="sxs-lookup"><span data-stu-id="18551-130">Implementation</span></span> | <span data-ttu-id="18551-131">Описание</span><span class="sxs-lookup"><span data-stu-id="18551-131">Description</span></span> |
| ---
<span data-ttu-id="18551-132">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-133">'Blazor'</span></span>
- <span data-ttu-id="18551-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-134">'Identity'</span></span>
- <span data-ttu-id="18551-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-136">'Razor'</span></span>
- <span data-ttu-id="18551-137">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-138">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-139">'Blazor'</span></span>
- <span data-ttu-id="18551-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-140">'Identity'</span></span>
- <span data-ttu-id="18551-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-142">'Razor'</span></span>
- <span data-ttu-id="18551-143">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-144">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-145">'Blazor'</span></span>
- <span data-ttu-id="18551-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-146">'Identity'</span></span>
- <span data-ttu-id="18551-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-148">'Razor'</span></span>
- <span data-ttu-id="18551-149">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-150">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-151">'Blazor'</span></span>
- <span data-ttu-id="18551-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-152">'Identity'</span></span>
- <span data-ttu-id="18551-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-154">'Razor'</span></span>
- <span data-ttu-id="18551-155">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-156">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-157">'Blazor'</span></span>
- <span data-ttu-id="18551-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-158">'Identity'</span></span>
- <span data-ttu-id="18551-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-160">'Razor'</span></span>
- <span data-ttu-id="18551-161">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-161">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-162">------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-163">'Blazor'</span></span>
- <span data-ttu-id="18551-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-164">'Identity'</span></span>
- <span data-ttu-id="18551-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-166">'Razor'</span></span>
- <span data-ttu-id="18551-167">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-168">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-169">'Blazor'</span></span>
- <span data-ttu-id="18551-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-170">'Identity'</span></span>
- <span data-ttu-id="18551-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-172">'Razor'</span></span>
- <span data-ttu-id="18551-173">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-174">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-175">'Blazor'</span></span>
- <span data-ttu-id="18551-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-176">'Identity'</span></span>
- <span data-ttu-id="18551-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-178">'Razor'</span></span>
- <span data-ttu-id="18551-179">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-179">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-180">------ | | [CompositeFileProvider](#compositefileprovider) | Используется для предоставления комбинированного доступа к файлам и каталогам из одного или нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="18551-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="18551-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="18551-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="18551-182">| | [PhysicalFileProvider](#physicalfileprovider) | Физический поставщик используется для доступа к физическим файлам системы.</span><span class="sxs-lookup"><span data-stu-id="18551-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="18551-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-183">PhysicalFileProvider</span></span>

<span data-ttu-id="18551-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> предоставляет доступ к физической файловой системе.</span><span class="sxs-lookup"><span data-stu-id="18551-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="18551-185">`PhysicalFileProvider` использует тип <xref:System.IO.File?displayProperty=fullName> (для физического поставщика), ограничивая все пути каталогом и его дочерними элементами.</span><span class="sxs-lookup"><span data-stu-id="18551-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="18551-186">Такая привязка к области защищает от доступа к файловой системе за пределами указанного каталога и его дочерних элементов.</span><span class="sxs-lookup"><span data-stu-id="18551-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="18551-187">Самый распространенный подход к созданию и использованию `PhysicalFileProvider` — запросить `IFileProvider` в конструкторе путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18551-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="18551-188">При непосредственном создании экземпляра этого поставщика нужно указать абсолютный путь к каталогу, который станет базовым путем для всех запросов, выполненных с помощью этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="18551-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="18551-189">Стандартные маски не поддерживаются в пути к каталогу.</span><span class="sxs-lookup"><span data-stu-id="18551-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="18551-190">Следующий код демонстрирует, как использовать `PhysicalFileProvider` для получения содержимого каталога и сведений о файлах:</span><span class="sxs-lookup"><span data-stu-id="18551-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="18551-191">В предшествующем примере используются следующие типы:</span><span class="sxs-lookup"><span data-stu-id="18551-191">Types in the preceding example:</span></span>

* <span data-ttu-id="18551-192">`provider` является `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="18551-193">`contents` является `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="18551-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="18551-194">`fileInfo` является `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="18551-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="18551-195">С помощью поставщика файлов вы можете выполнить итерацию по каталогу, указанному в параметре `applicationRoot`, или вызвать `GetFileInfo` для получения сведений о файлах.</span><span class="sxs-lookup"><span data-stu-id="18551-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="18551-196">Стандартные маски невозможно передать методу `GetFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="18551-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="18551-197">Поставщик файлов не имеет доступ к каталогам за пределами `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="18551-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="18551-198">Пример приложения *FileProviderSample* создает поставщик в методе `Startup.ConfigureServices` с помощью <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="18551-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="18551-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="18551-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="18551-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="18551-201">`ManifestEmbeddedFileProvider` использует манифест, скомпилированный в сборку, для воссоздания исходных путей для внедренных файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="18551-202">Чтобы создать манифест для внедренных файлов, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="18551-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="18551-203">Добавьте в проект пакет NuGet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded).</span><span class="sxs-lookup"><span data-stu-id="18551-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="18551-204">Задайте для свойства `<GenerateEmbeddedFilesManifest>` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="18551-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="18551-205">Укажите файлы для внедрения с [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="18551-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="18551-206">Используйте [стандартные маски](#glob-patterns) для указания одного или нескольких файлов, которые вы хотите внедрить в сборку.</span><span class="sxs-lookup"><span data-stu-id="18551-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="18551-207">Пример приложения *FileProviderSample* создает `ManifestEmbeddedFileProvider` и передает в соответствующий конструктор текущую выполняемую сборку.</span><span class="sxs-lookup"><span data-stu-id="18551-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="18551-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18551-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="18551-209">Дополнительные перегрузки позволяют сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="18551-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="18551-210">указать относительный путь к файлу;</span><span class="sxs-lookup"><span data-stu-id="18551-210">Specify a relative file path.</span></span>
* <span data-ttu-id="18551-211">ограничить файлы по дате последнего изменения;</span><span class="sxs-lookup"><span data-stu-id="18551-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="18551-212">указать имя внедренного ресурса, содержащего внедренный файл манифеста.</span><span class="sxs-lookup"><span data-stu-id="18551-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="18551-213">Перегрузка</span><span class="sxs-lookup"><span data-stu-id="18551-213">Overload</span></span> | <span data-ttu-id="18551-214">Описание</span><span class="sxs-lookup"><span data-stu-id="18551-214">Description</span></span> |
| ---
<span data-ttu-id="18551-215">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-216">'Blazor'</span></span>
- <span data-ttu-id="18551-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-217">'Identity'</span></span>
- <span data-ttu-id="18551-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-219">'Razor'</span></span>
- <span data-ttu-id="18551-220">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-221">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-222">'Blazor'</span></span>
- <span data-ttu-id="18551-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-223">'Identity'</span></span>
- <span data-ttu-id="18551-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-225">'Razor'</span></span>
- <span data-ttu-id="18551-226">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-226">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-227">---- | --- название: автор: описание:  monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-228">'Blazor'</span></span>
- <span data-ttu-id="18551-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-229">'Identity'</span></span>
- <span data-ttu-id="18551-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-231">'Razor'</span></span>
- <span data-ttu-id="18551-232">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-233">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-234">'Blazor'</span></span>
- <span data-ttu-id="18551-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-235">'Identity'</span></span>
- <span data-ttu-id="18551-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-237">'Razor'</span></span>
- <span data-ttu-id="18551-238">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-239">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-240">'Blazor'</span></span>
- <span data-ttu-id="18551-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-241">'Identity'</span></span>
- <span data-ttu-id="18551-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-243">'Razor'</span></span>
- <span data-ttu-id="18551-244">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-244">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Принимает необязательный параметр `root` со значением относительного пути.</span><span class="sxs-lookup"><span data-stu-id="18551-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="18551-246">Укажите `root`, чтобы ограничить вызовы к <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> только ресурсами по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="18551-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="18551-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Принимает необязательный параметр `root` со значением относительного пути и параметр `lastModified` со значением даты (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="18551-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="18551-248">Параметр `lastModified` ограничивает дату последнего изменения для экземпляров <xref:Microsoft.Extensions.FileProviders.IFileInfo>, возвращаемых функцией <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="18551-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="18551-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Принимает необязательный параметр `root` со значением относительного пути, дату `lastModified` и параметры `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="18551-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="18551-250">`manifestName` здесь представляет имя встроенного ресурса, содержащего манифест.</span><span class="sxs-lookup"><span data-stu-id="18551-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="18551-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-251">CompositeFileProvider</span></span>

<span data-ttu-id="18551-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> объединяет экземпляры `IFileProvider`, предоставляя единый интерфейс для работы с файлами от нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="18551-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="18551-253">При создании `CompositeFileProvider` передайте в соответствующий конструктор один или несколько экземпляров `IFileProvider`:</span><span class="sxs-lookup"><span data-stu-id="18551-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="18551-254">В примере приложения *FileProviderSample* `PhysicalFileProvider` и `ManifestEmbeddedFileProvider` предоставляют файлы для `CompositeFileProvider` с регистрацией в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="18551-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="18551-255">Следующий код находится в методе `Startup.ConfigureServices` проекта:</span><span class="sxs-lookup"><span data-stu-id="18551-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="18551-256">Отслеживание изменений</span><span class="sxs-lookup"><span data-stu-id="18551-256">Watch for changes</span></span>

<span data-ttu-id="18551-257">Метод <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> позволяет просматривать один или несколько файлов или каталогов на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="18551-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="18551-258">Метод `Watch`:</span><span class="sxs-lookup"><span data-stu-id="18551-258">The `Watch` method:</span></span>

* <span data-ttu-id="18551-259">Принимает строку пути, которая может использовать [стандартные маски](#glob-patterns) для указания нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="18551-260">Возвращает значение типа <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="18551-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="18551-261">Итоговый токен изменений предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="18551-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="18551-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>. свойство, которое можно проверить, чтобы определить, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="18551-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="18551-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>. вызывается при обнаружении изменений по указанной строке пути.</span><span class="sxs-lookup"><span data-stu-id="18551-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="18551-264">Каждый токен изменения выполняет соответствующий обратный вызов только в ответ на отдельное изменение.</span><span class="sxs-lookup"><span data-stu-id="18551-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="18551-265">Чтобы реализовать непрерывный мониторинг, используйте <xref:System.Threading.Tasks.TaskCompletionSource`1>, как показано ниже, или повторно создавайте экземпляры `IChangeToken` в ответ на изменения.</span><span class="sxs-lookup"><span data-stu-id="18551-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="18551-266">Пример приложения *WatchConsole* записывает сообщение при каждом изменении файла *TXT* в каталоге *TextFiles*:</span><span class="sxs-lookup"><span data-stu-id="18551-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="18551-267">Некоторые файловые системы, такие как контейнеры Docker и сетевые папки, не могут надежно отправлять уведомления об изменениях.</span><span class="sxs-lookup"><span data-stu-id="18551-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="18551-268">Задайте для переменной среды `DOTNET_USE_POLLING_FILE_WATCHER` значение `1` или `true`, чтобы опрашивать файловую систему на предмет изменений каждые 4 секунды (это значение нельзя изменить).</span><span class="sxs-lookup"><span data-stu-id="18551-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="18551-269">Стандартные маски</span><span class="sxs-lookup"><span data-stu-id="18551-269">Glob patterns</span></span>

<span data-ttu-id="18551-270">В путях файловой системы используются шаблоны подстановочных знаков, которые называются *стандартными масками*.</span><span class="sxs-lookup"><span data-stu-id="18551-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="18551-271">Эти маски позволяют указывать группы файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="18551-272">Поддерживаются два подстановочных знака — `*` и `**`.</span><span class="sxs-lookup"><span data-stu-id="18551-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="18551-273">Совпадает с любым элементом на текущем уровне папок, любым именем или расширением файла.</span><span class="sxs-lookup"><span data-stu-id="18551-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="18551-274">Совпадения завершаются символами `/` и `.` в пути файла.</span><span class="sxs-lookup"><span data-stu-id="18551-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="18551-275">Совпадает со всем содержимым на разных уровнях каталогов.</span><span class="sxs-lookup"><span data-stu-id="18551-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="18551-276">Может использоваться для рекурсивного сопоставления множества файлов в иерархии каталогов.</span><span class="sxs-lookup"><span data-stu-id="18551-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="18551-277">Следующая таблица содержит типичные примеры стандартных масок.</span><span class="sxs-lookup"><span data-stu-id="18551-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="18551-278">Шаблон</span><span class="sxs-lookup"><span data-stu-id="18551-278">Pattern</span></span>  |<span data-ttu-id="18551-279">Описание</span><span class="sxs-lookup"><span data-stu-id="18551-279">Description</span></span>  |
|---
<span data-ttu-id="18551-280">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-281">'Blazor'</span></span>
- <span data-ttu-id="18551-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-282">'Identity'</span></span>
- <span data-ttu-id="18551-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-284">'Razor'</span></span>
- <span data-ttu-id="18551-285">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-286">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-287">'Blazor'</span></span>
- <span data-ttu-id="18551-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-288">'Identity'</span></span>
- <span data-ttu-id="18551-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-290">'Razor'</span></span>
- <span data-ttu-id="18551-291">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-291">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-292">-----|---
название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-293">'Blazor'</span></span>
- <span data-ttu-id="18551-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-294">'Identity'</span></span>
- <span data-ttu-id="18551-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-296">'Razor'</span></span>
- <span data-ttu-id="18551-297">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-298">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-299">'Blazor'</span></span>
- <span data-ttu-id="18551-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-300">'Identity'</span></span>
- <span data-ttu-id="18551-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-302">'Razor'</span></span>
- <span data-ttu-id="18551-303">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-303">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-304">-----|
|`directory/file.txt`|Соответствует конкретному файлу в заданном каталоге.| |`directory/*.txt`|Соответствует всем файлам с расширением *TXT* в конкретном каталоге.| |`directory/*/appsettings.json`|Соответствует всем файлам *appsettings.json* в каталоге ровно на один уровень ниже каталога *directory*.| |`directory/**/*.txt`|Соответствует всем файлам с расширением *TXT* в каталоге *directory*.|</span><span class="sxs-lookup"><span data-stu-id="18551-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18551-305">ASP.NET Core абстрагирует доступ к файловой системе с помощью поставщиков файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="18551-306">В рамках платформы ASP.NET Core используются поставщики файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="18551-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> предоставляет [корневой каталог содержимого](xref:fundamentals/index#content-root) и [корневой каталог документов](xref:fundamentals/index#web-root) приложения в виде типов `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="18551-308">[ПО промежуточного слоя для статических файлов](xref:fundamentals/static-files) использует поставщики файлов для поиска статических файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="18551-309">[Razor](xref:mvc/views/razor) использует поставщики файлов для поиска страниц и представлений.</span><span class="sxs-lookup"><span data-stu-id="18551-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="18551-310">Инструменты .NET Core используют поставщики файлов и стандартные маски, чтобы указать файлы для публикации.</span><span class="sxs-lookup"><span data-stu-id="18551-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="18551-311">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18551-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="18551-312">Интерфейсы поставщика файлов</span><span class="sxs-lookup"><span data-stu-id="18551-312">File Provider interfaces</span></span>

<span data-ttu-id="18551-313">Основной интерфейс — <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="18551-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="18551-314">`IFileProvider` предоставляет методы для следующих действий:</span><span class="sxs-lookup"><span data-stu-id="18551-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="18551-315">получение сведений о файле (<xref:Microsoft.Extensions.FileProviders.IFileInfo>);</span><span class="sxs-lookup"><span data-stu-id="18551-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="18551-316">получение сведений о каталоге (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>);</span><span class="sxs-lookup"><span data-stu-id="18551-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="18551-317">настройка уведомлений об изменениях (с помощью <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="18551-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="18551-318">`IFileInfo` предоставляет методы и свойства для работы с файлами:</span><span class="sxs-lookup"><span data-stu-id="18551-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="18551-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (в байтах);</span><span class="sxs-lookup"><span data-stu-id="18551-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="18551-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> (дата).</span><span class="sxs-lookup"><span data-stu-id="18551-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="18551-321">Данные из файла можно считывать с помощью метода [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="18551-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="18551-322">Пример приложения демонстрирует, как настроить в `Startup.ConfigureServices` поставщике файлов, чтобы использовать его в приложении путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18551-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="18551-323">Реализации поставщиков файлов</span><span class="sxs-lookup"><span data-stu-id="18551-323">File Provider implementations</span></span>

<span data-ttu-id="18551-324">Доступны три реализации `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="18551-325">Реализация</span><span class="sxs-lookup"><span data-stu-id="18551-325">Implementation</span></span> | <span data-ttu-id="18551-326">Описание</span><span class="sxs-lookup"><span data-stu-id="18551-326">Description</span></span> |
| ---
<span data-ttu-id="18551-327">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-328">'Blazor'</span></span>
- <span data-ttu-id="18551-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-329">'Identity'</span></span>
- <span data-ttu-id="18551-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-331">'Razor'</span></span>
- <span data-ttu-id="18551-332">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-333">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-334">'Blazor'</span></span>
- <span data-ttu-id="18551-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-335">'Identity'</span></span>
- <span data-ttu-id="18551-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-337">'Razor'</span></span>
- <span data-ttu-id="18551-338">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-339">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-340">'Blazor'</span></span>
- <span data-ttu-id="18551-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-341">'Identity'</span></span>
- <span data-ttu-id="18551-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-343">'Razor'</span></span>
- <span data-ttu-id="18551-344">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-345">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-346">'Blazor'</span></span>
- <span data-ttu-id="18551-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-347">'Identity'</span></span>
- <span data-ttu-id="18551-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-349">'Razor'</span></span>
- <span data-ttu-id="18551-350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-351">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-352">'Blazor'</span></span>
- <span data-ttu-id="18551-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-353">'Identity'</span></span>
- <span data-ttu-id="18551-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-355">'Razor'</span></span>
- <span data-ttu-id="18551-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-356">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-357">------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-358">'Blazor'</span></span>
- <span data-ttu-id="18551-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-359">'Identity'</span></span>
- <span data-ttu-id="18551-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-361">'Razor'</span></span>
- <span data-ttu-id="18551-362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-363">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-364">'Blazor'</span></span>
- <span data-ttu-id="18551-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-365">'Identity'</span></span>
- <span data-ttu-id="18551-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-367">'Razor'</span></span>
- <span data-ttu-id="18551-368">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-369">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-370">'Blazor'</span></span>
- <span data-ttu-id="18551-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-371">'Identity'</span></span>
- <span data-ttu-id="18551-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-373">'Razor'</span></span>
- <span data-ttu-id="18551-374">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-374">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | Физический поставщик используется для доступа к физическим файлам системы.</span><span class="sxs-lookup"><span data-stu-id="18551-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="18551-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Поставщик внедренных манифестов используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="18551-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="18551-377">| | [CompositeFileProvider](#compositefileprovider) | Составной поставщик используется для предоставления комбинированного доступа к файлам и каталогам из одного или нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="18551-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="18551-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-378">PhysicalFileProvider</span></span>

<span data-ttu-id="18551-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> предоставляет доступ к физической файловой системе.</span><span class="sxs-lookup"><span data-stu-id="18551-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="18551-380">`PhysicalFileProvider` использует тип <xref:System.IO.File?displayProperty=fullName> (для физического поставщика), ограничивая все пути каталогом и его дочерними элементами.</span><span class="sxs-lookup"><span data-stu-id="18551-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="18551-381">Такая привязка к области защищает от доступа к файловой системе за пределами указанного каталога и его дочерних элементов.</span><span class="sxs-lookup"><span data-stu-id="18551-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="18551-382">Самый распространенный подход к созданию и использованию `PhysicalFileProvider` — запросить `IFileProvider` в конструкторе путем [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18551-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="18551-383">При непосредственном создании экземпляра этого поставщика нужно указать путь к каталогу, который станет базовым путем для всех запросов, выполненных с помощью этого поставщика.</span><span class="sxs-lookup"><span data-stu-id="18551-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="18551-384">Следующий пример кода демонстрирует, как создать и использовать `PhysicalFileProvider` для получения содержимого каталога и сведений о файлах:</span><span class="sxs-lookup"><span data-stu-id="18551-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="18551-385">В предшествующем примере используются следующие типы:</span><span class="sxs-lookup"><span data-stu-id="18551-385">Types in the preceding example:</span></span>

* <span data-ttu-id="18551-386">`provider` является `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="18551-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="18551-387">`contents` является `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="18551-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="18551-388">`fileInfo` является `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="18551-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="18551-389">С помощью поставщика файлов вы можете выполнить итерацию по каталогу, указанному в параметре `applicationRoot`, или вызвать `GetFileInfo` для получения сведений о файлах.</span><span class="sxs-lookup"><span data-stu-id="18551-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="18551-390">Поставщик файлов не имеет доступ к каталогам за пределами `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="18551-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="18551-391">Этот пример приложения создает поставщик для приложения в классе `Startup.ConfigureServices`, используя [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="18551-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="18551-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="18551-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> используется для доступа к файлам, внедренным в сборки.</span><span class="sxs-lookup"><span data-stu-id="18551-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="18551-394">`ManifestEmbeddedFileProvider` использует манифест, скомпилированный в сборку, для воссоздания исходных путей для внедренных файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="18551-395">Чтобы создать манифест для внедренных файлов, задайте для свойства `<GenerateEmbeddedFilesManifest>` значение `true`.</span><span class="sxs-lookup"><span data-stu-id="18551-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="18551-396">Выберите файлы для внедрения с помощью [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="18551-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="18551-397">Используйте [стандартные маски](#glob-patterns) для указания одного или нескольких файлов, которые вы хотите внедрить в сборку.</span><span class="sxs-lookup"><span data-stu-id="18551-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="18551-398">Наш пример приложения создает `ManifestEmbeddedFileProvider` и передает в соответствующий конструктор текущую выполняемую сборку.</span><span class="sxs-lookup"><span data-stu-id="18551-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="18551-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="18551-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="18551-400">Дополнительные перегрузки позволяют сделать следующее:</span><span class="sxs-lookup"><span data-stu-id="18551-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="18551-401">указать относительный путь к файлу;</span><span class="sxs-lookup"><span data-stu-id="18551-401">Specify a relative file path.</span></span>
* <span data-ttu-id="18551-402">ограничить файлы по дате последнего изменения;</span><span class="sxs-lookup"><span data-stu-id="18551-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="18551-403">указать имя внедренного ресурса, содержащего внедренный файл манифеста.</span><span class="sxs-lookup"><span data-stu-id="18551-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="18551-404">Перегрузка</span><span class="sxs-lookup"><span data-stu-id="18551-404">Overload</span></span> | <span data-ttu-id="18551-405">Описание</span><span class="sxs-lookup"><span data-stu-id="18551-405">Description</span></span> |
| ---
<span data-ttu-id="18551-406">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-407">'Blazor'</span></span>
- <span data-ttu-id="18551-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-408">'Identity'</span></span>
- <span data-ttu-id="18551-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-410">'Razor'</span></span>
- <span data-ttu-id="18551-411">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-412">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-413">'Blazor'</span></span>
- <span data-ttu-id="18551-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-414">'Identity'</span></span>
- <span data-ttu-id="18551-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-416">'Razor'</span></span>
- <span data-ttu-id="18551-417">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-417">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-418">---- | --- название: автор: описание:  monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-419">'Blazor'</span></span>
- <span data-ttu-id="18551-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-420">'Identity'</span></span>
- <span data-ttu-id="18551-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-422">'Razor'</span></span>
- <span data-ttu-id="18551-423">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-424">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-425">'Blazor'</span></span>
- <span data-ttu-id="18551-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-426">'Identity'</span></span>
- <span data-ttu-id="18551-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-428">'Razor'</span></span>
- <span data-ttu-id="18551-429">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="18551-430">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="18551-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="18551-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="18551-431">'Blazor'</span></span>
- <span data-ttu-id="18551-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="18551-432">'Identity'</span></span>
- <span data-ttu-id="18551-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="18551-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="18551-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="18551-434">'Razor'</span></span>
- <span data-ttu-id="18551-435">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="18551-435">'SignalR' uid:</span></span> 

<span data-ttu-id="18551-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Принимает необязательный параметр `root` со значением относительного пути.</span><span class="sxs-lookup"><span data-stu-id="18551-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="18551-437">Укажите `root`, чтобы ограничить вызовы к <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> только ресурсами по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="18551-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="18551-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Принимает необязательный параметр `root` со значением относительного пути и параметр `lastModified` со значением даты (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="18551-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="18551-439">Параметр `lastModified` ограничивает дату последнего изменения для экземпляров <xref:Microsoft.Extensions.FileProviders.IFileInfo>, возвращаемых функцией <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="18551-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="18551-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Принимает необязательный параметр `root` со значением относительного пути, дату `lastModified` и параметры `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="18551-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="18551-441">`manifestName` здесь представляет имя встроенного ресурса, содержащего манифест.</span><span class="sxs-lookup"><span data-stu-id="18551-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="18551-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="18551-442">CompositeFileProvider</span></span>

<span data-ttu-id="18551-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> объединяет экземпляры `IFileProvider`, предоставляя единый интерфейс для работы с файлами от нескольких поставщиков.</span><span class="sxs-lookup"><span data-stu-id="18551-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="18551-444">При создании `CompositeFileProvider` передайте в соответствующий конструктор один или несколько экземпляров `IFileProvider`:</span><span class="sxs-lookup"><span data-stu-id="18551-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="18551-445">В нашем примере приложения `PhysicalFileProvider` и `ManifestEmbeddedFileProvider` предоставляют файлы для `CompositeFileProvider` с регистрацией в контейнере служб приложения:</span><span class="sxs-lookup"><span data-stu-id="18551-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="18551-446">Отслеживание изменений</span><span class="sxs-lookup"><span data-stu-id="18551-446">Watch for changes</span></span>

<span data-ttu-id="18551-447">Метод [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) позволяет контролировать изменения в одном или нескольких файлах или каталогах.</span><span class="sxs-lookup"><span data-stu-id="18551-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="18551-448">`Watch` принимает строку пути, которая можно использовать [стандартные маски](#glob-patterns) для указания нескольких файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="18551-449">`Watch` возвращает <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="18551-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="18551-450">Этот токен изменений предоставляет следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="18551-450">The change token exposes:</span></span>

* <span data-ttu-id="18551-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>. свойство, которое можно проверить, чтобы определить, произошло ли изменение.</span><span class="sxs-lookup"><span data-stu-id="18551-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="18551-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>. вызывается при обнаружении изменений по указанной строке пути.</span><span class="sxs-lookup"><span data-stu-id="18551-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="18551-453">Каждый токен изменения выполняет соответствующий обратный вызов только в ответ на отдельное изменение.</span><span class="sxs-lookup"><span data-stu-id="18551-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="18551-454">Чтобы реализовать непрерывный мониторинг, используйте <xref:System.Threading.Tasks.TaskCompletionSource`1>, как показано ниже, или повторно создавайте экземпляры `IChangeToken` в ответ на изменения.</span><span class="sxs-lookup"><span data-stu-id="18551-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="18551-455">В нашем примере консольное приложение *WatchConsole* будет отображать сообщение при изменении текстового файла:</span><span class="sxs-lookup"><span data-stu-id="18551-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="18551-456">Некоторые файловые системы, такие как контейнеры Docker и сетевые папки, не могут надежно отправлять уведомления об изменениях.</span><span class="sxs-lookup"><span data-stu-id="18551-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="18551-457">Задайте для переменной среды `DOTNET_USE_POLLING_FILE_WATCHER` значение `1` или `true`, чтобы опрашивать файловую систему на предмет изменений каждые 4 секунды (это значение нельзя изменить).</span><span class="sxs-lookup"><span data-stu-id="18551-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="18551-458">Стандартные маски</span><span class="sxs-lookup"><span data-stu-id="18551-458">Glob patterns</span></span>

<span data-ttu-id="18551-459">В путях файловой системы используются шаблоны подстановочных знаков, которые называются *стандартными масками*.</span><span class="sxs-lookup"><span data-stu-id="18551-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="18551-460">Эти маски позволяют указывать группы файлов.</span><span class="sxs-lookup"><span data-stu-id="18551-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="18551-461">Поддерживаются два подстановочных знака — `*` и `**`.</span><span class="sxs-lookup"><span data-stu-id="18551-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="18551-462">Совпадает с любым элементом на текущем уровне папок, любым именем или расширением файла.</span><span class="sxs-lookup"><span data-stu-id="18551-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="18551-463">Совпадения завершаются символами `/` и `.` в пути файла.</span><span class="sxs-lookup"><span data-stu-id="18551-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="18551-464">Совпадает со всем содержимым на разных уровнях каталогов.</span><span class="sxs-lookup"><span data-stu-id="18551-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="18551-465">Может использоваться для рекурсивного сопоставления множества файлов в иерархии каталогов.</span><span class="sxs-lookup"><span data-stu-id="18551-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="18551-466">**Примеры стандартных масок**</span><span class="sxs-lookup"><span data-stu-id="18551-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="18551-467">Соответствует конкретному файлу в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="18551-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="18551-468">Соответствует всем файлам с расширением *.txt* в заданном каталоге.</span><span class="sxs-lookup"><span data-stu-id="18551-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="18551-469">Соответствует всем файлам `appsettings.json` в любом каталоге, расположенном ровно на один уровень ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="18551-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="18551-470">Соответствует всем файлам с расширением *.txt*, находящимся на любом уровне ниже каталога *directory*.</span><span class="sxs-lookup"><span data-stu-id="18551-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
